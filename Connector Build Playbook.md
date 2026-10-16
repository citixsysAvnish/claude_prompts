# iVendNext Connector Build Playbook
iVendNext Connector Build Playbook
Every step to build a new iVendNext connector app from scratch, using the ivendnext_connector_template (v2.0.0.23) and ivendnext_integration_core (contract v2). Each step names the file it touches, the rule behind it, and how to verify it worked.

## Step 1: Before You Start
Make three decisions first. They are hard to change once code exists.

### 1.1. Pick the Other System and the Transport
REST, SOAP, a file drop or a DB link. Core does not care, but your client.py is shaped around it.

### 1.2. Pick the Entities and Their Direction
Inbound-only is the easiest first cut. A bidirectional entity needs a push pipeline (Step 8) from day one.

### 1.3. Check Whether Any Entity Carries Money
If yes, read the Sage X3 guide's money-entity checklist now. It changes the shape of connector.py from the first commit.

### 1.4. Access You Will Need
Read access to the ivendnext GitHub org (template and Core repos; Core is private).

A PR route into github.com/ivendnext/.github (needed in Step 2).

Test credentials for the other system, kept out of the repo. Never type them on a command line that lands in shell history.

## Step 2: Reserve Your Names First
Frappe names (DocTypes, modules, custom-field prefixes) are global per site. A clash shows up later, when one app's uninstall deletes another app's data.

Open one PR to:

👉 https://github.com/ivendnext/.github

Add a row to namespace/NAMESPACE_REGISTRY.tsv with app name, repo, module name and DocType prefix. Leave the custom-field-prefix column as - for now (reserve it in Step 10).

Validate the row before you open the PR:

    python3 namespace/check_namespace.py --registry namespace/NAMESPACE_REGISTRY.tsv --lint-registry

Verify: the lint prints "registry lint passed".

## Step 3: Clone the Newest Template Tag and Rename It
A clone of an older tag inherits every defect the template has since fixed. Read the template's CHANGELOG.md before you pick a tag.

Use bash, not zsh (the rename uses perl -pi -e).

    git clone --branch v2.0.0.23 https://github.com/ivendnext/ivendnext_connector_template.git iVendNext_[Platform]
    cd iVendNext_[Platform]
    rm -rf .git
    mv docs ../connector-docs

Set the name shapes:

    P=[snake_name]; Pc="[Display Name]"; Pk=[ClassForm]

Example

    P=shopify; Pc="Shopify"; Pk=Shopify

### 3.1. Rename Folders and Files

    mv ivendnext_connector_template "ivendnext_$P"
    mv "ivendnext_$P/ivendnext_connector_template" "ivendnext_$P/ivendnext_$P"
    mv "ivendnext_$P/ivendnext_$P/doctype/ivendnext_connector_template_setting" "ivendnext_$P/ivendnext_$P/doctype/ivendnext_${P}_setting"
    for e in json py js; do
      mv "ivendnext_$P/ivendnext_$P/doctype/ivendnext_${P}_setting/ivendnext_connector_template_setting.$e" "ivendnext_$P/ivendnext_$P/doctype/ivendnext_${P}_setting/ivendnext_${P}_setting.$e"
    done

### 3.2. Replace the Name Shapes in Every File

    grep -rl iVendNextConnectorTemplateSetting . | xargs perl -pi -e "s/iVendNextConnectorTemplateSetting/iVendNext${Pk}Setting/g"
    grep -rl connector_template . | xargs perl -pi -e "s/connector_template/$P/g"
    grep -rl "Connector Template" . | xargs perl -pi -e "s/Connector Template/$Pc/g"
    grep -rl TemplateConnector . | xargs perl -pi -e "s/TemplateConnector/${Pk}Connector/g"
    mv ../connector-docs docs

Why more than one name shape: Frappe finds a DocType's Python class by removing the spaces from the DocType name. A name with a space (e.g. "Sage X3") needs a separate space-free spelling for class names.

### 3.3. Verify

    grep -rl "connector_template\|Connector Template\|TemplateConnector\|ConnectorTemplate" ivendnext_$P ci/

This must print nothing.

## Step 4: Set the Version Constants and Wire hooks.py
Core refuses registration on these before it reads a single entity declaration.

### 4.1. [app]/\_\_init\_\_.py

    __version__ = "2.0.0"
    try:
        from ._build import BUILD
    except ImportError:
        BUILD = 0
    __version__ = f"{__version__}.{BUILD}"

    CONTRACT_VERSION = 2            # must be in Core's SUPPORTED_CONTRACT_VERSIONS
    TESTED_WITH_CORE = "v[tag]"     # the Core build you tested against
    TEMPLATE_ORIGIN = "v2.0.0.23"   # never edit by hand after this

Never copy CONTRACT_VERSION from a sibling connector. Read Core's supported set first. A sibling once shipped 1 against a Core that supported only (2,) and was red from day one.

