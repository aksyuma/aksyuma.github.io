# Security Policy

## Supported Versions

| Version | Supported |
|---------|-----------|
| `main` branch (latest deployment) | ✅ Yes |
| Older deployments | ❌ No |

Only the current `main` branch and its latest deployment to GitHub Pages are actively maintained.

## Reporting a Vulnerability

If you discover a security vulnerability in this project, please report it responsibly:

1. **Do not open a public issue.** Security issues must be reported privately.
2. **Email:** Open a [private security advisory](https://github.com/aksyuma/aksyuma.github.io/security/advisories/new) on this repository.
3. **Include:**
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)

## Response Timeline

- **Acknowledgement:** within 72 hours
- **Assessment:** within 7 days
- **Fix (if confirmed):** best effort, typically within 14 days for critical issues

## Scope

This policy covers:
- The source code in this repository
- The deployed static site at `https://aksyuma.github.io`
- GitHub Actions workflows and their configuration
- Dependencies declared in `package.json`

This policy does **not** cover:
- GitHub's infrastructure itself
- Third-party services linked from the site
- Content accuracy (not a security issue)

## Security Measures

- GitHub Actions are pinned to commit SHAs
- Workflow permissions follow least-privilege
- Dependencies are locked via `yarn.lock`
- The site is static (no server-side application runtime)
- No user data is collected or stored
