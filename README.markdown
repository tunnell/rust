# Rust Stable for Xous

Build stable Rust binaries for Xous! This release targets Rust 1.97.0.

## Why this is a separate fork

The `riscv32imac-unknown-xous-elf` target lives upstream in rust-lang/rust, but
it is a [Tier 3 target](https://doc.rust-lang.org/rustc/platform-support.html) —
Rust ships no pre-compiled artifacts for it, so `rustup target add` alone gives
you no usable `std`. This fork's main job is to build and distribute that `std`:
each stable Rust release gets a `<version>-xous` branch whose CI builds the
library and publishes it as a versioned release zip
(`riscv32imac-unknown-xous_<version>.zip`) that you unzip into your sysroot (see
below).

It also carries the Xous-specific `std` pieces that aren't upstream yet — the
PDDB-backed filesystem backend (`library/std/src/sys/fs/xous.rs`), the path
backend (`library/std/src/sys/path/xous.rs`), and the `std::os::xous`
extensions. Against the matching upstream release, a `<version>-xous` branch is
only those few commits ahead and nothing behind; it is rebased forward for each
new Rust version.

## Installing Prebuilt Releases

1. Ensure you are running Rust 1.97.0. Future versions of Rust will need a different version of this software.
2. Download the latest release from the [releases](https://github.com/betrusted-io/rust/releases/latest) page
3. Unzip the zipfile to your Rust sysroot. On Unix systems can do this with something like:
```sh
cd $(rustc --print sysroot)
wget https://github.com/betrusted-io/rust/releases/latest/download/riscv32imac-unknown-xous_1.97.0.zip
rm -rf lib/rustlib/riscv32imac-unknown-xous-elf # Remove any existing version
unzip *.zip
rm *.zip
cd -
```

On Windows with Powershell you can run:

```powershell
Push-Location $(rustc --print sysroot)
if (Test-Path lib\rustlib\riscv32imac-unknown-xous-elf) { Remove-Item -Recurse -Force lib\rustlib\riscv32imac-unknown-xous-elf }
Invoke-WebRequest -Uri https://github.com/betrusted-io/rust/releases/latest/download/riscv32imac-unknown-xous_1.97.0.zip -Outfile toolchain.zip
Expand-Archive -DestinationPath . -Path toolchain.zip
Remove-Item toolchain.zip
Pop-Location
```

## Building From Source

1. Install a RISC-V toolchain, and ensure it's in your path. Set `CC` and `AR` to point to the toolchain's -gcc and -ar binaries.
2. Run `./rebuild.sh`. This will build libstd and install it.

## Building on Windows Powershell

On Windows, you can use the `rebuild.ps1` script to build and install this package. You will need
to have a Riscv compiler in your path.

Run `rebuild.ps1`. It is recommended that you run it under a new shell in order to avoid polluting your environment with Rust-specific variables:

```powershell
powershell .\rebuild.ps1
```
