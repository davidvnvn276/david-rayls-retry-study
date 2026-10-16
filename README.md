# DAVID: Rayls retry and message identity study

This package tests a useful integration question: **what can be retried, and what counts as a new message?**

The central local result is reproducible: replaying a successful ID was rejected; another source submission of the same application payload produced a new ID and another effect. The pinned source senders set `ignoresNonce=true`, so their messages also executed out of order in this harness. Manually setting the flag false exercised a different, conditional receiver policy.

Read `ARTICLE.md` for the publication draft and `RESULTS.md` for the exact evidence map. All documents and programs are in English.

## 1. Understand the scope before running anything

- **Actual Rayls code:** Endpoint, MessageSender, BatchMessageSender, MessageReceiver, ResourceManager, MessageExecutor and AccessManager, pinned to commit `ed8863885c8ed5d5d317d47a34c063907192687a`.
- **Physical environment:** one local Ganache EVM, chain ID 31337. Endpoint settings 10101/20202 are logical identifiers inside that VM, not separate running networks.
- **Manual transport:** an authorized local fixture account injects source-emitted messages into the destination Endpoint. No live relayer, Private Network Hub, CTS, proof verification or remote RPC is running.
- **Test fixtures:** the application records counter units, not money. Participant/token validation is deliberately permissive. The reentry target and compatible V2 implementation are also study fixtures.
- **Resource scope:** direct addresses and existing resource mappings. Automatic factory deployment is not exercised; the ResourceManager’s unused factory reference is zero, as its constructor allows.
- **Source scope:** this is a public-source snapshot, not a confirmed production deployment, security audit, benchmark or report of a customer incident.

The upstream package metadata reports `2.6.3-nightly.2`; `sources/upstream-package.json` preserves it. The local chain clock is deliberately initialized to 2026-01-01 and advances by one second per block. These are synthetic local block timestamps; actual experiment wall-clock timestamps are recorded separately in each run summary.

No wallet, seed phrase, private key, gas purchase, Docker, WSL, Go or Foundry is needed. The provider uses public deterministic development accounts locally. Never fund or reuse those accounts for real assets.

## 2. Extract the ZIP completely

1. Save `david-rayls-retry-study.zip` to a folder you can find, such as Downloads.
2. Right-click the ZIP and choose **Extract All** on Windows. On macOS, double-click it. On Linux, use your archive manager.
3. Open the extracted `david-rayls-retry-study` folder. Do not run programs from an archive preview.
4. You should see `README.md`, `ARTICLE.md`, `package.json`, `package-lock.json`, `scripts`, `vendor`, `evidence` and `figures`.
5. On Windows, click File Explorer’s address bar in this folder, type `powershell` and press Enter. This opens a terminal in the correct directory.
6. On macOS/Linux, open Terminal, type `cd `, drag the extracted folder into the window, and press Enter.

To confirm the location:

```powershell
Get-Location
Get-ChildItem
```

On macOS/Linux:

```bash
pwd
ls
```

The listing must include `package.json` and `scripts`. If you are in `C:\Windows\System32` or a different folder, return to the extracted project before continuing.

## 3. Install Node.js once

