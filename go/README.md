# Go modules

## Layout

- Each domain is one module in `go/<name>/`, with its own `go.mod`. The module path is `pkg.narwhl.dev/<name>`. The `go/` directory is not part of it: `go/authn/` holds the module `pkg.narwhl.dev/authn`.
- There is no module that spans several domains, and no `common` or `util` module. Code goes in the module of the domain it serves.
- Module names are short lowercase domain names, for example `authn` (authentication) and `authz` (authorization).
- The module root holds no Go code. Packages live in `go/<name>/<package>/` and commands in `go/<name>/cmd/<command>/`. A package at the root would be named after the domain, and would clash with the applications' own packages of the same name.
- `go/go.work` lists every module, so a change across modules builds and tests together locally.
- To try a change in an application before it is released, point the application at a local checkout with a `replace` directive, and remove the directive before the application's change merges.

## Dependencies

Every application that imports a module compiles its dependencies, so keep them few. When a heavy dependency serves only part of a domain, put that part in its own module so the rest does not pull it in. For example, the OpenFGA model parser brings in ANTLR and gRPC, so it stays out of `authn`.

## Constructors and fx

- Every package works without [fx](https://github.com/uber-go/fx). A plain constructor takes a config struct and returns the value or an error.
- fx support is a thin, separate package named `<name>fx`, for example `authnfx`, that wraps the plain constructors. It:
  - never provides types the application owns, such as the logger, the HTTP client or the OpenTelemetry providers;
  - never registers HTTP routes;
  - never reads configuration files or environment variables. The application provides the config struct.
- A module never provides nil. It builds its value, or fails startup with an error that names the missing setting. An empty setting never means "off".
- Turning a feature off is the application's decision. The application leaves the module out of its fx graph, based on a switch it reads before `fx.New`. A consumer that can run without the feature marks its dependency `optional:"true"`.

The reason for the last two rules: fx accepts nil for a dependency that is not optional. A module that provided nil would turn a mistyped setting into a crash on the first request, or into a feature that is silently off.

## Options

Optional behaviour is off by default and turned on by an explicit option.

## Errors

- Export a sentinel error for each condition a caller acts on, and wrap it with `%w` so that `errors.Is` finds it.
- Error messages start with the package name, for example `oidclogin: invalid configuration`.

## Go version

The `go` line of a `go.mod` names the oldest Go release the module supports, written as `go 1.N.0`.

## Versions

Tags, compatibility and the `/vN` rule are in [CONTRIBUTING.md](../CONTRIBUTING.md#versions-and-releases).
