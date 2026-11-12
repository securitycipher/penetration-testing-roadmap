# Dependency Confusion

Dependency confusion (also called a substitution attack) abuses how package managers resolve names. If a company uses an **internal** package name that is not also claimed on the **public** registry, an attacker can publish a malicious package with that same name - often with a higher version number - on the public registry. The build tool, seeing a "newer" public version, downloads and runs the attacker's code. Researcher Alex Birsan famously used this to breach dozens of major companies.

## Why it works

- Internal packages (e.g. `acme-internal-utils`) are referenced in `package.json` / `requirements.txt` but only live in a private feed.
- Many package managers check public and private sources and pick the **highest version**, not the trusted source.
- If the public registry has no such name, an attacker registers it with version `99.0.0`.
- The build resolves to the public malicious package and executes install scripts with build-level access.

## Affected ecosystems

npm, PyPI (pip), RubyGems, and others with similar resolution. The install-time script hooks (`preinstall`/`postinstall` in npm, `setup.py` in pip) are the usual execution trigger.

## How to find it

1. Collect internal package names from leaked `package.json`, `requirements.txt`, lockfiles, error messages, or client-side bundles.
2. Check whether each name is **unclaimed** on the public registry.
3. In scope only, publish a benign package with a high version that calls back (DNS/HTTP) on install to prove execution - never ship real malware.

```bash
# Example check (npm): does the name exist publicly?
npm view acme-internal-utils      # 404 => potentially claimable
```

## Mitigation

- **Claim your internal names** on public registries (even empty placeholder packages).
- Use **scoped packages** (npm `@org/pkg`) tied to your private registry.
- Configure the package manager to pull internal names **only** from the private feed (scope mapping, `.npmrc` registry scoping, pip index priorities).
- Pin versions and hashes in lockfiles; verify integrity.
- Disable or sandbox install scripts in CI.

## Related

- [CI/CD Security](CI-CD%20Security.md), [GitHub Actions Security](GitHub%20Actions%20Security.md)
- [Vulnerable and Outdated Components](../OWASP%20Top%2010/Vulnerable%20and%20Outdated%20Components.md)

## Resources

- Alex Birsan, "Dependency Confusion" (2021) - the original research
