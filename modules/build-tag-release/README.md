# ACore Module Build and Release

A reusable GitHub Action for building, checking, and releasing AzerothCore modules. This composite action streamlines the process of compiling your AzerothCore modules, performing static code analysis, and optionally tagging a new release upon successful builds.

## Features

- **Docker Layer Caching**: Speeds up build times by caching Docker layers.
- **Code Checkout**: Checks out your repository code.
- **Module Management**: Copies the module to the AzerothCore container.
- **Building with Caching**: Compiles the modules with caching optimizations.
- **Static Code Analysis**: Runs `cppcheck` to analyze code quality.
- **Optional Release Tagging**: Automatically tags a new release if the build succeeds.

## Inputs

| Input               | Description                                                         | Required | Default  |
|---------------------|---------------------------------------------------------------------|----------|----------|
| `GITHUB_TOKEN`      | GitHub token with appropriate permissions.                           | Yes      | N/A      |
| `tag_release`       | Whether to tag a release if the build is successful.                | No       | `false`  |
| `bump_version_scheme` | The version bump scheme to use (e.g., `minor`, `major`, `patch`).  | No       | `minor`  |

## Usage

To use this composite action in your workflow, follow these steps:

1. **Add the Composite Action to Your Repository**

   Ensure that the composite action is available in a repository (e.g., `araxiaonline/acore-module-build-and-release`) or within your own repository under `.github/actions/acore-module-build-and-release`.

2. **Reference the Action in Your Workflow**

   Below is an example of how to incorporate the `ACore Module Build and Release` action into your GitHub Actions workflow.

```yaml
   name: ACore Module Build and Release

   on:
     push:
       branches:
         - main
     pull_request:
       branches:
         - main

   jobs:
     build-and-release:
       runs-on: ubuntu-latest
       container:
         image: ghcr.io/araxiaonline/ac-wotlk-worldserver-devcontainer:latest
         options: --user root
       steps:
         - name: Run ACore Module Build and Release
           uses: araxiaonline/acore-module-build-and-release@v1.0.0
           with:
             GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
             tag_release: "true" # Set to "false" to skip tagging releases
             bump_version_scheme: "minor" # Options: "minor", "major", "patch"
```

## Action Steps

The `ACore Module Build and Release` action performs the following steps:

1. **Cache Docker Layers**: Utilizes `actions/cache@v3` to cache Docker layers, speeding up subsequent builds.
2. **Checkout Code**: Uses `actions/checkout@v4` to clone the repository code into the workflow.
3. **Get Repository Name**: Extracts the repository name and sets it as an environment variable `REPO_NAME`.
4. **Copy Module to Container**: Copies the module code into the AzerothCore container's modules directory.
5. **Rebuild Modules with Caching**: Navigates to the build directory and compiles the modules using CMake with caching optimizations.
6. **Install cppcheck**: Installs `cppcheck` for static code analysis.
7. **Run cppcheck**: Executes `cppcheck` on the module code to identify potential issues. If issues are found, the workflow fails.
8. **Tag Release on Success**: If `tag_release` is set to `true` and all previous steps succeed, the action uses `rymndhng/release-on-push-action@master` to tag a new release based on the specified `bump_version_scheme`.
