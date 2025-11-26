<h1 align="center" style="margin-top: 0px;">build-docc</h1>

<p align="center">
<a href="https://github.com/space-code/build-docc/blob/main/LICENSE"><img alt="Licence" src="https://img.shields.io/badge/License-MIT-yellow.svg"></a> 
<a href="https://github.com/space-code/build-docc/actions/workflows/ci.yml"><img alt="CI" src="https://github.com/space-code/build-docc/actions/workflows/ci.yml/badge.svg?branch=main"></a>
</p>

## Description

build-docc is a GitHub Action for generating Swift DocC documentation with support for multiple schemes and versioning.

This action builds your DocC archive using Xcode and outputs a static documentation website that you can deploy anywhere (GitHub Pages, S3, etc).

## Features

🛠 Generates DocC documentation for one or multiple schemes
🏷 Supports documentation versioning (e.g., 1.0.0, latest, beta) 
📦 Easy to integrate in CI/CD workflows 

## Table of Contents

- [Usage](#usage)
- [License](#license)

## Usage

```
name: Build Documentation

on:
  push:
    branches: [ "main" ]

jobs:
  build-docs:
    runs-on: macos-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Build DocC archive
        uses: space-code/build-docc@v1
        with:
          schemes: '["MyLibrary"]'
          version: '1.2.0'

      - name: Deploy to GitHub Pages
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./docs
```

## Communication

- 🐛 **Found a bug?** [Open an issue](https://github.com/space-code/build-docc/issues/new?template=bug_report.md)
- 💡 **Have a feature request?** [Open an issue](https://github.com/space-code/build-docc/issues/new?template=feature_request.md)
- ❓ **Questions?** [Start a discussion](https://github.com/space-code/build-docc/discussions)
- 🔒 **Security issue?** Email nv3212@gmail.com

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