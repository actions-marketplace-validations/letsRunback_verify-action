# Runback Verify (GitHub Action)

A thin composite-action wrapper around [`@runback/verify`](https://www.npmjs.com/package/@runback/verify) —
checks the tamper-evidence and signature of a `runback.cassette/v1` audit
record in CI, so a broken or unsigned export fails the build instead of
being noticed later. No account, no API key.

## Usage

```yaml
- uses: letsRunback/verify-action@v1
  with:
    path: "audit-exports/**/*.json"
```

### Inputs

| Input                | Required | Default | Description |
|-----------------------|----------|---------|--------------|
| `path`                | yes      | —       | Glob matching one or more cassette/narrative/finding JSON files. |
| `key`                 | no       | —       | HMAC signing key, for legacy HMAC-signed records only. Pass via a secret. |
| `version`             | no       | `2`     | `@runback/verify` npm dist-tag or exact version to install. |
| `fail-on-unverified`  | no       | `true`  | Fail the step on a self-consistent-but-unsigned/unpinned record (exit 2), not just a broken one (exit 1). |

### Outputs

| Output    | Description |
|-----------|-------------|
| `verdict` | Worst verdict across all matched files: `invalid` \| `unverified` \| `valid`. |
| `checked` | Number of files verified. |

## Why this exists

`npx @runback/verify` already runs anywhere with no account. This wraps it in
a composite action for the one thing a raw CLI can't do on its own: fail a
GitHub Actions run and surface an annotation inline on the PR, so tamper
detection is a CI gate a team can require, not a command someone has to
remember to run.

## Publishing (maintainer notes)

Developed here, published to the public [`letsRunback/verify-action`](https://github.com/letsRunback/verify-action),
because a Marketplace listing needs `action.yml` at the root of its own public
repository. `.github/workflows/mirror-verify-action.yml` copies this directory
there on a `verify-action-v*` tag and moves the `v1` major tag users pin to.
v1.0.0 was published on 2026-10-19. The Marketplace listing itself is a
one-time click on the release ("Publish this Action to the GitHub
Marketplace"); there is no API for it.
