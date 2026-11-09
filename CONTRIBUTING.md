# Contributing

## What belongs here

- Code that more than one of our applications uses. Code that only one application needs stays in that application.
- Code that knows nothing about any one application: no application names, database schemas, configuration keys or HTTP routes.
- Code whose API is settled enough to share. Prototypes stay on branches until the applications that will use them have tried them.

## Pull requests

- Keep one change per pull request, in one module where possible.
- In each module you changed, run:

  ```sh
  cd go/<name>
  gofmt -l .
  go vet ./...
  go test ./...
  ```

  `gofmt -l` prints nothing when the code is formatted.
- Give every exported identifier a doc comment that states its contract: what it does, what it requires, and how it fails.
- Add tests for new behaviour. Tests do not reach the network; use fakes or `net/http/httptest`.
- If the change alters a public API, say so in the description and say how a consumer updates.

## Commit messages

Use [Conventional Commits](https://www.conventionalcommits.org/), with the module as the scope:

```text
feat(authz): add fgagen, the OpenFGA model constant generator
feat(authn): refuse an empty login config instead of discovering nothing
docs(authn): record the findings of the core and zinc proof of concept
```

A change that touches no module has no scope, for example `docs: describe the repository layout`. The body says why the change is made.

## Versions and releases

Each module is versioned on its own, with [semantic versioning](https://semver.org/).

- A version is a git tag `go/<name>/vX.Y.Z`, for example `go/authn/v0.3.0`, on a commit in `main`.
- A tag is never moved or deleted once pushed. A bad release is fixed by releasing the next version.
- Before `v1.0.0`, a minor version may change the API. Its release notes say how to update.
- From `v2`, the module path ends in the major version, `pkg.narwhl.dev/<name>/v2`. The `go.mod` stays in `go/<name>/`; there is no `v2` directory.
- A release workflow publishes each tag to `pkg.narwhl.dev`. It is added to this repository together with the first module.
