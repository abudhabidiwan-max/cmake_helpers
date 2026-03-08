# cmake_helpers

CMake helper scripts.

A collection of small, focused CMake and supporting scripts designed to make common CMake workflows easier to maintain and reuse across projects. This repository provides helper macros, functions, and utilities that simplify finding dependencies, setting up targets, packaging rules, and developer tooling.

## Table of contents

- [Overview](#overview)
- [Why this repo exists](#why-this-repo-exists)
- [Features](#features)
- [Language composition](#language-composition)
- [Repository layout (typical)](#repository-layout-typical)
- [Installation / Adding to your project](#installation--adding-to-your-project)
- [Usage examples](#usage-examples)
  - [Loading helpers from a subdirectory](#loading-helpers-from-a-subdirectory)
  - [Common helper usages](#common-helper-usages)
- [Contributing](#contributing)
- [Testing](#testing)
- [License](#license)
- [Maintainers / Contact](#maintainers--contact)
- [Changelog](#changelog)

## Overview

This repo contains reusable CMake modules and a few supporting scripts (Python/C++) to ease cross-project configuration and reduce duplication in CMakeLists.txt files. The helpers aim to be:

- Minimal and easy to read
- Cross-platform where possible (Linux/macOS/Windows)
- Compatible with modern CMake (recommended 3.18+)
- Focused on common patterns (find/wrap libraries, target properties, packaging)

## Why this repo exists

CMake lists and modules tend to accumulate a lot of boilerplate across multiple repositories (target export logic, find wrappers, consistent target property settings, test helpers). This repo centralizes small, well-documented helpers so teams can share and maintain best practices in one place.

## Features

- Modular CMake helper files for common tasks (finding libraries, target setup, packaging assists)
- Example usage snippets for target creation and consumption
- Small CLI/utility scripts for developer workflows (format checks, generator helpers) — optional
- Guidelines and examples for packaging and cross-platform flags

## Language composition

This repository is primarily CMake with supporting C++, Python and a small amount of C.

- CMake: 64.5%
- C++: 28.2%
- Python: 6.1%
- C: 1.2%

## Repository layout (typical)

The actual layout in your repo may vary. Example structure this README assumes:

- cmake/                — CMake modules (.cmake) and helper files
  - Modules/            — modular find/utility scripts (Find*.cmake, helpers)
  - helpers.cmake       — central include that aggregates helpers
- examples/             — small example projects or usage snippets
- scripts/              — Python or shell utilities for dev tasks
- src/                  — optional C/C++ test utilities or examples
- tests/                — unit or integration tests for helpers
- README.md
- LICENSE

Adjust paths to match your repo structure.

## Installation / Adding to your project

There are multiple ways to consume these helpers. Use whichever fits your project's policies:

1. As a subdirectory (recommended for local use)
   - Copy or submodule the repo under `cmake/helpers` and include it:
     ```cmake
     list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/helpers")
     include(helpers)            # or include(Modules/helpers)
     ```

2. As a Git submodule
   - Add the repo as a submodule and reference the module path similarly.

3. As an installable package (advanced)
   - Export and install the cmake modules to a shared location and append that path to CMAKE_MODULE_PATH on consuming projects.

## Usage examples

### Loading helpers from a subdirectory

If you placed the helpers in `cmake/helpers`:

```cmake
# top-level CMakeLists.txt
list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/helpers")
include(helpers)  # this file should include/forward the specific helper modules you want
```

### Common helper usages

Below are example patterns you might find or adopt in these helper modules.

- Wrap a library find with consistent target creation:

```cmake
# Example: in Modules/FindMyLib.cmake (or via helper function)
find_package(MyLib REQUIRED)
if (MyLib_FOUND)
  add_library(MyLib::MyLib INTERFACE IMPORTED)
  set_target_properties(MyLib::MyLib PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES "${MyLib_INCLUDE_DIRS}"
    INTERFACE_LINK_LIBRARIES "${MyLib_LIBRARIES}"
  )
endif()
```

- Convenience macro to create a namespaced target with sane defaults:

```cmake
# Example helper: add_sane_library(<name> <sources...>)
function(add_sane_library name)
  cmake_parse_arguments(ARG "" "" "SOURCES" ${ARGN})
  add_library(${name} ${ARG_SOURCES})
  target_compile_features(${name} PUBLIC cxx_std_17)
  target_include_directories(${name} PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)
  # more consistent flags and properties...
endfunction()
```

- Packaging helper to create export and CMake config files:

```cmake
# Example: call in install step
include(CMakePackageConfigHelpers)
write_basic_package_version_file("${CMAKE_CURRENT_BINARY_DIR}/MyLibConfigVersion.cmake"
                                 VERSION ${PACKAGE_VERSION}
                                 COMPATIBILITY AnyNewerVersion)
install(EXPORT MyLibTargets
        FILE MyLibTargets.cmake
        NAMESPACE MyLib::
        DESTINATION lib/cmake/MyLib)
```

## Python / scripts

If the repo includes small Python utilities (6.1% of code), typical uses are:

- Formatting or lint-check wrappers
- Small code generators for CMake config fragments
- Developer tooling such as "update-deps.py" to refresh vendored files

Example usage:

```bash
python3 scripts/format-check.py
```

(Replace the script path with the actual path in this repo.)

## Contributing

Thanks for considering contributions! Suggested workflow:

- Open an issue describing the enhancement or bug
- Create a branch named `feat/<short-desc>` or `fix/<short-desc>`
- Follow the repository's formatting and style rules
- Add examples or tests for new helpers
- Open a pull request and reference the issue

Guidelines:
- Keep helpers small and focused
- Document public macros/functions with clear examples
- Maintain backward compatibility where possible

## Testing

- Add small example projects under `examples/` that exercise helper behavior.
- If you have CI (recommended), run CMake configure + build steps for multiple generators (Ninja, Unix Makefiles) on Linux/macOS/Windows.
- For Python scripts, include a small test harness or use pytest/unittest if needed.

## License

Specify a license in a LICENSE file (e.g., MIT, Apache-2.0). If the repo doesn't have one yet, add one to clarify reuse.

## Maintainers / Contact

Maintainer: abudhabidiwan-max

(If you'd like, maintainers can add an email or GitHub profile link here.)

## Changelog

- Initial README describing purpose, layout, and examples.
