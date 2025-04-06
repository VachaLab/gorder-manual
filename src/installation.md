# Installation

Installing the `gorder` tool consists of the following steps:

1. **Install Rust.**
    - Follow [this guide](https://www.rust-lang.org/tools/install) for your operating system.
    - If you already have Rust installed, you can skip this step. To check, run `rustc --version`. 
    > You need Rust v1.82 or newer to be able to install `gorder`. To update Rust, run `rustup update`.

2. **Install `gorder`.**
    ```bash
    cargo install gorder
    ```

You can verify that the installation was successful by running `gorder --version`. The current version of the `gorder` tool should be displayed in the terminal. If everything went right, you can now call `gorder` from anywhere.

***

## Troubleshooting
Below are some common errors you might encounter when installing `gorder` on Linux. If you are still unable to proceed, please [open an issue on GitHub](https://github.com/Ladme/gorder/issues) and provide details about the problem. 

> Unfortunately, we **cannot** provide support for installing `gorder` on Windows or macOS, and we apologize for any inconvenience this may cause.

### Command not found: `cargo`
If, after installing Rust, you are unable to run `cargo install gorder` and receive an error like `command not found: cargo`, it likely means that `cargo` is not included in your PATH. Try closing and reopening your terminal. 

If this does not help and you are using `bash`, try adding the following line to your `.bashrc` file (typically located in your home directory, `~`): `export PATH="$HOME/.cargo/bin:$PATH"`. Then restart the terminal again.