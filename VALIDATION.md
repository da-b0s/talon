# Validation and submission handoff

Repository: https://github.com/da-b0s/talon, branch `main`.
Public demo: https://talon-inky.vercel.app (Hedera testnet).

## Recorded clean scaffold — 29 September 2026

Source: `5c15a2b`; repeated scaffold at `17ff5e8` verified the corrected outro.
All 161 compared application/toolchain files were identical between those copies.
Tools: Node 24.12.0, Yarn 3.2.3, create-scaffold-hbar 0.4.1.
Clean destination: `talon-scaffold-check-20260928c` outside the source checkout.

Install with `--immutable`, lint, 174 library tests, 83 contract tests and the
production build passed. Seven core routes returned HTTP 200. No private
environment files were copied. Browser automation did not initialize, so this
run did not establish browser hydration or wallet behavior.

The Windows machine required a writable process-local `XDG_CACHE_HOME` and a
process-local Yarn command pointing to the bundled Yarn. The external CLI checks
for Yarn before downloading the template; bundled Yarn is available only after
download. These were tooling workarounds, not edits to generated application code.

## Submission review — 4 October 2026

The documentation/comment update builds on `17ff5e8`; no contract logic or wallet
configuration was changed. Local lint, 174 library tests, 83 contract tests and
the production build passed. Gitleaks 8.30.1 scanned all 33 reachable commits with
its default rules and redacted output: no leaks found. Only `.env.example` files
are tracked; exact `.env`/`.env.local` history checks found none. A scanner result
is not a guarantee that every possible credential format is detected.

### Final public scaffold and Harness

Tested source: `43848c1cecb6ccfed62bb3f7336112c76a60be14`.
Tools: Node 24.12.0, Yarn 3.2.3, create-scaffold-hbar 0.4.1, Harness 1.2.2.
Fresh destination: `talon-final-scaffold-20261004` outside the source checkout.

```sh
npm create scaffold-hbar@latest -- talon-final-scaffold-20261004 --template da-b0s/talon --ci --skip-install --skip-hedera-skills
cd talon-final-scaffold-20261004
node .yarn/releases/yarn-3.2.3.cjs install --immutable
```

Scaffolding and immutable install passed. Installation took 8m 54s on the Windows
machine and emitted peer-dependency warnings. The same process-local cache/Yarn
workarounds described above were used. No private environment files were copied.

For the Harness source-file gate only, the unchanged `template.json` from the
tested revision was restored: the CLI intentionally consumes/removes that file.
No generated application code or validator assertion was changed. Comparison
of 161 application/toolchain files found only line-ending differences in two
files. The Harness's `validateWorkspace` entry point ran the committed spec
and validators and returned **passed=true, zero findings**:

| Check | Result |
| --- | --- |
| Install, lint, library tests, contract tests, production build | All exit 0 |
| Library / contract tests | 174 / 83 passed |
| Browser smoke checks | 9/9 routes returned 200 and rendered |
| Browser console errors / forbidden error text | None |
| Source-file and configured secret assertions | Passed |

Routes: `/`, `/how-it-works`, `/feeds`, `/evidence`,
`/evidence?topic=0.0.4318417`, `/evidence?topic=0.0.999999999`, `/policies`,
`/debug`, `/blockexplorer`. Browser: installed Chrome through Playwright.
An earlier interrupted Harness attempt produced no result and is not counted.
The [machine-readable summary](validation/bounty-check-20261004.json) preserves
the completed result. This was deterministic validation, not the optional AI
semantic reviewer or funded chain-validation tier. It did not sign wallet
transactions. Subsequent handoff/README edits only record these results.

The deployed contracts and September 29 website still use the earlier source.
The Solidity edit in this review changes documentation comments only; deployed
Sourcify verification refers to the original deployed source, not a rebuilt
artifact containing the new comment metadata. No redeployment was needed.

## Public testnet evidence

- Settlement contract: `0xa95601CA138C2a673C4314E6beFf56bb3832257C`.
- Operation: `trigger(1)` for the current deployment.
- [Transaction](https://hashscan.io/testnet/transaction/0x3d3397ddfe128d84d749be71916db810011fa9c6124fc2ed404ecd3b3540c0ab).
- [Mirror result](https://testnet.mirrornode.hedera.com/api/v1/contracts/results/0x3d3397ddfe128d84d749be71916db810011fa9c6124fc2ed404ecd3b3540c0ab):
  rechecked 4 October, `SUCCESS`, contract `0.0.10752545`.
- [HCS messages](https://testnet.mirrornode.hedera.com/api/v1/topics/0.0.10752744/messages?limit=100&order=asc):
  three records, `policy_created`, `triggered`, `settled`, rechecked 4 October.
- Full current addresses, reproduction scripts and older evidence: [EVIDENCE.md](EVIDENCE.md).

Chainlink supplies the oracle observations used by settlement. Hedera contracts
hold escrow and enforce lifecycle/payment rules. HCS is a separate operator's
ordered summary, not independent proof of its claims or a complete event log.
The HSS helper has no demonstrated scheduled-expiry execution in this handoff.

## Files and remaining owner actions

- Setup/workflow: [README.md](README.md), [START-HERE.md](START-HERE.md).
- Agent guidance: [AGENTS.md](AGENTS.md); manifest: [template.json](template.json);
  license: [LICENSE](LICENSE).
- Tests: `packages/hardhat/test`, `packages/nextjs/lib/settlement`, wallet utility tests.
- Harness 1.2.2: `.harness/spec.yaml`, `.harness/acceptance-contract.json`,
  `.harness/validators/static.json`, `yarn.json`, `playwright-smoke.yaml`.
- Public wallet transactions remain unverified; wallet configuration is unchanged.
- Human editorial/code review, registration, developer-experience survey and
  submission are not asserted complete. Review and submit through the
  [official submission page](https://hedera.com/scaffold-hbar-template-bounty/).
