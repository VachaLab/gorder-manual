# Installation

Installing the `gorder` tool consists of the following steps:

1. **Install `cmake`.**
    - It is highly likely that `cmake` is already installed on your system. You can confirm this by running `cmake --version`. Ensure that you have version 3 or higher.
    - If `cmake` is not installed, you can install it on Debian-based Linux distributions (e.g., Debian, Ubuntu, Linux Mint, Pop!_OS) using:
      ```bash
      sudo apt-get install cmake
      ```
      For other operating systems, or if you do not have `sudo` rights, visit the [official `cmake` download page](https://cmake.org/download) to obtain the appropriate version.

2. **Install Rust.**
    - Follow [this guide](https://www.rust-lang.org/tools/install) for your operating system.
    - If you already have Rust installed, you can skip this step. To check, run `rustc --version`. 
    > You need Rust v1.82 or newer to be able to install `gorder`. To update Rust, run `rustup update`.

3. **Install `gorder`.**
    ```bash
    cargo install gorder
    ```

You can verify that the installation was successful by running `gorder --version`. The current version of the `gorder` tool should be displayed in the terminal. If everything went right, you can now call `gorder` from anywhere.

***

## Troubleshooting
Below are some common errors you might encounter when installing `gorder` on Linux. If you are still unable to proceed, please [open an issue on GitHub](https://github.com/Ladme/gorder/issues) and provide details about the problem. 

> Unfortunately, we **cannot** provide support for installing `gorder` on Windows or macOS, and we apologize for any inconvenience this may cause.

### Failed to run custom build command
If, during installation of `gorder`, you encounter an error like `error: failed to run custom build command for chemfiles-sys (...)`, it indicates that `cmake` is not installed. You need to install it as specified above.

### Command not found: `cargo`
If, after installing Rust, you are unable to run `cargo install gorder` and receive an error like `command not found: cargo`, it likely means that `cargo` is not included in your PATH. Try closing and reopening your terminal. 

If this does not help and you are using `bash`, try adding the following line to your `.bashrc` file (typically located in your home directory, `~`): `export PATH="$HOME/.cargo/bin:$PATH"`. Then restart the terminal again.