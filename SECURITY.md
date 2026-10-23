# Security Policy

## Reporting a Vulnerability

We take the security of Retrieve-IQ seriously and encourage you to report any vulnerabilities you discover.

### Responsible Disclosure

If you believe you've found a security vulnerability, please report it to us following our **Responsible Disclosure** process:

1. **DO NOT** disclose the vulnerability publicly (social media, public forums, HackerOne without approval)
2. Send an email to: **security@retrieve-iq.com**
3. Include in your report:
   - Vulnerability description
   - Steps to reproduce
   - Potential impact/attack vector
   - Any proof-of-concept code

### What We Commit To

- **Acknowledgment**: We will acknowledge receipt of your report within 48 hours
- **Investigation**: We will investigate and validate the issue within 1 week
- **Timeline**: We'll provide a timeline for fixes based on severity (Critical: 7 days, High: 14 days, Medium: 30 days)
- **Credit**: We'll publicly credit responsible reporters (with their explicit permission)

### Out of Scope

The following are **out of scope** for our vulnerability disclosure program:

- Cross-site scripting (XSS) in search queries without data exfiltration capabilities
- SQL injection attempts in public demo environments
- Access to accounts that have been publicly exposed or breached by a third party
- Attacks on services we do not own, operate, or control
- Penetration testing without express written permission from retrieve-iq.com

### Bug Bounty Program

We run a **conditional** bug bounty program. See our [Bug Bounty Policy](BUG_BOUNTY.md) for details:

- **Critical vulnerabilities**: Up to $5,000 USD (remote code execution, authentication bypass affecting production)
- **High severity**: Up to $2,000 USD (SQL injection, XSS enabling account takeover)
- **Medium severity**: Up to $500 USD (information disclosure, DoS)

Bounty eligibility requires:
- Responsible disclosure via the above email
- No public disclosure prior to our announcement
- Vulnerability affects production systems

## Security Best Practices

### For Users

When using Retrieve-IQ services:

1. **Keep API Keys Secure**
   - Never commit keys to version control
   - Use environment variables or secret management tools
   - Rotate keys regularly (every 90 days recommended)

2. **Rate Limiting**
   - Implement client-side rate limiting
   - Respect `X-RateLimit-*` response headers
   - Exponential backoff on 429 responses

3. **Input Validation**
   - Always validate API responses
   - Sanitize user input before use
   - Use parameterized queries for database operations

4. **Dependency Management**
   - Keep SDKs and dependencies updated
   - Monitor [npm advisories](https://www.npmjs.com/advisories)
   - Use tools like Snyk or Dependabot

### For Contributors

When contributing code:

1. **Code Review**: All PRs are reviewed for security implications
2. **Secret Scanning**: We scan commits for exposed credentials
3. **Dependency Updates**: Keep dependencies current and secure
4. **Secure Defaults**: Implement principle of least privilege

## Security Headers

Our platform sends the following security headers:

| Header | Value | Purpose |
|--------|-------|---------|
| Content-Security-Policy | Strict CSP policy | Prevent XSS |
| X-Content-Type-Options | nosniff | Prevent MIME sniffing |
| X-Frame-Options | DENY | Prevent clickjacking |
| Referrer-Policy | strict-origin-when-cross-origin | Control referrer information |
| Permissions-Policy | Geolocation, microphone=() | Restrict browser features |

## Incident Response

In the event of a security incident:

1. **Assessment**: Our security team assesses the severity
2. **Containment**: We contain the issue to prevent further damage
3. **Communication**: We notify affected parties (customers, partners)
4. **Remediation**: We apply fixes and verify resolution
5. **Post-Mortem**: We document lessons learned

### Notification Timeline

| Severity | Customer Notification | Public Disclosure |
|----------|----------------------|-------------------|
| Critical (CC1-CC2) | Within 2 hours | As required by law |
| High (CC3) | Within 24 hours | After remediation |
| Medium (CC4) | Within 72 hours | At discretion of security team |

Severity categories:
- **CC1**: Data breach affecting authentication credentials
- **CC2**: Active exploit in production requiring immediate patch
- **CC3**: Vulnerability with known public exploits
- **CC4**: Non-exploitable issues or misconfigurations

## Security Audits

We perform regular security audits:

- **Quarterly penetration testing** by external firms
- **Annual code review** focusing on security architecture
- **Continuous monitoring** via our SIEM infrastructure
- **Dependency scanning** for known vulnerabilities

## Supply Chain Security

### Software Bill of Materials (SBOM)

We maintain an SBOM at `/sbom.json` containing:
- All direct and transitive dependencies
- Package versions and licenses
- Known vulnerability status

### Dependency Updates

Our automated processes handle dependency updates:
- Dependabot for GitHub-hosted repos
- Renovate for version resolution
- Automatic PR creation for security patches

## Third-Party Integrations

When using third-party services with Retrieve-IQ:

1. Use official SDKs when available
2. Verify the vendor's security practices
3. Implement network segmentation
4. Monitor integration health metrics
5. Have an exit strategy (e.g., API rate limits, alternative sources)

## Security Checklist for Deployments

Before deploying to production:

- [ ] Rotate all API keys and secrets
- [ ] Review firewall and network rules
- [ ] Enable TLS 1.3 with strong cipher suites
- [ ] Verify database backups are encrypted at rest
- [ ] Set up intrusion detection/logging
- [ ] Configure security headers
- [ ] Review third-party SDK versions
- [ ] Test failover procedures

## Security Resources

- [OWASP Cheat Sheets](https://cheatsheetseries.owasp.org/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- Our internal security docs at `internal-docs/security/`

## Security Team Contact

For security questions or concerns:
- **Email**: security@retrieve-iq.com
- **Slack**: #security channel (enterprise customers only)

---

_Last updated: 2024-10-20_

*This document is subject to review and updates. Changes will be announced via our security advisory feed.*
