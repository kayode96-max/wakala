# Contributing to Wakala

Thank you for your interest in contributing to Wakala, a USDT and mobile-money swap protocol for the W3Node Node Engine hackathon (Cape Town, 12–14 November 2026). This document explains how to propose changes safely and consistently.

The project name **Wakala** is provisional. “Wakala” is Swahili for agent; please check for naming clashes before any public launch.

## Code of conduct

Contributors are expected to treat one another with respect. Harassment, discrimination and personal attacks are not acceptable. If you experience or witness unacceptable behaviour, email the maintainer (see [Security disclosure](#security-disclosure)) rather than posting publicly.

## Getting started

1. Fork the repository (or request collaborator access) and clone your copy.
2. Create a feature branch from `main` (see [Branch naming](#branch-naming)).
3. Follow the setup steps in the root [README](README.md). Until the application is scaffolded, treat any `TODO` setup commands in the README as placeholders, not as working instructions.
4. Keep secrets out of the repository. Copy `.env.example` to `.env.local` when it exists, and never commit `.env`, private keys, seed phrases, paymaster keys or mobile-money API credentials.

### Suggested local toolchain

- Node.js 20 LTS (or later) and a package manager (pnpm preferred when the monorepo is initialised)
- Foundry (`forge`, `cast`, `anvil`) for the escrow and bond contracts
- Access to the Safaricom Daraja sandbox (and later the MTN MoMo sandbox) for callback testing

## Branch naming

Use short, descriptive prefixes:

| Prefix | Use |
| --- | --- |
| `feat/` | New user-facing behaviour |
| `fix/` | Bug fixes |
| `docs/` | Documentation only |
| `chore/` | Tooling, dependencies, CI |
| `refactor/` | Internal change with no intended behaviour change |
| `test/` | Tests only |
| `style/` | Formatting that does not change meaning |

Examples: `feat/escrow-timeout-refund`, `fix/daraja-callback-signature`, `docs/whitepaper-bond-slash`.

Do not commit directly to `main`.

## Conventional commits

Write commit messages in the [Conventional Commits](https://www.conventionalcommits.org/) style:

```
<type>(optional scope): short summary in the imperative mood

Optional body explaining why, not only what.
```

Common types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `ci`.

Examples:

- `feat(escrow): release only after provider callback`
- `fix(orders): prevent double-release on duplicate callbacks`
- `docs(prd): mark USSD claim as could-have`

If a change is a breaking API or contract change, add `!` after the type (`feat!: ...`) and describe the break in the body. Treat contract upgrades and storage-layout changes as breaking.

## Pull request process

1. Rebase or merge the latest `main` before opening the pull request.
2. Keep the pull request focused. Prefer several small PRs over one that mixes UI, contracts and documentation.
3. Fill in the PR description: what changed, why, how to test it, and any screenshots or screen recordings for interface work.
4. Link related issues.
5. Ensure linting, unit tests and Foundry tests pass locally. Until CI is wired up, say in the PR what you ran.
6. Do not include generated artefacts (`node_modules/`, `.next/`, Foundry `out/` and `cache/`, coverage reports).
7. A maintainer will review. Address review comments with additional commits or an amended history, as agreed.
8. Squash-merging is preferred unless the commit history is already clean and reviewable.

Contract changes require:

- updated Foundry tests, including timeout, dispute and bond-slash paths
- a short note on storage layout and whether a redeploy is required
- the addresses used on the relevant testnet, if already deployed

## Code style

- **TypeScript / Next.js:** prefer TypeScript strict mode, named exports for utilities, and clear component boundaries. Follow the project's ESLint and Prettier configuration once added. Until then, match neighbouring files, use two-space indentation, and avoid unnecessary `any`.
- **React:** keep cash-out and cash-in flows understandable under stress. Prefer semantic HTML, visible focus states and a large-text, low-data mode. Order status must remain readable offline or on a slow connection once cached.
- **Solidity / Foundry:** follow Checks-Effects-Interactions, favour explicit visibility and custom errors, and add NatSpec for public and external functions. Escrow and bond accounting must be covered by tests. Do not leave compiler warnings unaddressed without a comment.
- **Backend / callbacks:** treat Daraja and MoMo webhooks as untrusted until authenticated. Handlers must be idempotent.
- Do not commit commented-out blocks of dead code. Remove or document temporary hacks.

## Testing

- Add or update tests alongside behavioural changes.
- Application logic: unit tests for quote matching, order state transitions and callback verification. Integration tests for sandbox cash-out where credentials exist.
- Contracts: Foundry tests for lock, release, refund-after-timeout, dispute and bond-slash, including attempts to release on a fake or missing proof.
- Manual checks: the order timeline (locked → paid → released), a screenshot “payment” that must *not* release funds, and a timeout refund path on Anvil or a testnet.
- Record a short backup demo video before relying on live sandbox callbacks during a pitch.

## Issue reporting

Use GitHub Issues. Please search existing issues first.

Include:

- a clear title
- expected versus actual behaviour
- steps to reproduce
- environment (OS, browser or device, Node and Foundry versions)
- logs, transaction hashes or screenshots, with secrets redacted

Label suggestions: `bug`, `enhancement`, `docs`, `good first issue`, `hackathon`, `protocol`.

## Security disclosure

Do **not** open a public issue for vulnerabilities, leaked keys, escrow bugs, bond-slash flaws, or weaknesses in payment-callback verification.

Email the maintainer privately with a description, impact assessment and, if possible, a proof of concept. Allow reasonable time for a fix before any public disclosure. Rotate any credentials you believe may have been exposed.

Issues in the escrow or bond contracts are treated as critical.

Maintainer: Usman Olukayode. A dedicated security contact will be added here when the public repository is published.

## Licence

By contributing, you agree that your contributions are licensed under the MIT Licence in [LICENSE](LICENSE).
