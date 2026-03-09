# Contributing to SGDynamicTextAssets

Thank you for your interest in contributing to the SGDynamicTextAssets plugin! This guide will help you get set up and walk you through the contribution process.

## Table of Contents

- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [Module Architecture](#module-architecture)
- [Coding Standards](#coding-standards)
- [Commit Convention](#commit-convention)
- [Branching and Pull Requests](#branching-and-pull-requests)
- [Testing](#testing)
- [Documentation](#documentation)
- [Licensing](#licensing)

---

## Getting Started

1. **Fork** the repository on GitHub: https://github.com/Start-Games-Open-Source/SGDynamicTextAssets
2. **Clone** your fork with submodules (the plugin depends on fkYAML as a git submodule):

   ```bash
   git clone --recurse-submodules https://github.com/<your-username>/SGDynamicTextAssets.git
   ```

3. **Add the upstream remote** so you can stay up to date:

   ```bash
   cd SGDynamicTextAssets
   git remote add upstream https://github.com/Start-Games-Open-Source/SGDynamicTextAssets.git
   ```

4. **Create a branch** for your work (see [Branching and Pull Requests](#branching-and-pull-requests)).

> **Tip:** If you want to test the plugin in a project, clone or fork the [Example_SGDTA](https://github.com/Start-Games-Open-Source/Example_SGDTA) project and replace the `Plugins/SGDynamicTextAssets` submodule with a symlink or copy of your fork.

## Development Setup

### Requirements

- **Unreal Engine 5.6+** (source or launcher build)
- A C++ IDE supported by Unreal Engine (Visual Studio 2022, Rider, etc.)

### Building

1. Place the plugin in your project's `Plugins/` directory (or use the Example_SGDTA project).
2. Generate project files:

   ```bash
   # Windows (from your project root)
   UnrealBuildTool.exe -projectfiles -project="MyProject.uproject" -game -engine
   ```

   Or right-click the `.uproject` file and select **Generate Visual Studio project files**.

3. Open the generated solution and build in **Development Editor** configuration.
4. Launch the editor and verify the plugin is enabled under **Edit > Plugins > SGDynamicTextAssets**.

### Verifying Your Setup

- Open **Window > Developer Tools > Session Frontend > Automation**.
- Filter by `SGDynamicTextAssets`.
- Run all tests. They should all pass before you begin making changes.

## Module Architecture

The plugin has two modules. Understanding which module your change belongs in is important:

| Module | Load Phase | Ships In | Purpose |
|--------|-----------|----------|---------|
| **SGDynamicTextAssetsRuntime** | PreDefault | Packaged builds | Core types, serializers, subsystem, file management, server interface, Blueprint statics |
| **SGDynamicTextAssetsEditor** | Default | Editor only | Browser UI, editor UI, reference viewer, property customizations, cook pipeline, commandlets, tests |

**Rules of thumb:**

- If it needs to exist at runtime in a packaged game, it goes in **Runtime**.
- If it's UI, tooling, or only needed during development, it goes in **Editor**.
- **Editor** depends on **Runtime**, never the other way around.
- Tests go in `SGDynamicTextAssetsEditor/Private/Tests/`.

See the [File Structure](Documentation/Reference/FileStructure.md) reference for the complete directory layout.

## Coding Standards

Follow the existing conventions in the codebase:

### Naming

| Type | Convention | Example |
|------|-----------|---------|
| UObject classes | `U` prefix + `SG` prefix | `USGDynamicTextAsset` |
| Structs | `F` prefix + `SG` prefix | `FSGDynamicTextAssetId` |
| Interfaces | `I` prefix + `SG` prefix | `ISGDynamicTextAssetProvider` |
| Enums | `E` prefix + `SG` prefix | `ESGLoadResult` |
| Slate widgets | `S` prefix + `SG` prefix | `SSGDynamicTextAssetBrowser` |
| Editor toolkits | `F` prefix + `SG` prefix | `FSGDynamicTextAssetEditorToolkit` |

### Files

- Header and source files match the class name (e.g., `SGDynamicTextAsset.h` / `SGDynamicTextAsset.cpp`).
- Public headers go in `Public/`, private implementation goes in `Private/`.
- Organize files into subdirectories by functionality (Core, Subsystem, Management, Serialization, etc.).

### Copyright Header

Every source file must include the copyright header as the first line:

```cpp
// Copyright Start Games, Inc. All Rights Reserved.
```

All contributions are made under the Start Games copyright. This is intentional and benefits everyone involved:

- **Shields contributors from legal liability.** If someone violates the Elastic License 2.0 (for example, reselling the plugin), Start Games handles enforcement as the sole copyright holder. Individual contributors are never named in or dragged into legal disputes over their contributed code.
- **Simplifies license enforcement.** With a single copyright holder, Start Games can act decisively to protect the project. Fragmented copyright across many contributors would require coordinating with every contributor who owns a piece, which is impractical.
- **Maintains a clean IP chain.** A unified copyright makes it unambiguous that all code falls under the Elastic License 2.0. There is no question of which contributor licensed which file under what terms.
- **Preserves relicensing flexibility.** If Start Games ever adjusts the license (for example, moving to a more permissive one), a single copyright holder can do so without needing written consent from every past contributor but we'd probably just update the Elastic License to the latest version keeping the same core tenants of trying to benefit everybody.
- **Follows established open source practice.** Many major projects (Apache Foundation, Google, Meta) use the same approach where contributions are made under the project's copyright, not the individual's. Its a safe practice essentially.

### General

- Follow standard [Unreal Engine Coding Standards](https://dev.epicgames.com/documentation/en-us/unreal-engine/epic-cplusplus-coding-standard-for-unreal-engine).
- Use `PCHUsageMode::UseExplicitOrSharedPCHs`.
- No hard asset references in DTA classes. The UHT plugin enforces this at compile time.
- Prefer soft references (`TSoftObjectPtr`, `TSoftClassPtr`) and `FSGDynamicTextAssetRef`.

## Commit Convention

This project uses [Conventional Commits](https://www.conventionalcommits.org/). Every commit message should follow this format:

```
<type>(<scope>): <short description>

[optional body]

[optional footer]
```

### Types

| Type | When to use |
|------|-------------|
| `feat` | A new feature or capability |
| `fix` | A bug fix |
| `docs` | Documentation changes only |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `test` | Adding or updating tests |
| `chore` | Build process, CI, tooling, or other maintenance |

### Scopes

Use the module or area of the plugin as the scope:

- `runtime`: SGDynamicTextAssetsRuntime module
- `editor`: SGDynamicTextAssetsEditor module
- `serialization`: Serializers (JSON, XML, YAML, Binary)
- `cook`: Cook pipeline and commandlets
- `browser`: DTA Browser UI
- `server`: Server interface and cache
- `docs`: Documentation files

### Examples

```
feat(serialization): add TOML serializer support
fix(editor): prevent crash when renaming a DTA with an open editor
docs(runtime): clarify async loading thread safety in Subsystem.md
test(serialization): add roundtrip tests for XML nested properties
refactor(runtime): simplify FileManager path resolution logic
chore: update fkYAML submodule to latest release
```

## Branching and Pull Requests

### Branch Naming

Create a branch from `main` using one of these prefixes:

| Prefix | Use |
|--------|-----|
| `feat/` | New features (e.g., `feat/toml-serializer`) |
| `fix/` | Bug fixes (e.g., `fix/yaml-empty-data-crash`) |
| `docs/` | Documentation changes (e.g., `docs/update-quickstart`) |
| `test/` | Test additions or fixes (e.g., `test/xml-nested-props`) |
| `refactor/` | Refactoring (e.g., `refactor/filemanager-paths`) |
| `chore/` | Maintenance (e.g., `chore/update-fkyaml`) |

### Pull Request Process

1. **Sync with upstream** before opening your PR:

   ```bash
   git fetch upstream
   git rebase upstream/main
   ```

2. **Push your branch** to your fork:

   ```bash
   git push -u origin feat/my-feature
   ```

3. **Open a Pull Request** against `main` on the upstream repository.
4. **Fill out the PR template.** Describe your changes, link the related issue, explain what you tested, and check the licensing box.
5. A maintainer will review your PR. Be prepared for feedback and follow-up commits.

### Code Review Expectations

- All PRs require at least one maintainer review before merging.
- PRs must compile without warnings and pass all existing tests.
- New features should include tests and documentation updates where applicable.
- Keep PRs focused. One feature or fix per PR. If your change touches multiple areas, consider splitting it or consulting a moderator.

## Testing

The plugin includes 19 unit tests built on Unreal's Automation Framework. All tests are in `Source/SGDynamicTextAssetsEditor/Private/Tests/`.

### Running Tests

1. Open **Window > Developer Tools > Session Frontend**.
2. Go to the **Automation** tab.
3. Filter by `SGDynamicTextAssets` to see all plugin tests.
4. Select tests and click **Start Tests**.

### Test Conventions

- **Test class naming**: `FClassName_FunctionName_ExpectedBehavior`
- **Test path**: `SGDynamicTextAssets.Module.Class.Function`
- **Guard**: Wrap all tests in `#if WITH_AUTOMATION_WORKER` / `#endif`
- **Test flags**: `EAutomationTestFlags::EditorContext | EAutomationTestFlags::ProductFilter`

### What to Test

- **Bug fixes**: Add a test that reproduces the bug and verifies the fix.
- **New features**: Add tests covering the main functionality and edge cases.
- **Serializers**: Include roundtrip tests (serialize then deserialize, verify equality).

See [Automated Tests](Documentation/Testing/AutomatedTests.md) for complete test coverage details.

## Documentation

The plugin has comprehensive documentation in the `Documentation/` directory. If your change affects behavior, APIs, or workflows:

- Update the relevant documentation page(s).
- If you add a new class, struct, or enum, add it to the [Class Reference](Documentation/Reference/ClassReference.md).
- If you add a new file or directory, update [File Structure](Documentation/Reference/FileStructure.md).
- Follow the existing documentation style. See any page in `Documentation/` for reference.

## Licensing

SGDynamicTextAssets is licensed under the [Elastic License 2.0](./LICENSE).

By submitting a contribution to this project, you agree that your contribution is submitted under the same Elastic License 2.0 that covers the project, and you have the right to submit it. You also confirm that you are not submitting code that is owned by a third party unless that code is compatible with the Elastic License 2.0.

If your contribution includes third-party code or dependencies, please note them in your PR description so they can be reviewed for license compatibility.

---

Thank you for contributing to SGDynamicTextAssets!
