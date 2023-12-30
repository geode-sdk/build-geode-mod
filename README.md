# build-geode-mod
An easy to use action to build a geode mod within github actions.

This repository contains two actions, one for just building the mods, and another for combining builds for multiple platforms into a single .geode file.

# Usage
```yml
- uses: geode-sdk/build-geode-mod@main
  with:
    # Which version of the SDK to use. Use nightly to specify latest commit
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

    # Path to the project which to build. Defaults to current directory.
    path: ''

    # Set this to true if you plan on merging .geode files later. See the README for more info.
    # Default: false
    combine: ''

    # Geode target to build for. Can be either "Win32", "MacOS", "Android32" or "Android64".
    # Defaults to what is appropriate for the current platform, so:
    # Defaults to Win32 on windows runners
    # Defaults to MacOS on macOS runners
    # Defaults to Android32 on linux runners
    target: ''
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
Note: the pdb is discarded for now
```yml
- uses: geode-sdk/build-geode-mod@main
  id: build
  with:
    build-config: RelWithDebInfo
```

# Combine
It is also possible to build mods for different platforms, and then afterwards combine them into a single .geode file.

Usually this is done using a matrix, and due to limitations on how much actions can do, you will have to add another job for the combining.

To do this, make sure to set `combine: true` on the build action!

## Building a mod on mac, android armv7 and armv8, windows, and then combining it
Full workflow:
```yml
name: Build Geode Mod

on:
  workflow_dispatch:
  push:
    branches:
      - "main"

jobs:
  build:
    strategy:
      fail-fast: false
      matrix:
        config:
        - name: Windows
          os: windows-latest
          
        - name: macOS
          os: macos-latest

        - name: Android32
          os: ubuntu-latest
          target: Android32

        - name: Android64
          os: ubuntu-latest
          target: Android64

    name: ${{ matrix.config.name }}
    runs-on: ${{ matrix.config.os }}

    steps:
      - uses: actions/checkout@v3

      - name: Build the mod
        uses: geode-sdk/build-geode-mod@main
        with:
          combine: true
          target: ${{ matrix.config.target }}
      
  package:
    name: Package builds
    runs-on: ubuntu-latest
    needs: ['build']

    steps:
      - uses: geode-sdk/build-geode-mod@combine
        id: build

      - uses: actions/upload-artifact@v3
        with:
          name: Build Output
          path: ${{ steps.build.outputs.build-output }}

```
