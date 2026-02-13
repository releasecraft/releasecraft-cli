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

## 🔌 Integration into Your Application

**Embed ReleaseCraft directly into your .NET application** for programmatic release notes generation. Perfect for building custom tools, internal portals, admin dashboards, or SaaS features.

### Installation for Integration

```bash
# Install the complete package (recommended)
dotnet add package ReleaseCraft

# Or install modular packages
dotnet add package ReleaseCraft.Engine           # Core
dotnet add package ReleaseCraft.Connectors.GitHub  # GitHub integration
dotnet add package ReleaseCraft.Templates         # Output formats
```

### Quick Integration Example

```csharp
using ReleaseCraft.Connectors.GitHub;
using ReleaseCraft.Templates;

var githubOptions = new GitHubOptions
{
    ProductName = "MyApp",
    Token = Environment.GetEnvironmentVariable("GITHUB_TOKEN") ?? string.Empty
};

var changeSource = new GitHubChangeSource(githubOptions);
var generator = new MarkdownReleaseNotesGenerator(new MarkdownGeneratorOptions());

var changes = await changeSource.GetMergedPullRequestsBetweenTagsAsync(
    owner: "dotnet",
    repo: "aspnetcore",
    fromTag: "v8.0.0",
    toTag: "v8.1.0",
    ct: CancellationToken.None
);

var releaseNotes = generator.Generate(
    changes,
    version: "v8.1.0",
    releaseDate: DateTimeOffset.UtcNow
);

Console.WriteLine(releaseNotes);
```

### Real-World Scenarios

**1. ASP.NET Core API Endpoint**
```csharp
app.MapPost("/api/releases/generate", async (
    [FromBody] GenerateRequest request,
    [FromServices] GitHubChangeSource changeSource,
    [FromServices] MarkdownReleaseNotesGenerator generator) =>
{
    var changes = await changeSource.GetMergedPullRequestsBetweenTagsAsync(
        owner: request.Owner, repo: request.Repo,
        fromTag: request.FromTag, toTag: request.ToTag, ct: default
    );
    return generator.Generate(changes, request.ToTag, DateTimeOffset.UtcNow);
});
```

**2. Background Service for Automated Generation**
```csharp
public class ReleaseNotesService : BackgroundService
{
    protected override async Task ExecuteAsync(CancellationToken ct)
    {
        while (!ct.IsCancellationRequested)
        {
            await GenerateReleaseNotesForNewTags();
            await Task.Delay(TimeSpan.FromHours(1), ct);
        }
    }
}
```

**3. Blazor UI Component**
```csharp
@page "/releases/generate"
@inject GitHubChangeSource ChangeSource
@inject MarkdownReleaseNotesGenerator Generator

<button @onclick="GenerateNotes">Generate Release Notes</button>

@code {
    private async Task GenerateNotes()
    {
        var changes = await ChangeSource.GetMergedPullRequestsBetweenTagsAsync(...);
        var notes = Generator.Generate(changes, version, DateTimeOffset.UtcNow);
    }
}
```

📖 **[Full Integration Guide](docs/INTEGRATION.md)** - Detailed examples, DI setup, custom connectors, error handling, and best practices.

## 📦 NuGet Packages

### Core Packages

- **[ReleaseCraft](https://www.nuget.org/packages/ReleaseCraft)** - Complete package (recommended for integration)
- **[ReleaseCraft.Engine](https://www.nuget.org/packages/ReleaseCraft.Engine)** - Core engine
- **[ReleaseCraft.Connectors.GitHub](https://www.nuget.org/packages/ReleaseCraft.Connectors.GitHub)** - GitHub integration
- **[ReleaseCraft.Templates](https://www.nuget.org/packages/ReleaseCraft.Templates)** - Output generators

### Tools

- **[ReleaseCraft.CLI](https://www.nuget.org/packages/ReleaseCraft.CLI)** - Command-line tool

**Installation:**
```bash
# For CLI usage
dotnet tool install --global ReleaseCraft.CLI

# For app integration
dotnet add package ReleaseCraft
```

## 📖 Documentation

- **[Integration Guide](docs/INTEGRATION.md)** - Embed ReleaseCraft in your .NET applications
- **[CLI Usage](https://github.com/releasecraft/releasecraft-cli#-quick-start)** - Command-line examples
- **[NuGet Packages](https://www.nuget.org/packages?q=ReleaseCraft)** - Available packages

Full documentation available at [releasecraft.io](https://releasecraft.io)

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
