![A GitHub Action for generating Swift DocC documentation](https://raw.githubusercontent.com/space-code/build-docc/main/resources/build-docc.png)

<h1 align="center" style="margin-top: 0px;">build-docc</h1>

<p align="center">
<a href="https://github.com/space-code/build-docc/blob/main/LICENSE"><img alt="Licence" src="https://img.shields.io/badge/License-MIT-yellow.svg"></a> 
<a href="https://github.com/space-code/build-docc/actions/workflows/ci.yml"><img alt="CI" src="https://github.com/space-code/build-docc/actions/workflows/ci.yml/badge.svg?branch=main"></a>
</p>

## Description

build-docc is a GitHub Action for generating Swift DocC documentation with support for multiple schemes and versioning.

This action builds your DocC archive using Xcode and outputs a static documentation website that you can deploy anywhere (GitHub Pages, S3, etc).

## Features

- 🎯 **Multiple Schemes Support** - Build documentation for multiple frameworks/libraries in one go
- 📦 **Version Management** - Automatic versioning with option to preserve or replace old versions
- 🔄 **Flexible Xcode Versions** - Choose any Xcode version for building
- 🌐 **Static Hosting Ready** - Generates documentation optimized for GitHub Pages or any static hosting
- 🚀 **Easy Integration** - Simple YAML configuration with sensible defaults

## Table of Contents

- [Quick Start](#quick-start)
    - [Basic Usage](#basic-usage)
    - [Complete Example with Deployment](#complete-example-with-deployment)
- [Inputs](#inputs)
- [Outputs](#outputs)
- [Usage Examples](#usage-examples)
    - [Single Framework](#single-framework)
    - [Multiple Frameworks](#multiple-frameworks)
    - [Specific Xcode Version](#specific-xcode-version)
    - [Without Version Preservation](#without-version-preservation)
- [Version Management](#version-management)
    - [Keep Old Versions](#keep-old-versions-default)
    - [Replace Old Versions](#replace-old-versions)
- [Deployment to GitHub Pages](#deployment-to-github-pages)
    - [Complete Workflow](#complete-workflow)
    - [Enable GitHub Pages](#enable-github-pages)
- [Generated Structure](#generated-structure)
- [Advanced Usage](#advanced-usage)
    - [Using Outputs](#using-outputs)
    - [Matrix Build for Multiple Projects](#matrix-build-for-multiple-projects)
- [FAQ](#faq)
- [Communication](#communication)
- [License](#license)

## Quick Start

### Basic Usage

```
- name: Build Documentation
  uses: space-code/build-docc@v1
  with:
    schemes: '["MyFramework"]'
    version: 'v1.0.0'
```

### Complete Example with Deployment

```
name: Generate Documentation

on:
  push:
    tags:
      - 'v*'

jobs:
  build-and-deploy:
    runs-on: macos-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Build Documentation
        uses: space-code/build-docc@v1
        with:
          schemes: '["MyLib", "MyUI"]'
          version: ${{ github.ref_name }}
          keep-old-versions: 'true'

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./docs
          keep_files: true
```

## Inputs

## 📋 Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `schemes` | JSON array of schemes to build (e.g. `["MyLib", "MyUI"]`) | ✅ Yes | - |
| `version` | Documentation version (e.g. `v1.0.0`, `latest`) | ❌ No | `latest` |
| `xcode-version` | Xcode version to use for building | ❌ No | `latest-stable` |
| `keep-old-versions` | Keep old versions in docs/ directory | ❌ No | `true` |

## Outputs

| Output | Description |
|--------|-------------|
| `docs-path` | Path to the generated docs folder |
| `version` | Version used for build |

## Usage Examples

### Single Framework

```yaml
- uses: space-code/build-docc@v1
  with:
    schemes: '["MyFramework"]'
    version: 'v1.0.0'
```

### Multiple Frameworks

```yaml
- uses: space-code/build-docc@v1
  with:
    schemes: '["CoreLib", "UIComponents", "Networking"]'
    version: ${{ github.ref_name }}
```

### Specific Xcode Version

```yaml
- uses: space-code/build-docc@v1
  with:
    schemes: '["MyFramework"]'
    version: 'v2.0.0'
    xcode-version: '15.0'
```

### Without Version Preservation

```yaml
- uses: space-code/build-docc@v1
  with:
    schemes: '["MyFramework"]'
    version: 'latest'
    keep-old-versions: 'false'
```

## Version Management

The action supports two modes for handling documentation versions:

### Keep Old Versions (default)

When `keep-old-versions: 'true'` (default):
- Previous versions remain accessible
- New version is added alongside existing ones
- Perfect for maintaining documentation history

```
docs/
├── v1.0.0/
│   └── MyLib/
├── v1.1.0/
│   └── MyLib/
└── v2.0.0/
    └── MyLib/
```

### Replace Old Versions

When `keep-old-versions: 'false'`:
- Previous versions are removed
- Only the new version is kept
- Useful for "latest" documentation or saving storage

```
docs/
└── latest/
    └── MyLib/
```

## Deployment to GitHub Pages

### Complete Workflow

```yaml
name: Documentation

on:
  push:
    tags:
      - 'v*'
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build-docs:
    runs-on: macos-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Checkout gh-pages
        uses: actions/checkout@v4
        with:
          ref: gh-pages
          path: docs
        continue-on-error: true

      - name: Build Documentation
        uses: space-code/build-docc@v1
        with:
          schemes: '["MyFramework"]'
          version: ${{ github.ref_name }}
          keep-old-versions: 'true'

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./docs
          keep_files: true
```

### Enable GitHub Pages

1. Go to your repository **Settings** → **Pages**
2. Set **Source** to "Deploy from a branch"
3. Select **gh-pages** branch and **/ (root)** folder
4. Save

Your documentation will be available at:
```
https://your-username.github.io/your-repo/v1.0.0/MyFramework/
```

## Generated Structure

The action generates the following structure:

```
docs/
├── v1.0.0/
│   ├── MyLib/
│   │   ├── index.html
│   │   ├── documentation/
│   │   ├── data/
│   │   └── css/
│   └── MyUI/
│       ├── index.html
│       └── ...
└── v2.0.0/
    └── MyLib/
        └── ...
```

Each version contains fully static HTML documentation that can be hosted anywhere.

## Advanced Usage

### Using Outputs

```yaml
- name: Build Documentation
  id: docc
  uses: space-code/build-docc@v1
  with:
    schemes: '["MyFramework"]'
    version: 'v1.0.0'

- name: Archive Documentation
  run: |
    tar -czf docs.tar.gz ${{ steps.docc.outputs.docs-path }}

- name: Upload Artifact
  uses: actions/upload-artifact@v4
  with:
    name: documentation-${{ steps.docc.outputs.version }}
    path: docs.tar.gz
```

### Matrix Build for Multiple Projects

```yaml
jobs:
  docs:
    runs-on: macos-latest
    strategy:
      matrix:
        project:
          - name: 'Project A'
            schemes: '["LibA", "UtilsA"]'
          - name: 'Project B'
            schemes: '["LibB"]'
    steps:
      - uses: actions/checkout@v4
      
      - name: Build ${{ matrix.project.name }}
        uses: space-code/build-docc@v1
        with:
          schemes: ${{ matrix.project.schemes }}
          version: ${{ github.ref_name }}
```

## FAQ

### How do I find my scheme names?

```bash
# For Swift Package Manager
swift package describe | grep "Name:"

# For Xcode projects
xcodebuild -list
```

### Can I use this with CocoaPods or Carthage?

Yes! As long as your project has Xcode schemes, the action will work. Just ensure your schemes are shared.

## Communication

- 🐛 **Found a bug?** [Open an issue](https://github.com/space-code/build-docc/issues/new?template=bug_report.md)
- 💡 **Have a feature request?** [Open an issue](https://github.com/space-code/build-docc/issues/new?template=feature_request.md)
- ❓ **Questions?** [Start a discussion](https://github.com/space-code/build-docc/discussions)
- 🔒 **Security issue?** Email nv3212@gmail.com

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## Author

**Nikita Vasilev**
- Email: nv3212@gmail.com
- GitHub: [@ns-vasilev](https://github.com/ns-vasilev)

## License

build-docc is released under the MIT license. See [LICENSE](LICENSE) for details.

---

<div align="center">

**[⬆ back to top](#build-docc)**

Made with ❤️ by [space-code](https://github.com/space-code)

</div>