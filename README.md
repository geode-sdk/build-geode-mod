# build-geode-mod
An easy to use action to build a geode mod within github actions.

This repository contains two actions, one for just building the mods, and another for combining builds for multiple platforms into a single .geode file.

If you're just here to copy paste the action, look at the examples folder: \
https://github.com/geode-sdk/build-geode-mod/tree/main/examples

# Usage

For more notes on some options, see the descriptions in the [workflow](action.yml).
```yml
- uses: geode-sdk/build-geode-mod@main
  with:
    # Which version of the SDK to use. Set to "nightly" for latest commit, "latest" for latest release, "given" for the version in the mod.json.
    # Default: given
    sdk: ''

    # Which CLI version to use.
    # Default: latest
    cli: ''

    # Extra arguments passed to CMake when configuring.
    configure-args: ''

    # Extra arguments passed to CMake when building.
    build-args: ''

    # Which build configuration to use.
    # Default: Release
    build-config: ''

    # Path to the project which to build.
    # Default: Current directory (./)
    path: ''

    # Set this to true if you plan on merging .geode files later. See the README for more info.
    # Default: false
    combine: ''

    # Geode target to build for. Can be either "Win64", "MacOS", "iOS", "Android32" or "Android64".
    # Default: What is appropriate for the current platform, so:
    #  - Win64 on Windows runners
    #  - MacOS on MacOS runners
    #  - Android64 on Linux runners
    target: ''

    # What repository to use for bindings. Must be in the format "user/repo".
    # Default: geode-sdk/bindings
    bindings: ''

    # Which commit/branch to use for bindings.
    # Default: Latest commit on the main branch
    bindings-ref: ''

    # The android min SDK version to target.
    # Default: 23
    android-min-sdk: ''

    # Version of the Android NDK to use.
    # Default: Latest version
    android-ndk-version: ''

    # The ccache variant to use (empty, ccache or sccache).
    # Default: sccache on Windows and Android, empty otherwise
    android-ndk-version: ''

    # Whether to cache CPM sources.
    # Default: true
    use-cpm-cache: ''

    # Whether to export PDB files for Windows builds.
    # Default: false
    export-pdb: ''

    # Whether to export debug files for builds (.pdb for Windows, .dbg for Android, .dSYM for MacOS).
    # Default: false
    export-debug: ''

    # Whether to use Link Time Optimization, improving build size at the cost of build time.
    # Default: true
    use-lto: ''

    # Whether to bundle PDB files into the .geode package.
    # Requires SDK v4.2.0 and CLI v3.3.0 at minimum.
    # Default: false
    bundle-pdb: ''

    # Whether to export Breakpad symbols for Android builds.
    # Default: false
    export-symbols: ''
```

# Examples

## Building and uploading a mod on latest sdk
```yml
- uses: geode-sdk/build-geode-mod@main
  id: build

- uses: actions/upload-artifact@v3
  with:
    name: My mod
    path: ${{ steps.build.outputs.build-output }}
```

## Building with RelWithDebInfo
```yml
- uses: geode-sdk/build-geode-mod@main
  id: build
  with:
    build-config: RelWithDebInfo
    # Export the pdb alongside the .geode file
    export-pdb: true
    # Bundle the pdb inside the .geode file
    # Be warned, they can be quite big
    bundle-pdb: true
```

# Combine
It is also possible to build mods for different platforms, and then afterwards combine them into a single .geode file.

Usually this is done using a matrix, and due to limitations on how much actions can do, you will have to add another job for the combining.

To do this, make sure to set `combine: true` on the build action!

## Building a mod on mac, ios, android armv7 and armv8, windows, and then combining it
Full workflow: \
https://github.com/geode-sdk/build-geode-mod/blob/main/examples/multi-platform.yml
