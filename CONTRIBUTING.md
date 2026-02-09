# Contributing to ReleaseCraft

Thank you for your interest in contributing to ReleaseCraft! 🎉

## 📋 Types of Contributions

We welcome the following contributions to this **public distribution repository**:

✅ **Bug Reports** - Issues you've encountered using the CLI or NuGet packages  
✅ **Feature Requests** - Ideas for improving the tool  
✅ **Documentation** - Improvements to README, guides, or examples  
✅ **Questions** - Usage questions or clarifications

❌ **Source Code PRs** - Source code development happens in our private repository

---

## 🐛 Reporting Bugs

When reporting bugs, please include:

1. **Version:** Which version of ReleaseCraft are you using?
   ```bash
   releasecraft --version
   ```

2. **Environment:**
   - Operating System (Windows, macOS, Linux)
   - .NET version (`dotnet --version`)
   - Shell/Terminal

3. **Command:** The exact command you ran
   ```bash
   releasecraft --owner ... --repo ... --from-tag ... --to-tag ...
   ```

4. **Expected behavior:** What you expected to happen

5. **Actual behavior:** What actually happened (include error messages)

6. **Additional context:** Any other relevant information

---

## 💡 Feature Requests

We love hearing your ideas! When submitting a feature request:

1. **Describe the problem** you're trying to solve
2. **Describe the solution** you'd like to see
3. **Describe alternatives** you've considered
4. **Additional context** (screenshots, examples, etc.)

---

## 📖 Documentation Improvements

Documentation PRs are welcome! You can improve:

- README clarity and examples
- Installation instructions
- Usage guides
- Troubleshooting sections
- FAQ

**Process:**
1. Fork this repository
2. Create a branch (`git checkout -b docs/improve-readme`)
3. Make your changes
4. Submit a Pull Request

---

## 🔒 Source Code Contributions

Source code development happens in our **private** `releasecraft-core` repository to protect intellectual property and proprietary algorithms.

**If you're interested in contributing code:**
- Email us at **nicolas@thal.dev**
- We evaluate collaboration opportunities case-by-case
- Potential contributors may be granted access to the private repository

---

## 📦 What's in This Repository

This `releasecraft-cli` repository contains:
- ✅ Compiled NuGet packages (.nupkg files)
- ✅ Public documentation
- ✅ Issue tracking
- ✅ Release distributions

This repository does **NOT** contain:
- ❌ Source code (it's in the private `releasecraft-core` repo)
- ❌ Build scripts
- ❌ Internal development documentation

---

## 🔄 Release Process

Releases are automated:
1. Core team creates a release tag in the private repository
2. CI/CD builds, tests, and publishes to NuGet.org
3. Compiled packages are synced to this public repository
4. GitHub Release is created here with binaries

---

## 🤝 Community Guidelines

- Be respectful and constructive
- Follow the [Code of Conduct](CODE_OF_CONDUCT.md)
- Search existing issues before creating new ones
- Help others when you can
- Stay on topic

---

## 📞 Contact

- **Email:** nicolas@thal.dev
- **GitHub Issues:** [Create an issue](https://github.com/releasecraft/releasecraft-cli/issues/new)
- **NuGet:** [ReleaseCraft.CLI](https://www.nuget.org/packages/ReleaseCraft.CLI)

---

## 📜 License

ReleaseCraft is released under the MIT License. See [LICENSE](LICENSE) for details.

---

**Thank you for contributing to ReleaseCraft!** 🚀
