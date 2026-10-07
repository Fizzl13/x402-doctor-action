# x402 Doctor check

Check your [x402](https://x402.org) payment endpoints on every push. The step
fails on a broken 402, each problem shows up as an annotation on the run and
the PR, and the job summary lists what is wrong with the fix next to it.

Read-only: no wallet, no keys, no payment. It checks what a paying agent sees:
the 402 status, the `PAYMENT-REQUIRED` challenge, networks (Base, Solana, XRP
Ledger), asset and `payTo` addresses, amounts, timeouts, Bazaar discovery
metadata and more. The same checks as [x402 Doctor](https://x402-doctor.fizzl.eu).

## Usage

```yaml
name: x402
on: [push, pull_request]
jobs:
  doctor:
    runs-on: ubuntu-latest
    steps:
      - uses: Fizzl13/x402-doctor-action@v1
        with:
          urls: |
            https://your-api.example.com/paid
            https://your-api.example.com/other
```

Checking a dev server before you deploy:

```yaml
      - uses: actions/checkout@v5
      - run: npm ci && (npm start &) && npx wait-on http://localhost:3000
      - uses: Fizzl13/x402-doctor-action@v1
        with:
          urls: http://localhost:3000/paid
```

| Input | Default | |
| --- | --- | --- |
| `urls` | (required) | One per line or comma separated. `localhost` works for a dev server started earlier in the job. |
| `method` | tries GET, then POST | `GET` or `POST` |
| `fail-on` | `fail` | `fail`: a failed check fails the step. `warn`: warnings too. `never`: report only. |

Outputs: `overall` (`pass`, `warn` or `fail`, the worst across the URLs) and
`report` (the full reports as JSON).

Needs Node 20.18 or newer on the runner (GitHub-hosted runners have it); the
job's own Node version is not changed.

## Badge

When a public endpoint passes, the job summary gives you a live badge for your
README, so buyers and agents can see your endpoint works:

[![x402 payable](https://x402-doctor.fizzl.eu/badge.svg?url=https%3A%2F%2Fx402-doctor.fizzl.eu%2Fapi%2Fv1%2Fdiagnose)](https://x402-doctor.fizzl.eu/trust?url=https%3A%2F%2Fx402-doctor.fizzl.eu%2Fapi%2Fv1%2Fdiagnose)

## More

- Free web check: <https://x402-doctor.fizzl.eu>
- Source of the checks: [Fizzl13/x402-doctor](https://github.com/Fizzl13/x402-doctor)
- Paid preflight API for agents (before they pay an endpoint): see x402 Doctor.
