# ReleaseCraft CLI

**Professional Release Notes in Seconds** - Transform scattered pull requests into beautifully formatted release notes.

## 🚀 Quick Start

### Installation

```bash
# Install as a .NET global tool
dotnet tool install --global ReleaseCraft.CLI

# Or use specific version
dotnet tool install --global ReleaseCraft.CLI --version v0.5.3
```

### Usage

```bash
# Generate release notes
releasecraft --owner microsoft --repo vscode --from-tag 1.84.0 --to-tag 1.85.0

# With authentication
export GITHUB_TOKEN="your_github_token"
releasecraft --owner kubernetes --repo kubernetes --from-tag v1.28.0 --to-tag v1.29.0

# Save to file
releasecraft --owner facebook --repo react --from-tag v18.0.0 --to-tag v18.2.0 --output notes.md
```

## 📦 NuGet Packages

- **ReleaseCraft.Engine** - Core engine
- **ReleaseCraft.Connectors.GitHub** - GitHub integration
- **ReleaseCraft.Templates** - Output generators
- **ReleaseCraft.CLI** - Command-line tool

## 📖 Documentation

Full documentation available at [releasecraft.io](https://releasecraft.io) (coming soon)

## 🤝 Contributing

We welcome bug reports and feature requests! Please open an issue in this repository.

For source code contributions, development happens in our private repository. Contact us at nicolas@thal.dev for collaboration opportunities.

## 📜 License

MIT License - See LICENSE file for details

## 🔗 Links

- [NuGet Package](https://www.nuget.org/packages/ReleaseCraft.CLI)
- [GitHub Action](https://github.com/marketplace/actions/releasecraft)
- [Documentation](https://releasecraft.io)
- [Support](mailto:nicolas@thal.dev)

---

**Version:** v0.5.3  
**Built from:** [releasecraft-core](https://github.com/releasecraft/releasecraft-core) (private)