1. Visit [nodejs.org](https://nodejs.org/en/download).
2. Install Node.js 24 for your operating system, including npm. The recorded runs used Node v24.19.0 and npm 11.9.0 on Linux.
3. Close and reopen the terminal in the extracted project folder.
4. Run these as two separate commands:

```bash
node --version
npm --version
```

If PowerShell blocks `npm.ps1`, use `npm.cmd --version` and `npm.cmd` for the install command below. You do not need to change the system execution policy.

Native Windows execution was not tested in the recorded experiment. The scripts use portable Node APIs and Windows commands are supplied, but only the Linux runs are claimed as verified.

## 4. Check the untouched distribution before installation

This command uses Node’s standard library only:

```bash
node scripts/check_integrity.cjs
```

Expected: a PASS for the SHA-256 distribution manifest and a PASS for 383 vendored Git blob identities.

If a hash differs, keep the error and obtain a clean distribution. Do not regenerate the manifest to conceal an unexplained change. A manifest establishes byte identity relative to the distributed inventory; it does not independently authenticate a provider, compiler, experiment or live blockchain.

The manifest excludes itself, installed dependencies, local database directories and new `runs/` folders. The recorded raw receipts, state captures, compiler inputs and verification reports are included.

## 5. Install the locked dependencies

Run once, from the project root:

```bash
npm ci --ignore-scripts --no-audit --no-fund
```

On PowerShell, if necessary:

```powershell
npm.cmd ci --ignore-scripts --no-audit --no-fund
```

This step needs internet access to obtain packages. Later EVM execution has no live RPC or network fallback. Lifecycle install scripts are disabled. The exact dependencies are pinned by the included lockfile.

## 6. Inspect the supplied evidence without rerunning the EVM

```bash
node scripts/verify.cjs evidence/run-01
node scripts/verify.cjs evidence/run-02
node scripts/verify.cjs evidence/run-03
node scripts/compare.cjs evidence/run-01 evidence/run-02 evidence/run-03
```

For deeper compiler/deployment checks:

```bash
node scripts/recompile.cjs evidence/run-01
node scripts/verify_deployments.cjs evidence/run-01
```

The first repeats compilation using the original entry files and preserved 66-file source closure. It uses the same compiler implementation, not an independently written compiler. The second reconstructs deployment input bytes, checks library addresses and compares the fixed runtime bytes in saved local `eth_getCode` replies. Compiler-declared immutable ranges are handled explicitly; this is not a production runtime mapping.

The verifier reads raw receipt topics/data, recomputes source message IDs, checks preserved revert bytes, reconciles counter effects with state getters and checks storage slots and opcode traces. It does not start Ganache or query a live chain. It writes a saved-record report for that directory. The comparison above checks matching focused states, seeded sequences and derived reports across the three original supplied runs. The fourth review execution is documented in step 7 below. Run 03 was reproduced after a separate clean dependency installation; `validation/clean-install.json` records the matching execution inputs.

Do not count a verifier PASS as a new empirical experiment. Do not use this script to certify an unrelated deployment.

Expected per supplied run:

| Item | Count |
|---|---:|
| Focused cases | 33 |
| Seeded sequences | 3 |
| Operations in each sequence | 60 |
| Source messages in those sequences | 28, 33, 32 |
| Local transactions, including setup and intentional reverts | 579 |
| Successful / reverted local transactions | 476 / 103 |
| Raw application-effect events | 114 |
| Source-emitted messages | 107 |
| Committed IDs across the fixture executors | 113 |

These counts have different meanings. An executor batch can apply several effects under one ID; controlled injections need not have a source dispatch. The same deterministic message IDs can recur in separate fresh runs. The totals are not distinct live users, payments or performance measurements.

## 7. Run the entire local study yourself

Choose a new output directory that does not already exist:

```bash
node scripts/run.cjs --output runs/my-check-01
```

The command verifies source identity, compiles the contracts locally, runs the focused and seeded sequences, exits the experiment worker, then starts a separate Node worker on the same saved Ganache database for the restart check. It also collects read-only opcode traces for F01, F06 and F25.

Expected final line:

```text
PASS complete study: 33 focused cases, 180 seeded operations, and separate-process restart.
```

Now reconcile your new records:

```bash
node scripts/verify.cjs runs/my-check-01
```

You can compare your run with a supplied run:

```bash
node scripts/compare.cjs evidence/run-01 runs/my-check-01
```

The comparison expects the same deterministic source, fixtures and action sequence. Runtime/OS differences can be relevant; preserve discrepancies rather than editing outputs to match.

### Additional review execution and RPC binding checks

`review-evidence/run-04/` preserves a fourth complete execution after a clean dependency installation. It includes the raw records and traces, with its regenerable binary `local-chain-db` excluded from distribution. The reviewed execution used the same unchanged experiment scripts and fixture bytes as the three original runs.

After step 5, verify the additional run:

```bash
node scripts/verify.cjs review-evidence/run-04
node scripts/verify_deployments.cjs review-evidence/run-04
```

Run the additional reviewer against any supplied run:

```bash
node review/reviewer_verify.cjs evidence/run-01
node review/reviewer_verify.cjs review-evidence/run-04
```

Expected per run: 579 exact transaction/RPC bindings, 103 caller/input/parent-state revert bindings, 248 snapshot bindings and 114 same-transaction application-effect/context bindings. The 5,636 individual getter comparisons are consistency checks, not independent experiments. The reviewer also checks all 66 saved compiler source texts against actual vendored, fixture or installed dependency files. Reports are written under `review/`.

`review/four-run-comparison.json` records agreement across all four complete local executions. `review/fresh-source-verification.json` binds all 383 vendored file identities to a newly retrieved GitHub tree at the pinned commit; six critical files were also fetched afresh and matched byte-for-byte. `review/REVIEW.md` describes the complete review and its limits.

If you run it again, keep the first directory and choose another name:

```bash
node scripts/run.cjs --output runs/my-check-02
```

Execution workers disable `fetch` and use an unforked local Ganache provider. There is no mainnet URL, wallet connection or remote transaction submission in the experiment. The source events are delivered manually by the harness; this does not test a live relayer or authenticate transport proofs.

## 8. Find a specific proof

| Question | File |
|---|---|
| What happened on the first delivery? | `evidence/run-01/focused/F01.json` |
| Was the same successful ID blocked? | `evidence/run-01/focused/F02.json` |
| Did a new source submission execute the same payload? | `evidence/run-01/focused/F04.json` |
| Did nonce 4 arrive before nonce 3? | `evidence/run-01/focused/F05.json` |
| Did failure leave the ID unconsumed? | `evidence/run-01/focused/F06.json` |
| Did a later default-path message progress during that failure? | `evidence/run-01/focused/F07.json` |
| Did the identical failed message succeed after recovery? | `evidence/run-01/focused/F08.json` |
| Where are conditional strict-nonce controls? | `evidence/run-01/focused/F11.json` through `F16.json` |
| Where are executor-batch tests? | `evidence/run-01/focused/F25.json` through `F28.json` |
| Where is the compatible test upgrade? | `evidence/run-01/focused/F31.json` |
| Did state survive a new Node process? | `before-process-restart.json`, `after-process-restart.json`, `restart-summary.json` in the run folder |
| Where are raw receipts and transaction inputs? | `transactions-experiment/`, `transactions-restart/` in the run folder |
| Where are exact RPC requests/replies? | `rpc-experiment.jsonl`, `rpc-restart.jsonl` in the run folder |
| What was compiled? | `solc-input.json`, `compiled-contracts.json`, `build-provenance.json` in the run folder |
| Where are opcode traces? | `trace-summary.json` and compressed `traces/F01.json.gz`, `F06.json.gz`, `F25.json.gz` in the run folder |

Each focused record references its raw transaction files. `status: 0x1` means that local transaction succeeded; `0x0` means it reverted. Deliberately rejected replay and permission tests are supposed to have status `0x0`.

The `revert` field contains bytes recovered by replaying the exact failed input with `eth_call` against its parent block. Receipt status alone does not contain a Solidity revert reason. The matching call/error is preserved in the RPC journal.

The new `local-chain-db` folder is used only by the run’s local processes. The supplied distribution omits its binary database files because the complete experiment recreates them. The raw before/after restart captures remain included. Do not point the experiment at an unrelated database.

The later trace collector reopens the same database read-only at the chain level and checks that the last block hash stays unchanged. Its F06 trace contains a tentative `SSTORE=1`, but the historical post-transaction slot is zero. F25 contains a log opcode from the first application subcall while its reverted receipt has no logs. Open a supplied `.json.gz` with an archive tool to inspect the raw JSON; no database is needed to read it. To recollect traces for your new run, use `node scripts/capture_traces.cjs runs/my-check-01`.

## 9. Read the article and inspect the figures

This deliverable is a written article with downloadable evidence. Open `ARTICLE.html` for the self-contained reading copy and its copy buttons, or open `ARTICLE.md` in a text editor. Upload the three PNGs separately in your article editor at the locations specified in `PUBLICATION.md`.

The revised DAVID figures use the unchanged supplied Rayls symbol, exact recorded values, pale grid, black panels, purple/lilac and chartreuse. The banner is 3200 × 1280 (5:2); the two internal figures are 3200 × 1800 (16:9). SVGs, full-ID aliases, source-case references and text bounds are included. They are evidence diagrams, not explorer screenshots.

Evidence verification and EVM execution require Node only. Artwork regeneration is optional: `review/build_david_figures.py` uses Python, Pillow and Nimbus fonts; `review/render_figures.cjs` additionally uses sharp. These authoring dependencies are not required to inspect the supplied PNG/SVG or reproduce the research.

## 10. Troubleshooting

| Problem | Action |
|---|---|
| `node` not recognized | Install Node, reopen the terminal and check `node --version` |
| Cannot find `package.json` or script | Fully extract the ZIP and open the project root |
| `npm.ps1` execution policy error | Use `npm.cmd`; leave the system policy unchanged |
| Output directory already exists | Preserve it and choose `my-check-02` |
| Native µWS binary warning | Ganache can fall back to JavaScript; inspect the actual exit code and PASS/failure files |
| Missing package | Complete the locked install in step 5 |
| Assertion or compiler failure | Preserve the entire new output directory and error; do not remove guards |
| Install reports unsupported optional macOS package | See `TOOLCHAIN_NOTES.md`; use the distributed lockfile, not a newly generated one |
| Archive is slow to browse | Extract first; evidence contains many small JSON files |

## 11. Publish with the evidence

Read `PUBLICATION.md`. Upload the package or expanded project to a **new repository for this study** before publishing. Do not present an older experiment as its evidence. Add the resulting real URL to the article; no new public evidence repository is claimed by this ZIP.

Source links in `ARTICLE.md` are pinned. Keep the initial local/synthetic/manual-transport scope paragraph and the distinction between source dispatch batches and executor batches. Do not describe the recording application’s units as money or the optional strict-mode controls as the pinned source senders’ default.
