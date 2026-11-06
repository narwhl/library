# narwhl/library

Libraries shared by the applications we maintain. Code moves here once more than one application needs it, so it is fixed, reviewed and tested in one place.

No module has been released yet.

## Layout

Each language has its own top-level directory. Go is the only language so far.

```text
library/
├── go/
│   ├── go.work            lists every Go module, for local development
│   └── <name>/            one Go module per domain, for example authn
│       ├── go.mod         module pkg.narwhl.dev/<name>
│       ├── <package>/     importable packages
│       └── cmd/<command>/ command-line tools
├── CONTRIBUTING.md
└── LICENSE
```

## Using a Go module

Each module is versioned and released on its own. Add one to an application with:

```sh
go get pkg.narwhl.dev/<name>@latest
```

and import its packages by their full path, for example `pkg.narwhl.dev/authn/oidclogin`.

## Documents

- [CONTRIBUTING.md](CONTRIBUTING.md): what belongs here, how to propose a change, and how versions are released.
- [go/README.md](go/README.md): the layout and design rules for Go modules.
