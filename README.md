# Rayls retry, message identity and application controls

**Release: `david-retry-reviewed-20261021`**

Start with the question: **does retrying an existing cross-chain message mean the same thing as submitting a business request again?** This package contains the article, three exact evidence diagrams, execution programs, pinned source, saved observations and separate verification programs.

The central application comparison is 14 versus 7 synthetic units. Two source submissions of the same request produced two successful protocol IDs in both applications. The deliberately unguarded fixture applied the request twice; the request-ID fixture applied it once. A separate sequencing control produced 12 or 21 depending on manual delivery order, while an application sequence guard rejected a premature step and allowed its later same-ID retry.

## 1. Choose the check you actually want

| Route | What it establishes | Requirements |
|---|---|---|
| Read the study | Inspect claims, raw records, diagrams and limitations | An archive tool and a text editor or browser |
| Check distribution identity | Included bytes match the supplied manifest | Python 3 standard library, or Node without installed packages |
| Verify saved observations | Calculations, source IDs, receipts, events and same-block state agree internally | Node and the locked npm dependencies |
| Run new contract experiments | Observe fresh local EVM execution | Node and the locked npm dependencies |
| Rebuild official Go components | Re-run selected relayer proof/calldata components | Go plus Node; see the optional section below |
| Run the complete private network | Exercise the actual service topology | A separate Docker-capable host and coordinated official components; not completed by this release |

These routes have different meanings. A checksum is not a blockchain observation. A saved-record verifier does not run the experiment. A successful component build is not a running relayer. A pair of Ganache providers is not an institutional network.

## 2. Understand the three evidence layers

**Baseline contract suite.** Four preserved executions: `evidence/run-01`, `run-02`, `run-03` and `review-evidence/run-04`. Each uses one Ganache EVM, physical chain ID 31337, with logical Endpoint IDs 10101/20202. Each has 33 focused cases, three sequences of 60 operations, 579 local transactions and 103 intentional reverts. The restart reopens a local database in another Node process. It is not a relayer restart.

**Added application controls.** Two fresh executions: `extension-evidence/run-01` and `run-02`. Each creates two separate, unforked Ganache providers, physical chain IDs 10101 and 20202, in one Node process. Each has 16 cases, 14 source messages, 19 delivery attempts, 90 local transactions, seven intentional reverts and 12 consumed protocol IDs. An authorized local account manually delivers messages. Application fixtures, their guards and permissive source validation are explicitly authored test code.

**Official component supplement.** `official-components/` preserves the earlier source-EVM re-execution and actual official Go proof/calldata component outputs. Its 18 calldata comparisons use its own preserved destination dataset; they are not the 19 delivery attempts in the new application runs. Source replay, proof generation, calldata encoding and destination execution must not be conflated.

No layer establishes production payment behavior, real token movement, authenticated Axyl consensus, a live CTS/Hub path, automatic relayer recovery or a production deployment mapping.

## 3. Extract the package

1. Save `david-rayls-retry-study.zip` in a folder you can find.
2. Use **Extract All** on Windows, double-click the ZIP on macOS, or use an archive manager on Linux.
3. Open the extracted `david-rayls-retry-study` directory. Do not run programs from an archive preview.
4. Confirm it contains `RELEASE.json`, `README.md`, `ARTICLE.md`, `package.json`, `extensions`, `evidence`, `official-components` and `figures`.
5. On Windows, open a terminal in this directory. On Linux/macOS, use `cd` to enter it.

Check your location:

```powershell
Get-Location
Get-ChildItem
```

Linux/macOS equivalents:

```bash
pwd
ls
```

All commands below assume this project directory unless a different directory is stated.

## 4. Inspect without executing research code

Open `ARTICLE.html` for a self-contained reading copy with the three diagrams. Open `ARTICLE.md` for plain Markdown. `SUBSTACK.html` and `SUBSTACK.md` adapt the same findings to a shorter newsletter format.

`RESULTS.md` gives numerical results and precise paths. `CLAIM_LEDGER.md` maps statements to records and limitations. `OFFICIAL_COMPONENTS.md` explains how the supplement relates to this release. `PUBLICATION.md` gives image positions, captions, alt text and platform instructions.

