# build-geode-mod
An easy to use action to build a geode mod within github actions.

This repository contains two actions, one for just building the mods, and another for combining builds for multiple platforms into a single .geode file.

If you're just here to copy paste the action, look at the examples folder: \
https://github.com/geode-sdk/build-geode-mod/tree/main/examples

# Usage
```yml
- uses: geode-sdk/build-geode-mod@main
  with:
    # Which version of the SDK to use.
    # Set to 'nightly' for latest commit, 'latest' for latest release
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

    # Whether to bundle PDB files into the .geode package. Not required.
    # Requires SDK v4.2.0 and CLI v3.3.0 at minimum.
    # Default: false
    bundle-pdb: ''

    # Path to the project which to build. Defaults to current directory.
    path: ''

    # Set this to true if you plan on merging .geode files later. See the README for more info.
    # Default: false
    combine: ''

    # Geode target to build for. Can be either "Win64", "MacOS", "Android32" or "Android64".
    # Defaults to what is appropriate for the current platform, so:
    # Defaults to Win64 on windows runners
    # Defaults to MacOS on macOS runners
    # Defaults to Android64 on linux runners
    target: ''

    # What repository to use for bindings. Must be in the format "user/repo". Not required.
    bindings: ''

    # Which commit/branch to use for bindings. Defaults to latest commit in main branch. Not required.
    bindings-ref: ''

    # The android min SDK version to target. Defaults to 23. Not required.
    android-min-sdk: ''

    # Whether to use Link Time Optimization, improving build size at the cost of build time.
    # Default: true
    use-lto: ''
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

## Building a mod on mac, android armv7 and armv8, windows, and then combining it
Full workflow: \
https://github.com/geode-sdk/build-geode-mod/blob/main/examples/multi-platform.yml
