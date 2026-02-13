# 🔌 Integration Guide

This guide shows you how to integrate ReleaseCraft into your .NET application for programmatic release notes generation.

## Table of Contents

- [When to Use Library Integration](#when-to-use-library-integration)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Real-World Scenarios](#real-world-scenarios)
- [Dependency Injection Setup](#dependency-injection-setup)
- [Custom Connectors](#custom-connectors)
- [Custom Output Formats](#custom-output-formats)
- [Error Handling](#error-handling)
- [Best Practices](#best-practices)

## When to Use Library Integration

Use ReleaseCraft as a library when you need:

- **UI Integration**: Generate release notes from a button click in your application
- **Automated Workflows**: Background services that monitor repos and auto-generate notes
- **Custom Pipelines**: Build release notes into your deployment or release process
- **Non-GitHub Sources**: Integrate with Azure DevOps, GitLab, or custom version control
- **Custom Formatting**: Generate outputs in proprietary formats specific to your needs
- **Full Control**: Programmatic access to every aspect of release notes generation

**Use the CLI instead** if you just need to generate notes from the command line or in CI/CD pipelines.

## Installation

### Option 1: Install Complete Package (Recommended)

```bash
dotnet add package ReleaseCraft
```

This includes:
- `ReleaseCraft.Engine` - Core orchestration
- `ReleaseCraft.Connectors.GitHub` - GitHub integration via Octokit
- `ReleaseCraft.Templates` - Markdown, JSON, HTML, CSV output formats
- `ReleaseCraft.AI` - AI-powered summarization (optional)

### Option 2: Install Modular Packages

```bash
# Core engine (required)
dotnet add package ReleaseCraft.Engine

# Add connectors as needed
dotnet add package ReleaseCraft.Connectors.GitHub

# Add output formats as needed
dotnet add package ReleaseCraft.Templates

# Add AI features if desired
dotnet add package ReleaseCraft.AI
```

## Quick Start

### Basic Example

```csharp
using ReleaseCraft.Connectors.GitHub;
using ReleaseCraft.Templates;

// Configure GitHub connection
var githubOptions = new GitHubOptions
{
    ProductName = "MyApp",
    Token = Environment.GetEnvironmentVariable("GITHUB_TOKEN") ?? string.Empty
};

// Create services
var changeSource = new GitHubChangeSource(githubOptions);
var generator = new MarkdownReleaseNotesGenerator(new MarkdownGeneratorOptions());

// Generate release notes
var changes = await changeSource.GetMergedPullRequestsBetweenTagsAsync(
    owner: "microsoft",
    repo: "vscode",
    fromTag: "1.84.0",
    toTag: "1.85.0",
    ct: CancellationToken.None
);

var releaseNotes = generator.Generate(
    changes,
    version: "1.85.0",
    releaseDate: DateTimeOffset.UtcNow
);

Console.WriteLine(releaseNotes);
```

## Real-World Scenarios

### Scenario 1: ASP.NET Core API Endpoint

Add a "Generate Release Notes" endpoint to your API:

```csharp
using Microsoft.AspNetCore.Mvc;
using ReleaseCraft.Connectors.GitHub;
using ReleaseCraft.Templates;

// Program.cs or Controller
app.MapPost("/api/releases/generate", async (
    [FromBody] GenerateReleaseNotesRequest request,
    [FromServices] GitHubChangeSource changeSource,
    [FromServices] MarkdownReleaseNotesGenerator generator,
    CancellationToken ct) =>
{
    try
    {
        var changes = await changeSource.GetMergedPullRequestsBetweenTagsAsync(
            owner: request.Owner,
            repo: request.Repository,
            fromTag: request.FromTag,
            toTag: request.ToTag,
            ct: ct
        );

        var notes = generator.Generate(
            changes,
            version: request.ToTag,
            releaseDate: DateTimeOffset.UtcNow
        );

        return Results.Ok(new { releaseNotes = notes });
    }
    catch (Exception ex)
    {
        return Results.Problem(
            detail: ex.Message,
            statusCode: 500,
            title: "Failed to generate release notes"
        );
    }
});

public record GenerateReleaseNotesRequest(
    string Owner,
    string Repository,
    string FromTag,
    string ToTag
);
```

### Scenario 2: Background Service for Automated Generation

Monitor repositories and automatically generate release notes:

```csharp
using Microsoft.Extensions.Hosting;
using ReleaseCraft.Connectors.GitHub;
using ReleaseCraft.Templates;

public class ReleaseNotesBackgroundService : BackgroundService
{
    private readonly GitHubChangeSource _changeSource;
    private readonly MarkdownReleaseNotesGenerator _generator;
    private readonly ILogger<ReleaseNotesBackgroundService> _logger;

    public ReleaseNotesBackgroundService(
        GitHubChangeSource changeSource,
        MarkdownReleaseNotesGenerator generator,
        ILogger<ReleaseNotesBackgroundService> logger)
    {
        _changeSource = changeSource;
        _generator = generator;
        _logger = logger;
    }

    protected override async Task ExecuteAsync(CancellationToken ct)
    {
        while (!ct.IsCancellationRequested)
        {
            try
            {
                await CheckForNewTagsAndGenerateNotes(ct);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error generating release notes");
            }

            // Check every hour
            await Task.Delay(TimeSpan.FromHours(1), ct);
        }
    }

    private async Task CheckForNewTagsAndGenerateNotes(CancellationToken ct)
    {
        // Your logic to detect new tags and generate notes
        _logger.LogInformation("Checking for new releases...");
        
        // Example: fetch latest tags, compare, and generate if new
        var changes = await _changeSource.GetMergedPullRequestsBetweenTagsAsync(
            owner: "your-org",
            repo: "your-repo",
            fromTag: "v1.0.0",
            toTag: "v1.1.0",
            ct: ct
        );

        var notes = _generator.Generate(changes, "v1.1.0", DateTimeOffset.UtcNow);
        
        // Store or send notes somewhere
        await StoreReleaseNotes(notes, ct);
    }

    private async Task StoreReleaseNotes(string notes, CancellationToken ct)
    {
        // Save to database, file system, or send to external service
        _logger.LogInformation("Release notes generated and stored");
    }
}
```

### Scenario 3: Blazor UI Integration

Add a UI component for generating release notes:

```csharp
@page "/releases/generate"
@inject GitHubChangeSource ChangeSource
@inject MarkdownReleaseNotesGenerator Generator

<h3>Generate Release Notes</h3>

<EditForm Model="@model" OnValidSubmit="@GenerateNotes">
    <InputText @bind-Value="model.Owner" placeholder="Owner" />
    <InputText @bind-Value="model.Repo" placeholder="Repository" />
    <InputText @bind-Value="model.FromTag" placeholder="From Tag" />
    <InputText @bind-Value="model.ToTag" placeholder="To Tag" />
    <button type="submit" disabled="@isGenerating">
        @(isGenerating ? "Generating..." : "Generate")
    </button>
</EditForm>

@if (!string.IsNullOrEmpty(releaseNotes))
{
    <div class="release-notes">
        <h4>Generated Release Notes</h4>
        <pre>@releaseNotes</pre>
    </div>
}

@code {
    private GenerateModel model = new();
    private string? releaseNotes;
    private bool isGenerating;

    private async Task GenerateNotes()
    {
        isGenerating = true;
        try
        {
            var changes = await ChangeSource.GetMergedPullRequestsBetweenTagsAsync(
                owner: model.Owner,
                repo: model.Repo,
                fromTag: model.FromTag,
                toTag: model.ToTag,
                ct: default
            );

            releaseNotes = Generator.Generate(
                changes,
                version: model.ToTag,
                releaseDate: DateTimeOffset.UtcNow
            );
        }
        finally
        {
            isGenerating = false;
        }
    }

    public class GenerateModel
    {
        public string Owner { get; set; } = string.Empty;
        public string Repo { get; set; } = string.Empty;
        public string FromTag { get; set; } = string.Empty;
        public string ToTag { get; set; } = string.Empty;
    }
}
```

## Dependency Injection Setup

### ASP.NET Core Registration

```csharp
using ReleaseCraft.Connectors.GitHub;
using ReleaseCraft.Templates;

var builder = WebApplication.CreateBuilder(args);

// Register ReleaseCraft services
builder.Services.AddSingleton(new GitHubOptions
{
    ProductName = "MyApp",
    Token = builder.Configuration["GitHub:Token"] ?? Environment.GetEnvironmentVariable("GITHUB_TOKEN") ?? string.Empty
});

builder.Services.AddSingleton<GitHubChangeSource>();
builder.Services.AddSingleton<MarkdownReleaseNotesGenerator>();

// If using background service
builder.Services.AddHostedService<ReleaseNotesBackgroundService>();

var app = builder.Build();
```

### Generic Host Registration

```csharp
var host = Host.CreateDefaultBuilder(args)
    .ConfigureServices((context, services) =>
    {
        services.AddSingleton(new GitHubOptions
        {
            ProductName = "MyApp",
            Token = context.Configuration["GitHub:Token"] ?? string.Empty
        });

        services.AddSingleton<GitHubChangeSource>();
        services.AddSingleton<MarkdownReleaseNotesGenerator>();
    })
    .Build();

await host.RunAsync();
```

## Custom Connectors

Implement `IChangeSource` to integrate with non-GitHub version control systems:

```csharp
using ReleaseCraft.Engine.Ports;
using ReleaseCraft.Engine.Model;

public class AzureDevOpsChangeSource : IChangeSource
{
    private readonly string _organization;
    private readonly string _personalAccessToken;

    public AzureDevOpsChangeSource(string organization, string personalAccessToken)
    {
        _organization = organization;
        _personalAccessToken = personalAccessToken;
    }

    public async Task<IEnumerable<Change>> GetMergedPullRequestsBetweenTagsAsync(
        string owner,
        string repo,
        string fromTag,
        string toTag,
        CancellationToken ct)
    {
        // Implement Azure DevOps API calls
        // Use Azure DevOps REST API or SDK to fetch PRs
        
        var changes = new List<Change>();
        
        // Example: Fetch work items/PRs from Azure DevOps
        // var workItems = await GetWorkItemsFromAzureDevOps(repo, fromTag, toTag, ct);
        
        // foreach (var item in workItems)
        // {
        //     changes.Add(new Change
        //     {
        //         Title = item.Title,
        //         Number = item.Id,
        //         Author = item.CreatedBy.DisplayName,
        //         Labels = item.Tags?.Split(';') ?? Array.Empty<string>(),
        //         Url = item.Url,
        //         MergedAt = item.CompletedDate
        //     });
        // }
        
        return changes;
    }
}
```

## Custom Output Formats

Implement `IReleaseNotesGenerator` for custom output formats:

```csharp
using ReleaseCraft.Engine.Ports;
using ReleaseCraft.Engine.Model;

public class SlackReleaseNotesGenerator : IReleaseNotesGenerator
{
    public string Generate(
        IEnumerable<Change> changes,
        string version,
        DateTimeOffset releaseDate)
    {
        var grouped = changes.GroupBy(c => c.Category);
        
        var blocks = new List<object>
        {
            new
            {
                type = "header",
                text = new { type = "plain_text", text = $"Release {version}" }
            },
            new
            {
                type = "context",
                elements = new[]
                {
                    new { type = "mrkdwn", text = $"*Released:* {releaseDate:yyyy-MM-dd}" }
                }
            }
        };

        foreach (var group in grouped.OrderBy(g => g.Key))
        {
            blocks.Add(new
            {
                type = "section",
                text = new
                {
                    type = "mrkdwn",
                    text = $"*{GetEmoji(group.Key)} {group.Key}*\n" +
                           string.Join("\n", group.Select(c => $"• <{c.Url}|#{c.Number}> {c.Title}"))
                }
            });
        }

        return System.Text.Json.JsonSerializer.Serialize(new { blocks });
    }

    private string GetEmoji(string category) => category switch
    {
        "Security" => "🔒",
        "Features" => "🚀",
        "Bug Fixes" => "🐛",
        _ => "📝"
    };
}
```

## Error Handling

### Handle API Rate Limits

```csharp
using Octokit;

try
{
    var changes = await changeSource.GetMergedPullRequestsBetweenTagsAsync(
        owner, repo, fromTag, toTag, ct);
}
catch (RateLimitExceededException ex)
{
    // GitHub API rate limit exceeded
    _logger.LogWarning(
        "GitHub API rate limit exceeded. Resets at: {ResetTime}",
        ex.Reset);
    
    // Wait until reset or use authenticated requests for higher limits
    await Task.Delay(ex.Reset - DateTimeOffset.UtcNow, ct);
}
catch (ApiException ex)
{
    // Other GitHub API errors
    _logger.LogError(ex, "GitHub API error: {Message}", ex.Message);
}
```

### Handle Missing Tags

```csharp
try
{
    var changes = await changeSource.GetMergedPullRequestsBetweenTagsAsync(
        owner, repo, fromTag, toTag, ct);
    
    if (!changes.Any())
    {
        _logger.LogWarning("No changes found between {FromTag} and {ToTag}", fromTag, toTag);
        return "No changes in this release.";
    }
}
catch (NotFoundException ex)
{
    _logger.LogError("Tag not found: {Message}", ex.Message);
    return $"Error: Tag '{fromTag}' or '{toTag}' not found in repository.";
}
```

## Best Practices

### 1. Use Authentication

Always provide a GitHub token for higher rate limits and access to private repositories:

```csharp
var githubOptions = new GitHubOptions
{
    ProductName = "MyApp",
    Token = Environment.GetEnvironmentVariable("GITHUB_TOKEN") ?? string.Empty
};
```

### 2. Cache Generated Notes

Avoid regenerating the same release notes repeatedly:

```csharp
public class CachedReleaseNotesService
{
    private readonly IMemoryCache _cache;
    private readonly GitHubChangeSource _changeSource;
    private readonly MarkdownReleaseNotesGenerator _generator;

    public async Task<string> GetOrGenerateAsync(
        string owner, string repo, string fromTag, string toTag, CancellationToken ct)
    {
        var cacheKey = $"{owner}/{repo}/{fromTag}..{toTag}";
        
        if (_cache.TryGetValue<string>(cacheKey, out var cached))
        {
            return cached!;
        }

        var changes = await _changeSource.GetMergedPullRequestsBetweenTagsAsync(
            owner, repo, fromTag, toTag, ct);
        
        var notes = _generator.Generate(changes, toTag, DateTimeOffset.UtcNow);
        
        _cache.Set(cacheKey, notes, TimeSpan.FromHours(24));
        
        return notes;
    }
}
```

### 3. Handle Cancellation Properly

Always respect cancellation tokens:

```csharp
public async Task<string> GenerateWithTimeoutAsync(
    string owner, string repo, string fromTag, string toTag)
{
    using var cts = new CancellationTokenSource(TimeSpan.FromSeconds(30));
    
    try
    {
        var changes = await _changeSource.GetMergedPullRequestsBetweenTagsAsync(
            owner, repo, fromTag, toTag, cts.Token);
        
        return _generator.Generate(changes, toTag, DateTimeOffset.UtcNow);
    }
    catch (OperationCanceledException)
    {
        _logger.LogWarning("Release notes generation timed out after 30 seconds");
        throw;
    }
}
```

### 4. Log Operations

Add comprehensive logging for debugging:

```csharp
_logger.LogInformation(
    "Generating release notes for {Owner}/{Repo} from {FromTag} to {ToTag}",
    owner, repo, fromTag, toTag);

var sw = Stopwatch.StartNew();
var changes = await _changeSource.GetMergedPullRequestsBetweenTagsAsync(...);
sw.Stop();

_logger.LogInformation(
    "Fetched {Count} changes in {ElapsedMs}ms",
    changes.Count(), sw.ElapsedMilliseconds);
```

### 5. Validate Input

Always validate user input before making API calls:

```csharp
public async Task<string> GenerateReleaseNotesAsync(
    string owner, string repo, string fromTag, string toTag, CancellationToken ct)
{
    if (string.IsNullOrWhiteSpace(owner))
        throw new ArgumentException("Owner cannot be empty", nameof(owner));
    
    if (string.IsNullOrWhiteSpace(repo))
        throw new ArgumentException("Repository cannot be empty", nameof(repo));
    
    if (string.IsNullOrWhiteSpace(fromTag))
        throw new ArgumentException("From tag cannot be empty", nameof(fromTag));
    
    if (string.IsNullOrWhiteSpace(toTag))
        throw new ArgumentException("To tag cannot be empty", nameof(toTag));

    // Proceed with generation
    var changes = await _changeSource.GetMergedPullRequestsBetweenTagsAsync(
        owner, repo, fromTag, toTag, ct);
    
    return _generator.Generate(changes, toTag, DateTimeOffset.UtcNow);
}
```

## Next Steps

- **[API Reference](API_REFERENCE.md)** - Detailed API documentation
- **[Configuration](CONFIGURATION.md)** - Customize category mappings and behavior
- **[Examples](EXAMPLES.md)** - More code samples and use cases
- **[CLI Usage](CLI_USAGE.md)** - For simple command-line generation

## Need Help?

- 💬 [GitHub Discussions](https://github.com/releasecraft/releasecraft-cli/discussions)
- 🐛 [Report Issues](https://github.com/releasecraft/releasecraft-cli/issues)
- 📧 [Email Support](mailto:support@releasecraft.io)
