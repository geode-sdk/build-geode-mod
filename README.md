# build-geode-mod

An easy-to-use GitHub Action for building Geode mods within GitHub Actions.

This repository contains two actions:
- One for building mods.
- Another for combining builds for multiple platforms into a single `.geode` file.

If you’re here to copy-paste the action, check out the [examples folder](https://github.com/geode-sdk/build-geode-mod/tree/main/examples).

---

## Usage

```yaml
- uses: geode-sdk/build-geode-mod@main
  with:
    # Which version of the SDK to use.
    # Set to 'nightly' for the latest commit, 'latest' for the latest release.
    # Default: latest
    sdk: ''

    # Which CLI version to use.
    # Default: latest
    cli: ''

    # Extra arguments passed to CMake when configuring. Not required.
    configure-args: ''

    # Extra arguments passed to CMake when building. Not required.
    build-args: ''

    # Which build configuration to use.
    # Default: Release
    build-config: ''

    # Whether to export PDB files for Windows builds. Not required.
    # Default: false
    export-pdb: ''

    # Path to the project to build. Defaults to the current directory.
    path: ''

    # Set this to true if you plan on merging .geode files later.
    # Default: false
    combine: ''

    # Geode target to build for. Can be one of:
    # "Win64", "MacOS", "Android32", "Android64".
    # Defaults to the appropriate platform for the runner:
    # - Win64 on Windows runners
    # - MacOS on macOS runners
    # - Android64 on Linux runners
    target: ''

    # Repository to use for bindings. Must be in the format "user/repo". Not required.
    bindings: ''

    # Which commit/branch to use for bindings. Defaults to the latest commit in the main branch. Not required.
    bindings-ref: ''

    # The Android minimum SDK version to target. Defaults to 23. Not required.
    android-min-sdk: ''

    # Whether to use Link Time Optimization (LTO) for smaller build size at the cost of longer build time.
    # Default: true
    use-lto: ''
```

---

## Examples

### Building and Uploading a Mod on Latest SDK

```yaml
- uses: geode-sdk/build-geode-mod@main
  id: build

- uses: actions/upload-artifact@v4
  with:
    name: My mod
    path: ${{ steps.build.outputs.build-output }}
```

### Building with `RelWithDebInfo`

```yaml
- uses: geode-sdk/build-geode-mod@main
  id: build
  with:
    build-config: RelWithDebInfo
    # Bundle the PDB inside the .geode file
    # Be aware that PDB files can be large
    export-pdb: true
```

---

## Combine

You can build mods for different platforms and then combine them into a single `.geode` file.

This process is usually done using a matrix. Due to GitHub Actions limitations, you will need to add a separate job to combine the builds.

Make sure to set `combine: true` in the build action.

### Example Workflow: Building and Combining for Multiple Platforms

To build a mod for macOS, Android (ARMv7 and ARMv8), and Windows, and then combine them, see the full workflow:

[Multi-Platform Example Workflow](https://github.com/geode-sdk/build-geode-mod/blob/main/examples/multi-platform.yml)

---
