# Slint Setup

|             Table of contents             |
|-------------------------------------------|
| [1. Introduction](#introduction)          |
| [2. Prerequisites](#prerequisites)        |
| [3. Steps](#steps)                        |
| [4. Optional](#optional)      |


## Introduction

As of writing, Slint documentation does not provide steps to setup your prject from scratch and instead have you download their templates. This document aims to address it and give you a quick start.

## Prerequisites

- Rust [installed](https://rust-lang.org/tools/install/) on your system
- Text editor of your choice
- Basic understanding of Rust language

## Steps

### 1. Create a new Rust project

- Example:

     ```bash
     cargo new --bin my_project
     ```

     Replace `my_project` with a name you desire.

### 2. Add **Slint** and **Slint Build** as dependencies

- Make sure you have the necessary dependencies in your `Cargo.toml` file:

- Example (via CLI):

    ```bash
    cargo add slint
    # Or add these features (see notes below)
    cargo add slint --features renderer-femtovg-wgpu,backend-winit
    
    cargo add slint-build --build
    ```
  - Notes:
    - (Optional) The `renderer-femtovg-wgpu` feature enables GPU acceleration using the femtovg renderer and wgpu backend.
    - (Optional) The `backend-winit` feature enables the winit backend for window management which `renderer-femtovg-wgpu` needs aside from **LinuxKMS** backend.
    - For more information, see [Backends and Renderers](https://docs.slint.dev/latest/docs/slint/guide/backends-and-renderers/backends_and_renderers/)

### 3. Create a Slint entry file and configure `main.rs`

- The entry file is typically a `.slint` file that contains the main component/body of your application's main window UI.

- Example:

    ```slint
    // ~/Projects/my_slint_app/ui/main_window.slint

    export component MainWindow inherits Window { // Take note of the component name `MainWindow`
        title: "Slint Samples";
        width: 400px;
        height: 300px;

        Text {
            text: "Hello, World!";
        }
    }
    ```

  - Then on your `main.rs` file, you can refer to the following code to run the application and render the UI you defined:

    ```rust
    // ~/Projects/my_slint_app/src/main.rs
    
    // Prevent console window in addition to Slint window in Windows release builds when, e.g., starting the app via file manager. Ignored on other platforms.
    #![cfg_attr(not(debug_assertions), windows_subsystem = "windows")]

    use std::error::Error;

    // This macro generates the necessary code to load and display the UI you just created
    slint::include_modules!();

    fn main() -> Result<(), Box<dyn Error>> {
        // Create an instance of the MainWindow component from main_window.slint
        let ui = MainWindow::new()?; 

        // Run the application and display the UI
        ui.run()?; 

        Ok(())
    }
    ```

### 4. Configure `build.rs`

- In order for the compiler to know where to find the entry Slint file, you need to configure the `build.rs` file.

- Example:

    ```rust
    // ~/Projects/my_slint_app/build.rs
    
    fn main() {
        // This compiles ths Slint files. You only need to specify the path to the entry slint file(s) (the main window file for example)
        slint_build::compile("ui/main_window.slint").expect("Slint build failed");
    }
    ```

### Troubleshooting

You might encounter a compile error with the message: ``error: failed to run custom build command for `yeslogic-fontconfig-sys v6.0.1` ``

In this case. You may need to install `pkgconf` and `fontconfig` as your system dependencies:

**Debian/Ubuntu:**
```bash
sudo apt install pkg-config libfontconfig1-dev
```

**Fedora/RHEL:**
```bash
sudo dnf install pkgconf-pkg-config fontconfig-devel
```

**Arch Linux:**
```bash
sudo pacman -S pkgconf fontconfig
```

After installation, run `cargo clean` and then `cargo run` again.

> `cargo clean` ensures your compile cache directory is clean and any potential corrupt files are removed, adding safety measure before your rerun of `cargo run`.

---

## Optional

Slint provides Material Design UI toolkit to help you build touch-friendly UI in your project. Refer to their [Getting Started](https://material.slint.dev/getting-started/) page to learn more.

---

## End Notes

Now, you should be able to run the application using `cargo run`. Initial compile time might be slow but after that, subsequent compiles are fast.

I also highly recommend reading the [official Slint documentation](https://docs.slint.dev/latest/docs/slint/) for more information on how to use Slint and build your frontend with it.

If you encounter any issues from following this guide, feel free to reach out by creating a new Github issue.
