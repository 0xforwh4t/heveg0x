# heveg0x


# Cross-Compile Rust for Windows on Linux

Compile a Rust project on Linux and generate a native Windows `.exe` executable Windows.

This guide uses the **`x86_64-pc-windows-gnu`** Rust target with the MinGW-w64 cross-compilation toolchain to build 64-bit Windows applications.

## Table of Contents

- [Requirements](#requirements)
- [1. Install Dependencies](#1-install-dependencies)
- [2. Install Rust](#2-install-rust)
- [3. Add the Windows Target](#3-add-the-windows-target)
- [4. Configure the Windows Linker](#4-configure-the-windows-linker)
- [5. Build the Windows Executable](#5-build-the-windows-executable)
- [6. Verify the Executable](#6-verify-the-executable)
- [7. Troubleshooting](#7-troubleshooting)

## Requirements

- Debian Linux
- Rust and Cargo
- MinGW-w64 cross-compiler
- A Rust project containing `Cargo.toml`

## 1. Install Dependencies

Update your package lists and install the required build tools:

```bash
sudo apt update

sudo apt install -y \
    curl \
    build-essential \
    pkg-config \
    gcc-mingw-w64-x86-64 \
    binutils-mingw-w64-x86-64 \
    file
```

Verify that the Windows cross-compiler is available:

```bash
x86_64-w64-mingw32-gcc --version
```

## 2. Install Rust

If Rust is not already installed, install it using `rustup`:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Load the Rust environment:

```bash
source "$HOME/.cargo/env"
```

Verify the installation:

```bash
rustc --version
cargo --version
```

## 3. Add the Windows Target

Install the Rust compilation target for 64-bit Windows:

```bash
rustup target add x86_64-pc-windows-gnu
```

Confirm that the target is installed:

```bash
rustup target list --installed
```

Expected output includes:

```text
x86_64-pc-windows-gnu
```

## 4. Configure the Windows Linker

Navigate to your Rust project directory, where `Cargo.toml` is located:

```bash
cd ~/my-rust-project
```

Create the Cargo configuration directory and file:

```bash
mkdir -p .cargo
nano .cargo/config.toml
```

Add the following configuration:

```toml
[target.x86_64-pc-windows-gnu]
linker = "x86_64-w64-mingw32-gcc"
```

Save the file.

This configuration tells Cargo to use the MinGW-w64 cross-compiler when linking Windows binaries.

## 5. Build the Windows Executable

Build your project in release mode:

```bash
cargo build --release --target x86_64-pc-windows-gnu
```

After a successful build, the executable will be located under:

```text
target/x86_64-pc-windows-gnu/release/
```

For example:

```text
target/x86_64-pc-windows-gnu/release/my_rust_project.exe
```

The actual filename depends on the binary target name defined by your Rust project.

To list generated executables:

```bash
find target/x86_64-pc-windows-gnu/release \
    -maxdepth 1 -type f -name '*.exe'
```

## 6. Verify the Executable

Check the generated binary's file format:

```bash
file target/x86_64-pc-windows-gnu/release/*.exe
```

A successful build should be identified as a Windows PE executable, typically `PE32+` for a 64-bit binary.

Inspect the executable's architecture and headers:

```bash
x86_64-w64-mingw32-objdump -f \
    target/x86_64-pc-windows-gnu/release/my_rust_project.exe
```

Replace `my_rust_project.exe` with your actual executable filename.

**Note:** Successful cross-compilation does not guarantee that every dependency is compatible with the Windows target. Dependencies containing native code may require additional Windows libraries or build tools.

## 7. Troubleshooting

### Linker not found

Check that MinGW-w64 is installed:

```bash
command -v x86_64-w64-mingw32-gcc
```

If the command returns nothing, reinstall the toolchain:

```bash
sudo apt install --reinstall gcc-mingw-w64-x86-64
```

### Windows target not installed

Run:

```bash
rustup target add x86_64-pc-windows-gnu
```

### Build fails because of a dependency

Check whether the dependency supports Windows GNU targets. Some crates require platform-specific configuration, native libraries, or additional cross-compilation tools.

### Clean and rebuild

If you need to remove previous build artifacts:

```bash
cargo clean
cargo build --release --target x86_64-pc-windows-gnu
```

## Summary

The essential commands are:

```bash
sudo apt update
sudo apt install -y build-essential pkg-config \
    gcc-mingw-w64-x86-64 binutils-mingw-w64-x86-64

rustup target add x86_64-pc-windows-gnu

cargo build --release --target x86_64-pc-windows-gnu
```

Your Windows executable will be generated in:

```text
target/x86_64-pc-windows-gnu/release/
```

**Result:** A native 64-bit Windows `.exe` compiled from your Rust source code on Debian Linux.