### 4.2. hooks.py

    required_apps = ["erpnext", "[engine app]"]

    integration_core_connectors = {"[platform]": "[app].connector.[Class]Connector"}
    integration_core_retry_method = {"[platform]": "[app].connector.retry"}
    integration_core_settings = {
        "[platform]": {"doctype": "[Setting DocType]", "label": "[Display Name]"}
    }

integration_core_retry_method is required even for an inbound-only connector. Without it, Retry has no entry point and throws.

Ship no Workspace, no card code, no after_install or before_uninstall.

### 4.3. Verify

    bench --site [site] run-tests --module [app].tests.test_scaffold

A fresh copy of v2.0.0.23 reads 14 tests, OK.

## Step 5: Build the Setting DocType and the Credential Reader
Every far-side call reads through exactly one function. One place proves a connection, and one place pauses it when something important changes underneath it.

### 5.1. One Row per Connection
Use autoname: "field:connection_name" and allow_rename: 0. Renaming silently breaks Core's Open setup route.

### 5.2. Three Permission Tiers
Permlevel 0 for identity and required fields. Permlevel 1 for every secret and the far side's address (System Manager only). Permlevel 2 for fields the system writes.

### 5.3. Write credentials.py
Write one function, connection_credentials(doc, *, proving=False). Return a dict bound to a local named auth. Frappe's Error Log and common secret scrubbers match on that exact name.

### 5.4. Classify Every Data Field
Put each field in exactly one of ADDRESS_FIELD, SIGN_IN_FIELDS, ROUTING_FIELDS, NOT_ROUTING_FIELDS or SYSTEM_FIELDS.

A field that could, by itself, make a different far-side account answer goes in ROUTING_FIELDS. Changing it pauses every registered connection until Test Connection runs again.

### 5.5. Compute and Check a Connection Proof
SHA-256 over address, sign-in, every routing field and the far-side identity. Never include the password.

Check it on every far-side call, not only in validate(). A direct frappe.db.set_value, a patch or a restore all skip validate, but none of them skips the reader.

