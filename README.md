# ReleaseCraft CLI

**Professional Release Notes in Seconds** - Transform scattered pull requests into beautifully formatted release notes.

---

## Quick Start

### Installation

```bash
# Install as a .NET global tool
dotnet tool install --global ReleaseCraft.CLI

# Verify installation
releasecraft --version
```

### Usage

```bash
# Generate release notes
releasecraft --owner microsoft --repo vscode --from-tag 1.84.0 --to-tag 1.85.0

# With authentication (for private repos or higher rate limits)
export GITHUB_TOKEN="your_github_token"
releasecraft --owner kubernetes --repo kubernetes --from-tag v1.28.0 --to-tag v1.29.0

# Save to file
releasecraft --owner facebook --repo react --from-tag v18.0.0 --to-tag v18.2.0 --output RELEASE_NOTES.md
```

---

## Features

- **Automated PR Extraction** - Fetches merged PRs between two git tags
- **Smart Categorization** - Groups PRs by labels (features, bugs, breaking changes, etc.)
- **Multiple Output Formats** - Markdown, JSON, HTML, CSV
- **AI Summarization** - Optional GPT-4 powered summaries (coming soon)
- **GitHub Action** - Automated release notes on every release
- **Highly Configurable** - Custom categories, templates, and more

---

## NuGet Packages

- **ReleaseCraft.CLI** - Command-line tool
- **ReleaseCraft.Engine** - Core library
- **ReleaseCraft.Connectors.GitHub** - GitHub integration
- **ReleaseCraft.Templates** - Output generators

---

## Authentication

ReleaseCraft supports GitHub authentication via:

1. **Environment Variable** (recommended):
   ```bash
   export GITHUB_TOKEN="ghp_your_token_here"
   releasecraft --owner owner --repo repo --from-tag v1.0 --to-tag v2.0
   ```

2. **Command-line Flag**:
   ```bash
   releasecraft --owner owner --repo repo --from-tag v1.0 --to-tag v2.0 --token ghp_your_token
   ```

**Creating a GitHub Token:**
1. Go to GitHub Settings → Developer Settings → Personal Access Tokens
2. Click "Generate new token (classic)"
3. Select scopes: `public_repo` (for public repos) or `repo` (for private repos)
4. Copy and save your token securely

---

## Documentation

- Installation Guide
- Usage Examples
- Configuration
- GitHub Action Setup
- API Reference

---

## Contributing

We welcome bug reports, feature requests, and documentation improvements!

See CONTRIBUTING.md for guidelines.

**Note:** Source code development happens in our private repository. For code contributions, please contact nicolas@thal.dev

---

## License

MIT License - See LICENSE for details

---

## Links

- **NuGet:** ReleaseCraft.CLI on NuGet.org
- **GitHub Action:** marketplace (coming soon)
- **Documentation:** releasecraft.io (coming soon)
- **Support:** nicolas@thal.dev

---

## Why ReleaseCraft?

| Without ReleaseCraft | With ReleaseCraft |
|---------------------|-------------------|
| 1 hour manual work | 30 seconds automated |
| Copy-paste from PRs | Automated extraction |
| Inconsistent formatting | Beautiful, consistent output |
| Missing PRs/errors | Complete and accurate |
| Boring, repetitive | Fast and reliable |

---

**Built with love by the ReleaseCraft team**  
**Version:** Latest on NuGet
