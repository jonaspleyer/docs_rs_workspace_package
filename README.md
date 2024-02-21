This repo is only supposed to showcase how building workspace packages is sometimes not possible with the current docs.rs setup.

## Structure
```bash
$ tree
.
├── Cargo.toml
├── my_lib
│   ├── Cargo.toml
│   └── src
│       └── lib.rs
└── README.md

3 directories, 4 files
```
The actual contents of `my_lib` do not matter, only the two configuration files.
```
$ cat Cargo.toml
[workspace]
members = [
        "my_lib",
]

[workspace.package]
version = "0.1.0"
```
and
```bash
$ cat my_lib/Cargo.toml
[package]
name = "my_lib"
version.workspace = true

[dependencies]
```

## Building

The build command
```bash
cargo run -- build crate -l path/to/docs_rs_workspace_package/my_lib
```
fails with
```bash
Error: Building documentation failed

Caused by:
    Building documentation failed

Caused by:
    invalid Cargo.toml syntax
```
which makes sense due to
```toml
version.workspace = true
```

## Fix
However when running the following sequence of commands
```bash
# Run this in the directory of docs_rs_workspace_package
cargo package -p my_lib
```
and then building again
```bash
# Run this from the docs.rs repo
cargo run -- build crate -l path/to/docs_rs_workspace_package/target/package/my_lib-0.1.0
```
then the build succeeds.
