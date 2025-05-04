# Rust Installation Profiles Comparison

| Profile      | Included Components                                                                                                                | Approximate Disk Usage (Linux x86\_64) | Approximate Disk Usage (Windows x86\_64) | Recommended Use Cases                                                                                          |                            |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------- | ---------------------------------------- | -------------------------------------------------------------------------------------------------------------- | -------------------------- |
| **minimal**  |                                                                                                                                    | \~449 MiB                              | \~500–600 MiB (estimated)                | Continuous Integration (CI) environments, systems with limited storage, or when only core tools are needed.    |                            |
| **default**  | All components in `minimal`, plus:<br>- `rust-docs` (Offline documentation)<br>- `rustfmt` (Code formatter)<br>- `clippy` (Linter) | \~1.2 GiB                              | \~1.3–1.5 GiB (estimated)                | General development, providing essential tools for code formatting and linting.                                |                            |
| **complete** | All components available through `rustup`, including:<br>- Development tools like `miri`, `rust-analyzer`, etc.                    | Varies; often fails due to size        | Varies; often fails due to size          | Not recommended for general use; intended for testing all components, which may lead to installation failures. |  |

### Platform-Specific Considerations

**Linux:**

-   Disk usage estimates are based on actual installations.
-   The `minimal` profile is suitable for CI environments or systems with limited storage.


**Windows:**

-   Disk usage estimates are approximate and may vary based on system configuration.
-   The `minimal` profile is recommended if you don't require local documentation, as the large number of files can cause issues with some antivirus systems .[Rust Programming Language+1The Rust Programming Language Forum+1](https://rust-lang.github.io/rustup/concepts/profiles.html?utm_source=chatgpt.com)


### Recommendations

-   **minimal**: Choose this profile for CI setups or environments where disk space is a constraint. It provides the essential tools required to compile and build Rust projects.
-   **default**: This is the recommended profile for most developers. It includes additional tools like `rustfmt` and `clippy`, which are useful for maintaining code quality and consistency.
-   **complete**: Generally not recommended, as it attempts to install every available component, which can lead to installation failures due to the sheer number of components. If you need specific additional tools, it's better to start with the `default` profile and add the necessary components manually using `rustup component add`.


### Installation Commands

To install Rust with a specific profile:

```bash
rustup-init --profile minimal
```

To set the profile globally after installation:

```bash
rustup set profile default
```

For project-specific configurations, specify the profile in a `rust-toolchain.toml` file:

```toml
[toolchain]
channel = "stable"
profile = "minimal"
components = ["clippy", "rustfmt"]
```

This custom Rust training was created by IQSOFT - EduTech/gabor for Ericsson – © 2025. 
All materials are exclusively for use by participants of the training. Sharing or using these materials outside of the training is not permitted without written permission from IQSOFT - EduTech.