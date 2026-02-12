# Security Policy

## Supported Versions

We actively maintain and provide security updates for the following versions:

| Version | Supported          | Status |
| ------- | ------------------ | ------ |
| 1.1.x   | :white_check_mark: | Current stable release |
| 1.0.x   | :white_check_mark: | Previous stable release |
| 0.5.x   | :warning:          | Legacy - critical fixes only |
| < 0.5   | :x:                | No longer supported |

## Reporting a Vulnerability

We take security vulnerabilities seriously. If you discover a security issue in ReleaseCraft, please report it responsibly.

### How to Report

**DO NOT** open a public GitHub issue for security vulnerabilities.

Instead, please report security issues via one of these methods:

1. **GitHub Security Advisories (Preferred)**
  - Go to [Security Advisories](https://github.com/releasecraft/releasecraft-cli/security/advisories)
   - Click "Report a vulnerability"
   - Provide detailed information about the vulnerability

2. **Email**
   - Send an email to: security@releasecraft.io
   - Include "SECURITY" in the subject line
   - Provide detailed information about the vulnerability

### What to Include

When reporting a vulnerability, please include:

- **Description**: Clear description of the vulnerability
- **Impact**: What can an attacker do with this vulnerability?
- **Reproduction Steps**: Detailed steps to reproduce the issue
- **Version**: Which version(s) are affected?
- **Environment**: OS, .NET version, any relevant configuration
- **Proof of Concept**: Code or examples demonstrating the issue (if applicable)
- **Suggested Fix**: If you have ideas on how to fix it (optional)

### Response Timeline

We are committed to addressing security vulnerabilities promptly:

- **Initial Response**: Within 48 hours of report
- **Confirmation**: Within 5 business days - we'll confirm if it's a valid vulnerability
- **Fix Development**: Depends on severity
  - Critical: 1-7 days
  - High: 7-14 days
  - Medium: 14-30 days
  - Low: 30-60 days
- **Public Disclosure**: After fix is released and users have had time to update (typically 7-14 days)

### Disclosure Policy

- We follow **coordinated disclosure**
- We will notify you when we plan to publicly disclose the vulnerability
- We will credit you in the security advisory (unless you prefer to remain anonymous)
- We ask that you do not publicly disclose the vulnerability until we have released a fix

## Security Best Practices

When using ReleaseCraft, follow these security best practices:

### 1. GitHub Tokens

- **Never commit tokens** to source control
- Use environment variables or secure secret management
- Use tokens with **minimum required permissions**:
  - `repo:status` - Read repository status
  - `public_repo` - Read public repositories (if only accessing public repos)
- Rotate tokens regularly (every 90 days recommended)
- Revoke tokens immediately if compromised

### 2. API Keys (for AI features)

- Store OpenAI API keys securely (environment variables, Azure Key Vault, etc.)
- Never hardcode API keys in configuration files
- Use separate keys for development and production
- Monitor API key usage for unusual activity
- Implement rate limiting to prevent abuse

### 3. Configuration Files

- Review `.releasecraftrc.json` files before committing
- Use `.gitignore` to exclude sensitive configuration
- Validate configuration inputs to prevent injection attacks
- Use file permissions to restrict access to config files (chmod 600)

### 4. CI/CD Pipelines

- Use GitHub Actions secrets for sensitive values
- Limit secret access to specific workflows/branches
- Enable branch protection rules on main/production branches
- Review workflow permissions regularly
- Audit workflow runs for suspicious activity

### 5. Dependencies

- Keep ReleaseCraft and its dependencies up to date
- Monitor security advisories for dependencies
- Use `dotnet list package --vulnerable` to check for known vulnerabilities
- Enable Dependabot security updates on your repository

### 6. Network Security

- Use HTTPS for all GitHub API calls (default in ReleaseCraft)
- Verify SSL/TLS certificates (default behavior)
- Consider using a proxy for egress filtering in enterprise environments
- Monitor outbound network traffic for anomalies

### 7. Output Security

- Review generated release notes before publishing
- Sanitize user-generated content (PR titles, descriptions)
- Be cautious with AI-generated content - review before publishing
- Consider using content security policies for HTML output

## Known Security Considerations

### GitHub Token Permissions

ReleaseCraft requires read access to your GitHub repositories:
- **Public repos**: `public_repo` scope
- **Private repos**: `repo` scope

We recommend using fine-grained personal access tokens (PATs) with repository-specific access when available.

### AI Features

When using AI summarization:
- PR content is sent to OpenAI API for processing
- No data is stored by ReleaseCraft or OpenAI beyond API processing
- Review OpenAI's [data usage policies](https://openai.com/policies/usage-policies)
- Consider disabling AI features for sensitive/proprietary repositories

### Rate Limiting

ReleaseCraft respects GitHub API rate limits:
- Authenticated requests: 5,000 per hour
- Unauthenticated requests: 60 per hour

Excessive requests may trigger rate limiting. Always use authentication.

## Security Updates

Security updates are released as:
- **Patch releases** (e.g., 1.0.1 → 1.0.2) for minor vulnerabilities
- **Minor releases** (e.g., 1.0.0 → 1.1.0) for moderate vulnerabilities
- **Immediate patches** for critical vulnerabilities

Subscribe to:
- [GitHub Security Advisories](https://github.com/releasecraft/releasecraft-cli/security/advisories)
- [Release notifications](https://github.com/releasecraft/releasecraft-cli/releases)
- [NuGet package updates](https://www.nuget.org/packages/ReleaseCraft/)

## Security Scanning

ReleaseCraft uses:
- **CodeQL** - Static analysis for code vulnerabilities
- **Dependabot** - Automated dependency security updates
- **GitHub Advanced Security** - Vulnerability scanning

All pull requests are automatically scanned for security issues.

## Acknowledgments

We appreciate security researchers who responsibly disclose vulnerabilities. Contributors who report valid security issues will be acknowledged in:
- Security advisories
- Release notes
- CONTRIBUTORS.md (if desired)

## Questions?

For security-related questions that are not vulnerabilities:
- Open a [GitHub Discussion](https://github.com/releasecraft/releasecraft-cli/discussions)
- Email: security@releasecraft.io

---

**Last Updated:** February 2026  
**Version:** 1.0