Individual JSON and JSONL files can be opened in a text editor. A receipt status of `0x0` is expected in deliberate rejection tests; it does not mean the experiment failed. Synthetic local block timestamps are deliberately initialized from 2026-01-01. Actual run start/end times are recorded separately in each summary.

## 5. Check byte identity before installing or running anything

With Python 3:

```bash
python verification/check_distribution.py
```

On systems where the executable is `python3`, substitute that name. On Windows, `py -3 verification/check_distribution.py` is another option if the Python launcher is installed.

Alternatively, with Node and no npm packages installed:

```bash
node scripts/check_integrity.cjs
```

The first command verifies every entry in the root SHA-256 manifest. The Node command also checks 383 vendored contract Git blob identities against the pinned inventory. The component directory has its own unchanged original manifest.

Expected: PASS, with no unexplained mismatch. The root manifest omits itself. Installed dependencies, regenerated local databases and your new `runs/` directories are not distribution entries. Extra files you create are not certified by the supplied manifest.

A hash establishes identity relative to the supplied inventory, not independent authenticity of blockchain observations. If a check fails, preserve the output and obtain the intended release. Do not rewrite checksums to hide an unexplained difference.

## 6. Install the tested Node toolchain and locked packages

Recorded execution: Linux, Node `v24.19.0`, npm `11.9.0`, Ganache `7.9.2`, ethers `6.16.0`, solc-js `0.8.24`, OpenZeppelin contracts `5.4.0`. The project declares Node 22 or newer; this release's executed validation uses Node 24.