### 5.6. No Account-Identity Field? (Optional)
(Optional, Only required if the far side's API returns no account-identity field)
Do not force the template's default _probe() design. Make far_side_identity an operator-typed required field, and say so in your module docstring.

### 5.7. Verify

    bench --site [site] run-tests --module [app].tests.test_credentials

test_every_field_is_classified_exactly_once must pass after every field you add, in the same commit as the JSON change.

## Step 6: Build the Transport — One File Owns the Socket
Only [platform]/client.py imports an HTTP (or SOAP) client. Every other file, including every mapper, talks to the far side through it.

### 6.1. TLS Verification
Never False. Use a path to a trusted CA bundle, or True. No toggle to turn it off.

### 6.2. Pagination and Incremental Reads
Build them from what you measure against the far side. Do not copy a sibling's overlap-seconds number or encoding fix without re-measuring.

### 6.3. classify_transport(exc) -> str
A fail-closed three-way decision: landed, never sent or unknown. Only a connection provably never established is "never sent". Everything ambiguous is "unknown", which Core stores as Unverified.

### 6.4. Verify

    grep -rn "^import requests\|^from requests" [app] --include=*.py

This must match only client.py and its own test file.

## Step 7: Write Your First Entity Mapper
One module, one entity, one shape that every mapper follows. A reviewer who knows one mapper can review all of them.

### 7.1. Before You Write It
Confirm the entity string is already in Core's ENTITY_VOCABULARY["erp"] or ["ecom"] at the Core build you pin. If it is not, request it in writing. Never invent one or borrow a neighbour's.

Read the same far-side record twice. Confirm its id does not change, and check whether its modification timestamp moves on every field edit. It may not. One connector found a description field changing with no timestamp movement, which changed how its fingerprint had to be built.

### 7.2. Module Contract (Every Name, No Exceptions)
ENTITY, ENTITY_SCHEME_VERSION, FINGERPRINT_VERSION: the Core string. Bump the scheme version only if the id derivation changes. Bump the fingerprint version only if the mapped field set changes.

DOCTYPE, RESOURCE, TIMESTAMP_FIELD, COMPANY_SCOPED: the engine DocType, the far-side resource, the field that drives incremental reads, and whether a company filter applies.

external_id, human_key, far_side_timestamp: pure functions over a raw body.

_mapped(body): the one place far-side field names become yours. Only fields with a real target belong here. A field with nowhere to land must never be in the fingerprint either.

fingerprint(body): SHA-256 of _mapped() plus the far-side timestamp. The stamp closes the revert gap: a value going A → B → A must mint a fresh key.

blocked_reason(connector, body): a sentence an operator can act on when the record must wait, else None. Call it even while it always returns None.

apply(connector, body, attempt, *, far_side_at=None): the 9-step order in 7.3.

### 7.3. The 9-Step apply() Order — Do Not Reorder
1 — Refuse a body with no far-side identity. Guessing one is worse than refusing.

2 — Ask the precondition. Return {"status": BLOCKED, "blocked_reason": ...}. Waiting is never failing.

3 — Supersession: record.is_superseded(...) returns SUPERSEDED. On a poll that re-reads its overlap window, out-of-order arrival is normal.

4 — Resolve identity through Core's map, then adopt an existing document by natural key, then create. The identity map is the idempotency, never a name guess.

5 — Write only the fields the ownership split gives you.

6 — (Money entities only) Ask the engine its own question before the save, for the one field you are flipping. Never a bare try/except around doc.save().

7 — doc.save(), always, including on an update with nothing new. Skipping it is the most common first-cut bug: the day you add a mapped field, every synced document silently stops updating.

8 — record.map_identity(...) after the document, in the same transaction.

9 — Return {"status": APPLIED, "external_ref": ..., "request": {flat scalars only}}.

### 7.4. Verify
Every new test must be seen to fail once before its fix lands. Read the counts from the output; bench run-tests exits 0 even on a failed run.

## Step 8: Build the Push Pipeline (Optional)
(Optional, Only required if the entity is outbound or bidirectional)
Skip this step for an inbound-only first cut.

### 8.1. Two Triggers
A fast path on the engine DocType's save hook with enqueue_after_commit=True, so it never fires on a rolled-back save.

A scheduled sweep that is the real correctness path. It recovers anything the fast path missed (worker crash, site restart, sync switched on after the record existed).

### 8.2. Scope and Readiness
Check before open_attempt (which commits on entry): does the record predate the sweep's seeded cursor, and does it have everything the far side requires to create?

### 8.3. Idempotency Key Includes the Content Fingerprint
An unchanged re-sweep opens no new attempt. A real change always mints one.

### 8.4. Mint-Then-Write
If the far side needs a code you generate, commit that code before any far-side call. Do a read-before-write collision check. An unrecognised answer on that read is indeterminate, never a green light.

### 8.5. Outcome Mapping
Confirmed success is Applied. An ambiguous reply (timeout, fault, a success reply you cannot fully trust) is Unverified. Never silently Applied, never re-sent automatically.

### 8.6. If You Do Not Build poll_status
An Unverified row then has no automated way out. That is acceptable for a non-money first cut, but say so in your docs.

## Step 9: Wire the Entity into connector.py

    # ENTITIES rows: (entity, direction, driven_by, source_of_truth, carries_money, entity_scheme_version)
    ENTITIES = (
        (my_mod.ENTITY, "Inbound", "Watermark", "Theirs", False, my_mod.ENTITY_SCHEME_VERSION),
    )

    INBOUND_MODULES = {my_mod.ENTITY: my_mod}
    PAYLOAD_ALLOW = [..., "code", "description", "document", "created"]

### 9.1. Declare Each Entity Once
Core reads carries_money across every row naming that entity. A second row with a different flag arms the money machinery in the other direction too.

### 9.2. Fill PAYLOAD_ALLOW
Every key a mapper returns in its request dict must be in PAYLOAD_ALLOW, or it is stored masked.

### 9.3. Leave Conditional Methods Commented Out
poll_status, induce, cancel and validate stay commented out, not defined as stubs that raise. Core detects them by method identity, so a raise NotImplementedError stub would falsely pass the refusal meant to stop you shipping without them.

### 9.4. Add a Re-Register Patch Every Time ENTITIES Changes
Core reads the stored child rows on each registered Connector, not the live ENTITIES tuple. Only register() writes them.

    # patches.txt
    [post_model_sync]
    [app].patches.v0_N.reregister_declared_connections

Never use after_migrate for this. Patches run in the schema-sync phase, before Core's own declaration check.

### 9.5. Verify

    bench --site [site] run-tests --module [app].tests.test_registration

Run it against a real Core, with the refusals actually made to fire (unsupported contract version, missing company, out-of-vocabulary entity).

## Step 10: Add a custom_[yours]_ Field (Optional)
(Optional, Only required if the engine DocType truly has nowhere to put a value)
Common when the far side sends a description or long-name field the engine DocType does not carry.

### 10.1. Reserve the Field Prefix
Confirm your prefix is reserved in the namespace registry (Step 2). Never bare custom_, which every app in the org shares.

### 10.2. Add the Field JSON
Path: [app]/custom_fields/[doctype_snake].json

    {"[DocType Name]": [{
        "fieldname": "custom_[yours]_description",
        "fieldtype": "Data",
        "insert_after": "[a real rendered field, never the title/autoname field]",
        "read_only": 1,
        "module": "[Your Module]"
    }]}

### 10.3. Load It from after_migrate
Wire hooks.py after_migrate to a function that loads every file in that folder through create_custom_fields(..., ignore_validate=True, update=True). Wrap it so it can never raise (Step 11).

### 10.4. Map, Write and Bump
Map the field in _mapped(), write it in apply() unconditionally (step 7 of 7.3), and bump FINGERPRINT_VERSION in the same commit.

### 10.5. Verify
Read the field back from the database after apply(), not from the return value.

## Step 11: Wire a Migrate Hook That Can Never Take the Site Down
Frappe runs every installed app's after_migrate hook in one loop, in one transaction, with no per-hook try/except. If yours raises, later apps' hooks never run, the phase rolls back, and migrate fails for every app on the site.

    def after_migrate() -> None:
        _sync_custom_fields()

    @_never_raise   # a named savepoint; rolls back only this app's statements on failure
    def _sync_custom_fields() -> None:
        ...

A bare frappe.db.rollback() inside your hook is worse than the bug it guards against. It discards uncommitted changes from every app that ran before yours.

### 11.1 Verify
In a test environment, make the wrapped function raise once and confirm migrate still completes for every app.

## Step 12: Wire the Scheduler

### 12.1. hooks.py

    scheduler_events = {
        "cron": {"*/5 * * * *": ["[app].controllers.drive.[platform]_every_five_minutes"]},
        "hourly": ["[app].controllers.drive.[platform]_hourly"],
    }

Name the methods [platform]_every_five_minutes and [platform]_hourly, never the bare template names. Frappe names a Scheduled Job Type by the method's last two dotted parts. A bare drive.hourly is one shared row across every connector on the site, and the app loaded later silently deletes the other's row on every migrate. This has been observed on this bench.

### 12.2. The Pass Shape (controllers/drive.py)

    def run_pass(adapter, run):
        for entity, *_ in adapter.ENTITIES:
            _poll_entity(adapter, run, entity)   # read watermark -> page -> record_arrival -> apply -> advance
        sweep_ours(adapter, run)                  # outbound catch-up, if you built Step 8
        frappe.db.commit()
        return run

### 12.3. Watermark
Advance it only after a complete walk, to the newest far-side stamp actually recorded, never to the last row of a page. Name any still-open attempt as skipped_attempt, or Core's watermark function advances past it.

### 12.4. Heartbeat
Write record.note_drive_ran in a finally block, unconditionally. Otherwise Core cannot tell "no work to do" from "never runs".

### 12.5. Cadence
Plan your cadence_due / last_pass_at writer before you ship. A gate that always returns True because nothing stamps the last-run time makes every connection poll on every tick.

### 12.6. Retry and Blocked Drain
Build lifecycle.due_attempts and blocked_due in the same release as your first Blocked precondition. Without it, an unchanged blocked or retrying row is only revisited by a manual Retry or a far-side change.

## Step 13: Tests, Gate and CI

### 13.1. Per-Mapper Tests
Pure-function tests: external_id, fingerprint changes with the stamp, fingerprint changes with each mapped field.

Real-Core tests: apply() creates then updates, an older stamp supersedes, a missing dependency blocks. Read the written field back from the database.

### 13.2. ci/[platform]_gate.py
Model it on a sibling's. It calls run_tests_for_module and reads .wasSuccessful() directly, because bench run-tests exits 0 even on failure. A module fails the gate if it is unsuccessful, skips anything, or runs zero tests.

### 13.3. ci/lints.manifest
At minimum, a TLS-never-off check and a no-committed-credentials check. Arm the second from environment variables read from a gitignored credentials file, never from a literal in a tracked file.

### 13.4. Verify
Every new test file is added to the gate's allow-list in the same commit, or the PR says why it is left out.

## Step 14: Final Step — Keep the Copy Upgradeable and Land Through Review

### 14.1. The template-base Branch
You started your own history, so git pull cannot bring later template fixes in. Save upgrade_template.sh (from docs/UPGRADING_YOUR_COPY.md in the newest tag you can reach) outside your repo, and run it at every upgrade:

    bash upgrade_template.sh [repo dir] [new tag] [snake_name] "[Display Name]" [ClassForm]

It maintains a template-base branch (the template, renamed to your names, at your current tag), so each upgrade merge only asks about files both you and the template changed. Merge every upgrade with a real merge commit, never squash or rebase.

### 14.2. Land the Work
Branch from the team's integration branch, open a PR back into it, and get the gate green at the exact pushed commit.

Cite code as path::function in commit messages and docs, never a line number.

Any entity that carries money needs two independent reviewers and a human before merge, every time.

Your connector is now ready to register with Integration Core. Re-read the cited template docs (GETTING_STARTED, BUILD_A_CONNECTOR, MAPPER_TEMPLATE, CODING_GUIDELINES, UPGRADING_YOUR_COPY at v2.0.0.23) before building on a specific mechanism.
