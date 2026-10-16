# Connector Build Playbook

Every step to build a new iVendNext connector app from scratch, using the `ivendnext_connector_template` and `ivendnext_integration_core`. Fourteen phases, in order — each one names the file it touches, the rule behind it, and how to prove it actually worked. Grounded in the template's own `docs/` (`GETTING_STARTED.md`, `BUILD_A_CONNECTOR.md`, `MAPPER_TEMPLATE.md`, `CODING_GUIDELINES.md`) and in `ivendnext_sage_x3`, the one connector on this bench that has walked this path.

Template v2.0.0.23 Core contract v2 erp / ecom class

## 0 Before you start

Three decisions you can't walk back cheaply once code exists.

### Decide

- **The other system and the transport.** REST, SOAP, a file drop, a DB link — Core doesn't care, but your `client.py` will be shaped around it.
- **Which entities you'll sync, and in which direction.** Inbound-only is the easiest first cut. A bidirectional entity needs a push pipeline (Phase 7) from day one.
- **Whether any entity carries money.** If yes, read Chapter 11 of the Sage X3 guide's "what a money entity needs before it can merge" checklist now, not after you've built the easy 90%. It changes `connector.py`'s shape from the first commit.

### Access you'll need

- Read access to the `ivendnext` GitHub org (the template and Core repos; Core's repo is private).
- A PR route into `github.com/ivendnext/.github` — Phase 1 needs it immediately.
- Test credentials for the other system, kept out of the repo from day one. Never type them into a command line that lands in shell history.

## 1 Reserve your names first

Frappe names (DocTypes, modules, custom-field prefixes) are global per site. A clash surfaces only later, when one app's uninstall deletes another's data.

### Do

Open **one** PR to `github.com/ivendnext/.github` adding a row to `namespace/NAMESPACE_REGISTRY.tsv`: app name, repo, module name, DocType prefix. Leave the custom-field-prefix column `-` for now — reserve it in Phase 6, the first time you actually need a `custom_<yours>_` field.

```
# namespace/check_namespace.py validates the row and checks nothing is reserved twice
python3 namespace/check_namespace.py --registry namespace/NAMESPACE_REGISTRY.tsv --lint-registry
```

**Verify** the lint prints `registry lint passed` before you open the PR, not after review comments.


## 2 Clone the newest template tag and rename it

A clone of an older tag inherits every defect the template has since fixed. Read the template's `CHANGELOG.md` for what's fix-bearing before you pick a tag.

### Do (bash, not zsh — the rename uses `perl -pi -e`)

```
git clone --branch v2.0.0.23 https://github.com/ivendnext/ivendnext_connector_template.git iVendNext_<Platform>
cd iVendNext_<Platform>
rm -rf .git                         # start your own history
mv docs ../connector-docs           # keep docs out of the rename, bring them back untouched after

P=<snake_name>; Pc="<Display Name>"; Pk=<ClassForm>   # e.g. P=shopify  Pc=Shopify  Pk=Shopify

mv ivendnext_connector_template "ivendnext_$P"
mv "ivendnext_$P/ivendnext_connector_template" "ivendnext_$P/ivendnext_$P"
mv "ivendnext_$P/ivendnext_$P/doctype/ivendnext_connector_template_setting" \
   "ivendnext_$P/ivendnext_$P/doctype/ivendnext_${P}_setting"
for e in json py js; do
  mv "ivendnext_$P/ivendnext_$P/doctype/ivendnext_${P}_setting/ivendnext_connector_template_setting.$e" \
     "ivendnext_$P/ivendnext_$P/doctype/ivendnext_${P}_setting/ivendnext_${P}_setting.$e"
done

# the four name shapes, in every file
grep -rl iVendNextConnectorTemplateSetting . | xargs perl -pi -e "s/iVendNextConnectorTemplateSetting/iVendNext${Pk}Setting/g"
grep -rl connector_template .          | xargs perl -pi -e "s/connector_template/$P/g"
grep -rl "Connector Template" .        | xargs perl -pi -e "s/Connector Template/$Pc/g"
grep -rl TemplateConnector .           | xargs perl -pi -e "s/TemplateConnector/${Pk}Connector/g"
mv ../connector-docs docs
```

Why three name shapes, not one: Frappe finds a DocType's Python class by taking the DocType name and removing its spaces. `iVendNext <Platform> Setting` needs the class `iVendNext<ClassForm>Setting` — a name with a space (e.g. "Sage X3") needs a fourth, space-free spelling for class names specifically.

**Verify** `grep -rl "connector_template\|Connector Template\|TemplateConnector\|ConnectorTemplate" ivendnext_$P ci/` prints nothing.


## 3 Set the four version constants and wire `hooks.py`

Core refuses registration on these before it reads a single entity declaration.

### `<app>/__init__.py`

```
__version__ = "2.0.0"
try:
    from ._build import BUILD
except ImportError:
    BUILD = 0
__version__ = f"{__version__}.{BUILD}"

CONTRACT_VERSION = 2       # Core's SUPPORTED_CONTRACT_VERSIONS
TESTED_WITH_CORE = "v"  # the Core build you tested against
TEMPLATE_ORIGIN = "v2.0.0.23"  # never edit by hand after this
```

Never copy `CONTRACT_VERSION` from a sibling connector — read Core's own supported set first. A sibling's first commit once shipped `1` against a Core that supported only `(2,)`, and was red from day one.

### `hooks.py`

```
required_apps = ["erpnext", ""]

integration_core_connectors = {"": ".connector.Connector"}
integration_core_retry_method = {"": ".connector.retry"}
integration_core_settings = {
    "": {"doctype": "", "label": ""}
}
```

`integration_core_retry_method` is required even for an inbound-only connector — without it, Retry has no entry point and throws. Ship no Workspace, no card code, no `after_install`/`before_uninstall`.

**Verify** `bench --site <site> run-tests --module <app>.tests.test_scaffold` — a fresh copy of v2.0.0.23 should read 14 tests, OK, before you've written anything else.


## 4 Build the Setting DocType and the one credential reader

Every far-side call in the connector reads through exactly one function, so one place proves a connection and one place pauses it when something load-bearing changes underneath it.

### Do

1. One row per connection (`autoname: "field:connection_name"`, `allow_rename: 0` — renaming breaks Core's *Open setup* route silently).
2. Three permission tiers: permlevel 0 for identity/required fields, permlevel 1 for every secret and the far side's address (System Manager only), permlevel 2 for fields the system writes.
3. Write `credentials.py`'s single `connection_credentials(doc, *, proving=False)`, returning a dict bound to a local literally named `auth` — Frappe's Error Log and common secret-scrubbers match on that exact name.
4. Classify every data field into `ADDRESS_FIELD` / `SIGN_IN_FIELDS` / `ROUTING_FIELDS` / `NOT_ROUTING_FIELDS` / `SYSTEM_FIELDS`. A field that could, by itself, make a *different* far-side account answer goes in `ROUTING_FIELDS` — changing it pauses every registered connection until Test Connection runs again.
5. Compute a connection proof (SHA-256 over address, sign-in, every routing field, and the far-side identity — never the password) and check it on every far-side call, not just in `validate()`. A direct `frappe.db.set_value`, a patch, or a restore all skip `validate`, but none skips the reader.

**If the far side's API gives you no account-identity field in any response** (confirmed true for at least one connector on this bench), don't force the template's default `_probe()` design. Make `far_side_identity` an operator-typed, required field instead, and say so plainly in your module docstring — it's a legitimate, documented divergence, not a shortcut.

**Verify** `tests/test_credentials.py::test_every_field_is_classified_exactly_once` passes after every field you add, in the same commit as the JSON change.


## 5 Build the transport — one file owns the socket

Security rule: only `<platform>/client.py` imports an HTTP (or SOAP) client. Every other file, including every mapper, talks to the far side only through it.

### `<platform>/client.py` needs

- TLS verification that is never `False` — a path to a trusted-CA bundle, or `True`. No toggle to turn it off, ever.
- A pagination/incremental-read primitive, built from what you actually measure against the far side — don't copy a sibling connector's overlap-seconds number or encoding fix without re-measuring; cite your own evidence.
- `classify_transport(exc) -> str`: a fail-closed three-way decision (*landed* / *never sent* / *unknown*). Only a connection provably never established is "never sent" — everything ambiguous defaults to "unknown," which Core stores as `Unverified`, never silently as a clean failure.

**Verify** `grep -rn "^import requests\|^from requests" <app> --include=*.py` matches only `client.py` and its own test file.


## 6 Write your first entity mapper

One module, one entity, one shape every mapper in the connector follows — so a reviewer who knows one mapper can review all of them.

### Before you write it

- Confirm the entity string is already in Core's class vocabulary (`ENTITY_VOCABULARY["erp"]` or `["ecom"]`) at the Core build you pin. If it isn't, request it in writing before you wire anything — never invent one or borrow a neighbor's.
- Read the same far-side record twice. Confirm its id doesn't change, and check whether its modification-timestamp field actually moves on *every* field edit — it may not. One connector on this bench confirmed a description field changing with no timestamp movement at all; that single fact changed how its fingerprint had to be built.

### Module contract (every name, no exceptions)

| `ENTITY`, `ENTITY_SCHEME_VERSION`, `FINGERPRINT_VERSION` | the Core string; bump the scheme version only if how you derive the id changes; bump the fingerprint version only if the *mapped field set* changes — never per payload |
| --- | --- |
| `DOCTYPE`, `RESOURCE`, `TIMESTAMP_FIELD`, `COMPANY_SCOPED` | the engine DocType, the far-side resource, which field drives incremental reads, whether a company filter applies |
| `external_id`, `human_key`, `far_side_timestamp` | pure functions over a raw body |
| `_mapped(body)` | the **one place** far-side field names become yours. Only fields with a real target belong here — a field with nowhere to land must never appear in the fingerprint either, or an edit to it alone triggers a wasted re-apply |
| `fingerprint(body)` | SHA-256 of `_mapped()` **plus** the far-side timestamp. The stamp closes the revert gap: a value that goes A → B → A must mint a fresh key, not be read as a repeat of A |
| `blocked_reason(connector, body)` | a sentence an operator can act on when the record must wait, else `None`. Call this even while it always returns `None` today — skipping the call is exactly the gap that lets a real future dependency crash live instead of blocking cleanly |
| `apply(connector, body, attempt, *, far_side_at=None)` | the 9-step order below, every time |

### The 9-step `apply()` order — do not reorder

1. Refuse a body with no far-side identity (guessing one is worse than refusing).
2. Ask the precondition → `{"status": BLOCKED, "blocked_reason": …}`. Waiting is never failing.
3. **Supersession, third.** `record.is_superseded(...)` → `SUPERSEDED`. On a poll that re-reads its overlap window every cycle, an out-of-order arrival is the normal case, not an edge case.
4. Resolve identity through Core's map, then adopt an existing document by natural key, then create. The identity map *is* the idempotency — never a name guess.
5. Write only the fields the ownership split gives you.
6. (Money entities only) ask the engine its own question, before the save, for the one field you're flipping — never a bare `try/except` around `doc.save()`, which turns 28 validate steps into one opaque reason.
7. `doc.save()` — **always**, including on an update with nothing new to create. Skipping it when there's "nothing to update yet" is the single most common first-cut bug: the day you add one more mapped field, every already-synced document silently stops updating.
8. `record.map_identity(...)` **after** the document, in the same transaction — a row written first would claim a document the save then rejected.
9. Return `{"status": APPLIED, "external_ref": …, "request": {flat scalars only}}`.

**Verify** every new test was seen to fail once before its fix landed. Read the counts from the output — `bench run-tests` exits 0 even on a failed run.

## 7 If the entity is outbound or bidirectional: build the push pipeline

Skip this phase entirely for an inbound-only connector's first cut.

### Shape (from the one worked bidirectional example on this bench)

1. **Two triggers.** A fast path on the engine doctype's own save hook (`enqueue_after_commit=True`, so it never fires on a rolled-back save), and a scheduled sweep that is the actual correctness path — it recovers anything the fast path missed (a worker crash, a site restart, the sync being switched on after the record already existed).
2. **Scope and readiness** checked before `open_attempt` (which commits on entry): does this record predate the sweep's own seeded cursor, and does it have everything the far side requires to create?
3. **Idempotency key includes the content fingerprint**, so an unchanged re-sweep opens no new attempt, and a real change always mints one.
4. **Mint-then-write**: if the far side needs a code you generate, commit that code *before* any far-side call, and do a read-before-write collision check — an unrecognized answer on that read is always treated as indeterminate, never as a green light.
5. **Outcome mapping** to Core's vocabulary: a confirmed success is `Applied`; an ambiguous reply (timeout, fault, a success reply you can't fully trust) is `Unverified` — never silently `Applied`, and never re-sent automatically.

**If you don't build `poll_status`:** an `Unverified` row has no automated way out, ever. That's acceptable for a first cut of a non-money entity — a human resolves it by reading the far side — but say so explicitly in your docs rather than leaving an operator to discover it.


## 8 Wire the entity into `connector.py`

### Do

```
# ENTITIES is a tuple of six-member rows:
# (entity, direction, driven_by, source_of_truth, carries_money, entity_scheme_version)
ENTITIES = (
    (my_mod.ENTITY, "Inbound", "Watermark", "Theirs", False, my_mod.ENTITY_SCHEME_VERSION),
)

INBOUND_MODULES = {my_mod.ENTITY: my_mod}
PAYLOAD_ALLOW = [..., "code", "description", "document", "created"]  # every request key, flat scalars only
```

- Declare each entity **once**. Core reads `carries_money` across every row naming that entity, so a second row with a different flag arms the money machinery in the other direction too.
- Every key a mapper returns in its `request` dict must be in `PAYLOAD_ALLOW` or it is stored masked — a cosmetic but real gap if you forget it.
- Leave every conditional method (`poll_status`, `induce`, `cancel`, `validate`) **commented out**, not defined as a stub that raises. Core detects whether you've built them by method identity — a `raise NotImplementedError` stub would falsely pass the refusal that exists specifically to stop you shipping without them.

### Add a re-register patch — every time `ENTITIES` changes

Core's amount-echo and entity-handling logic reads the **stored** child rows on each registered Connector, not the live `ENTITIES` tuple — only `register()` writes them. A release that adds or re-classifies an entity ships one new patch row:

```
# patches.txt
[post_model_sync]
.patches.v0_N.reregister_declared_connections
```

**Never** use `after_migrate` for this — patches run in the earlier schema-sync phase, before Core's own declaration check, with no ordering luck `after_migrate` can offer.

**Verify** `tests/test_registration.py` passes against a real Core, with the refusals actually made to fire (an unsupported contract version, a missing company, an out-of-vocabulary entity).

## 9 Add a `custom_<yours>_` field, only when the engine truly has nowhere to put a value

Common the moment your far side sends a description or long-name field the engine DocType simply doesn't carry.

### Do, in order

1. Confirm your field prefix is actually reserved in the namespace registry (Phase 1) — never bare `custom_`, which every app in the org shares and which namespace tooling can't protect.
2. Add `<app>/custom_fields/<doctype_snake>.json`: `{"<DocType Name>": [{"fieldname": "custom_<yours>_description", "fieldtype": "Data", "insert_after": "<a real rendered field, never the title/autoname field>", "read_only": 1, "module": "<Your Module>"}]}`.
3. Wire `hooks.py::after_migrate` to a function that loads every file in that folder through `create_custom_fields(..., ignore_validate=True, update=True)` — wrapped so it can never raise (next phase explains why that's not optional).
4. Map the field in `_mapped()`, write it in `apply()` **unconditionally** (step 7 of Phase 6 — the save must happen on update too), and bump `FINGERPRINT_VERSION` in the same commit.

**Verify** by reading the field back from the database after `apply()`, not from the function's return value — a test that only checks the return dict would have missed exactly the "skipped save on update" bug this phase exists to prevent.

## 10 Wire a migrate hook that can never take the whole site down

Frappe runs every installed app's `after_migrate` hook in one plain loop, inside one database transaction, with **no per-hook try/except**. If yours raises, every later app's hook never runs, the whole phase rolls back, and the migrate fails for every app on the site — not just yours.

### Do

```
def after_migrate() -> None:
    _sync_custom_fields()

@_never_raise   # a named savepoint; rolls back ONLY this app's statements on failure
def _sync_custom_fields() -> None:
    ...
```

A bare `frappe.db.rollback()` inside your own hook is worse than the bug it's guarding against — it discards every uncommitted change from every app that ran before yours in the same loop.

**Verify** by deliberately making the wrapped function raise once in a test environment and confirming the migrate still completes for every app.

## 11 Wire the scheduler

### `hooks.py`

```
scheduler_events = {
    "cron": {"*/5 * * * *": [".controllers.drive._every_five_minutes"]},
    "hourly": [".controllers.drive._hourly"],
}
```

**Name the methods `<platform>_every_five_minutes` / `<platform>_hourly` — never the bare template names.** Frappe names a Scheduled Job Type by a method's *last two dotted parts*. A bare `drive.hourly` is one shared row across every connector on the same site built from this template, and the app that happens to load later in the site's hook order silently deletes the other's row on every migrate. This has actually been observed on this bench: one connector's hourly health job has had no Scheduled Job Type row at all, with no error anywhere, because a sibling app's bare name took it.

### The pass shape (`controllers/drive.py`)

```
def run_pass(adapter, run):
    for entity, *_ in adapter.ENTITIES:
        _poll_entity(adapter, run, entity)   # read watermark -> page -> record_arrival -> apply -> advance
    sweep_ours(adapter, run)                  # outbound catch-up, if you built Phase 7
    frappe.db.commit()
    return run
```

- Advance the watermark only after a **complete** walk, to the newest far-side stamp *actually recorded* — never to the last row of a page. Name any still-open attempt as `skipped_attempt`, or Core's own watermark function advances blindly past it.
- Write the heartbeat (`record.note_drive_ran`) in a `finally`, unconditionally — Core cannot otherwise tell "no work to do" from "this connector never runs at all."
- Plan your `cadence_due`/`last_pass_at` writer *before* you ship, not after. A gate that always returns `True` because nothing ever stamps the last-run time means every connection polls on every tick regardless of its configured cadence — an easy gap to ship by accident.
- Build the retry/blocked drain (`lifecycle.due_attempts`, `blocked_due`) in the same release that ships your first `Blocked` precondition. Without it, an unchanged blocked or retrying row is never re-tested on its own — only an operator's manual Retry, or the record actually changing on the far side again, ever revisits it.


## 12 Tests, gate, and CI

### Build

- Per mapper: pure-function tests (`external_id`, fingerprint-changes-with-the-stamp, fingerprint-changes-with-each-mapped-field) plus real-Core tests (`apply()` creates then updates, an older stamp supersedes, a missing dependency blocks). Read the written field back from the database, never from a return value.
- A curated `ci/<platform>_gate.py`, modeled on a sibling's: it calls `run_tests_for_module` and reads `.wasSuccessful()` directly, because `bench run-tests`'s own exit code is 0 even on failure. A module fails the gate if it's unsuccessful, skips anything, or runs zero tests — any one of those blocks a merge the same as a hard failure.
- `ci/lints.manifest`: at minimum, a TLS-never-off check and a no-committed-credentials check, the second armed from environment variables read from a gitignored credentials file, never from a literal in a tracked file.

**Verify** every new test file is added to the gate's allow-list in the same commit, or the PR explicitly says why it's left out. "Added later" has a way of becoming "never."


## 13 Keep the copy upgradeable, and land through review

### The `template-base` branch

You copied the template once and started your own history, so `git pull` can't bring later template fixes in. Save the template's own `upgrade_template.sh` script (from `docs/UPGRADING_YOUR_COPY.md` in the *newest* tag you can reach) outside your repo, and run it at every upgrade:

```
bash upgrade_template.sh <repo dir> <new tag> <snake_name> "<Display Name>" <ClassForm>
```

It maintains a `template-base` branch — the template, renamed to your names, at the tag you're on — so each upgrade's merge only asks about files that both you *and* the template changed. Merge every upgrade with a real merge commit, never squash or rebase: the merge commit is what tells the *next* upgrade where your base is.

### Land the work

- Branch from the team's agreed integration branch, open a PR back into it, gate green at the exact pushed commit.
- Cite code as `path::function` in commit messages and docs — never a line number, which is wrong after the next commit.
- Any entity that carries money needs two independent reviewers and a human, before merge, every time — not a one-off exception for the first money entity.

Synthesized from the connector template's own `docs/` (GETTING_STARTED, BUILD_A_CONNECTOR, MAPPER_TEMPLATE, CODING_GUIDELINES, UPGRADING_YOUR_COPY) at tag `v2.0.0.23`, and from `ivendnext_sage_x3` — the one connector on this bench that has actually walked every phase above. Re-read the cited source before you build on a specific mechanism; this page is a map, not a replacement for it.