If needed, obtain Node from [nodejs.org](https://nodejs.org/en/download), install a suitable Node 24 build and reopen your terminal. Check:

```bash
node --version
npm --version
```

Then install from the supplied lockfile:

```bash
npm ci --ignore-scripts --no-audit --no-fund
```

This is the dependency-download step and needs internet access. Install lifecycle scripts are disabled. The local EVM experiments do not use a fork or remote RPC and disable `fetch` in their workers.

If PowerShell blocks only the npm PowerShell wrapper, use:

```powershell
npm.cmd --version
npm.cmd ci --ignore-scripts --no-audit --no-fund
```

These alternatives do not require changing the PowerShell execution policy. If executable-control or organizational policy blocks Node or Docker itself, use static inspection or an approved environment. This release does not require disabling Windows protections. Native Windows execution has not been validated; the recorded successful runs are Linux runs.

No Docker, WSL, wallet, real asset, mainnet funding or Go installation is required for the baseline or added application experiments.

## 7. Verify all supplied observations with one command

After dependency installation:

```bash
node verification/verify_all.cjs --output runs/saved-audit-01
```

Choose a new output directory if that name exists. This command checks the original runs, added runs, their repeatability, compiler/deployment consistency and the saved component observations. It runs no Ganache EVM and contacts no live RPC. It preserves command output under the chosen directory.

Some original programs regenerate deterministic reports inside the supplied run/review directories. Run the distribution integrity check first. Checksum verification and saved-record verification answer different questions.

Expected final status: PASS. Review `runs/saved-audit-01/summary.json` and the per-command logs. A result named `rejected` in the checker mutation controls is expected: those controls intentionally alter copies to confirm that inconsistent evidence is detected.

## 8. Inspect the added application evidence directly

```bash
node extensions/verify.cjs extension-evidence/run-01
node extensions/verify.cjs extension-evidence/run-02
node extensions/compare.cjs extension-evidence/run-01 extension-evidence/run-02
```

Expected in each verifier report:

| Check | Count |
|---|---:|
| Application cases | 16 |
| Transaction/RPC bindings | 90 |
| Getter bindings, including repeated snapshots | 940 |
| Source message ID reconstructions | 14 |
| Destination calldata bindings | 19 |
| Committed protocol IDs | 12 |
| Applied application-effect events | 11 |
| Successful duplicate-ignored events | 1 |

These are different observations, not counts of independent experiments. A protocol message can complete successfully while the application deliberately records no second business effect.

The verifier decodes raw source receipts, reconstructs IDs, matches receiver calldata, checks exact RPC submissions/receipts/mined inputs, binds reported state to same-block getters and validates the explicit fee-cap ordering. It also checks that compiled source text matches the distributed source and installed dependencies. It uses the same ethers library as the harness; it is a separate checker, not an independent implementation of every primitive.

Inspect the checker controls separately:

```bash
node extensions/check_controls.cjs extension-evidence/run-01
```

Expected: accept the unchanged copied dataset; reject altered reported totals, source-message payloads and destination calldata. These mutations are synthetic tests of the checker.

## 9. Run the two-EVM application experiment yourself

Choose a fresh output directory:

```bash
node extensions/run.cjs --output runs/application-01
node extensions/verify.cjs runs/application-01
node extensions/compare.cjs extension-evidence/run-01 runs/application-01
```

The runner compiles locally, creates separate unforked providers, deploys real pinned protocol implementations and authored application fixtures, dispatches source messages and manually delivers the controlled destination calls. It records all RPC traffic, source and destination transactions, receipts, cases and before/after state.

The new controls explicitly set transaction type 2, a 3-gwei maximum fee and a 1-gwei priority cap. The priority cap is below the maximum fee. This avoids relying on the older fixture's default fee construction. It does not establish admission rules on Axyl or production.

Expected final line:

```text
PASS application controls: 16 cases; 14 source messages; 19 delivery attempts.
```

Expected outcomes: unguarded 14; guarded 7 after the two equal requests, then 15 after a distinct 8-unit request; recovery 0 then 5; forward 12; reverse 21; sequence-enforced recovery 12.

This runner has no persistent database or external service to clean up. Its output directory is the record of your experiment. Preserve failures as well as successes. A discrepancy is a result to investigate, not a reason to edit output files.

## 10. Verify or rerun the broader baseline

To inspect a supplied baseline run:

```bash
node scripts/verify.cjs evidence/run-01
node review/reviewer_verify.cjs evidence/run-01
node scripts/verify_deployments.cjs evidence/run-01
```

For all four recorded baseline runs:

```bash
node scripts/compare.cjs evidence/run-01 evidence/run-02 evidence/run-03 review-evidence/run-04
```

To repeat compilation using the same compiler and source closure:

```bash
node scripts/recompile.cjs evidence/run-01
```

This is a same-compiler reproducibility check, not an independently written compiler rebuild. Deployment checks compare creation inputs, linked library addresses and runtime bytes while handling compiler-declared immutables explicitly.

To execute the complete baseline from a fresh local database:

```bash
node scripts/run.cjs --output runs/baseline-01
node scripts/verify.cjs runs/baseline-01
node review/reviewer_verify.cjs runs/baseline-01
node scripts/compare.cjs evidence/run-01 runs/baseline-01
```

Expected final line from execution:

```text
PASS complete study: 33 focused cases, 180 seeded operations, and separate-process restart.
```

The baseline worker exits and another process reopens its local Ganache database. Raw database files are regenerated and omitted from this distribution. Do not confuse the Node/database restart with a relayer service restart. Retain your output; delete only a directory you created when you no longer need it.

Some preserved older Ganache transactions have fee-cap combinations a stricter Ethereum client would reject. The unchanged baseline replays local EVM behavior, not production transaction admission. The new application controls and the supplement's fresh type-2 compatibility control explicitly use valid fee caps.

## 11. Locate a claim in the raw evidence

| Question | Record |
|---|---|
| Failed attempt rolls back both markers | `extension-evidence/run-01/cases/E01.json` |
| Same ID succeeds after recovery | `extension-evidence/run-01/cases/E02.json` |
| Successful replay is rejected | `extension-evidence/run-01/cases/E03.json` |
| Same request applied twice without a business guard | `E04.json`, `E05.json` in that cases directory |
| Two IDs consumed, only one guarded effect | `E06.json`, `E07.json` |
| Duplicate request ID with a changed amount rejected | `E09.json` |
| Different business request remains usable | `E10.json` |
| Application source-context controls | `E11.json`, `E12.json`; E11 is explicitly a controlled receiver input |
| Forward 12 and reverse 21 | `E13.json`, `E14.json` |
| Sequence failure and identical-ID recovery | `E15.json`, `E16.json` |
| Emitted IDs and delivery references | `messages.json`, `deliveries.json` in the same run |
| Source/destination raw transactions | `transactions-source/`, `transactions-destination/` |
| Exact raw RPC traffic | `rpc-source.jsonl`, `rpc-destination.jsonl` |
| Physical-chain and deployment information | `fixture.json`, `deployments-source.json`, `deployments-destination.json` |
| Compiled inputs and build identity | `solc-input.json`, `compiled-contracts.json`, `build-provenance.json` |
| Earlier strict-nonce controls | Baseline `focused/F11.json` through `F16.json` |
| Earlier tentative SSTORE versus committed slot | Baseline `trace-summary.json`, `traces/F06.json.gz` |

Read the `destination_transactions` paths in a case to find the corresponding full receipts. A failed receipt has no surviving logs. Revert bytes are obtained by replaying the exact transaction input with `eth_call` at its parent block; that request/error is retained in the journal.

## 12. Optional official-component inspection

The original supplement is included unchanged under `official-components/`, including its own manifest and source provenance.

```bash
python official-components/check_integrity.py
```

Then enter the component directory for its saved-record checker:

```bash
cd official-components
node verify_saved.cjs evidence/run-02
cd ..
```

Expected: 877 upstream blobs, 40 source replay bindings, 18 preserved destination-calldata comparisons, type-0 proof/root agreement and type-2 root disagreement for the two one-transaction Ganache controls. The checker is not a generic MPT verifier and does not rebuild the historical Go binary.

For fresh Go execution, follow `official-components/README.md`. The recorded toolchain was Go 1.26.4. Build the official relayer and authored study driver from the pinned source, then run the component runner. Its current native runner targets Linux binary names; native Windows execution is not validated.

That source-only run executes the official proof generator and calldata component, not an initialized relayer service, Hub transport or destination service. Full service startup additionally needs Docker and coordinated nodes, CTS, databases, messaging and relayers. It was not completed in this review.

## 13. Troubleshooting

| Symptom | Next step |
|---|---|
| Cannot locate package or script | Extract completely and enter the project root |
| `node` not recognized | Install an appropriate Node version, reopen the terminal and check the version |
| Only `npm.ps1` is blocked | Use `npm.cmd`; do not change the machine execution policy |
| Executable-control policy blocks software | Use static inspection or an approved machine |
| Optional native uWS warning | Ganache may use its JavaScript fallback; inspect the actual exit status and result |
| Missing npm dependency | Finish the locked `npm ci` step from the project root |
| Output directory already exists | Preserve it and choose a new name |
| Intentionally reverted receipt | Match it to the case's expected error; status alone is insufficient |
| Hash, source or state assertion fails | Preserve logs and the output directory; investigate the discrepancy |
| Archive contains many small files | Extract first; do not browse execution programs inside the ZIP viewer |
| Docker unavailable | The Node contract studies still have their own reproduction route; full transport remains untested |
| Historical public RPC returned 403 | Treat it as unavailable from that environment, not zero state or a global outage |

## 14. Publish the matching revision

Read `PUBLICATION.md`. Update the public evidence ZIP and repository README before replacing the article text. The intended repository is `https://github.com/davidvnvn276/david-rayls-retry-study`. This package does not claim that the new release was uploaded by the review.

Use `GITHUB_README.md` as the repository landing README, keep the ZIP's exact filename, and add the external `.sha256` file supplied alongside it. A public GitHub commit permalink is preferable when citing a fixed release.

The article identifies `david-retry-reviewed-20261021`. Readers should find that same release in `RELEASE.json` after extraction. Keep the opening scope and synthetic-unit distinction when editing or cross-posting. The publication files contain no fabricated explorer screenshots.

## 15. What remains unverified

The full official network and relayer service path, authenticated header/proof acceptance by the Hub, CTS encryption/decryption, actual Axyl transaction admission, production deployment identity, live message ordering and automatic service recovery remain unverified.

The guarded application is a didactic fixture. It does not establish a complete issuer policy, distributed banking workflow or a guarantee that every business system executes exactly once. The experiment provides specific, reproducible observations and keeps those limitations visible.
