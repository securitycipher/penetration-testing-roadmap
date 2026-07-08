# SAST (Static Application Security Testing)

Imagine you're building a house. Before you move in, you want to make sure it's safe and secure. You might inspect the structure, check the doors and windows, and ensure there are no hidden dangers like faulty wiring or weak foundations. In the world of software development, SAST is like doing a safety inspection on the code of the application you're building.

## Here's how it works:

- Static: SAST looks at the code itself, without actually running the program. It's like examining the blueprint of the house before it's built, rather than waiting until it's constructed.

- Application: This refers to the software you're developing, whether it's a website, a mobile app, or any other type of software.

- Security Testing: SAST focuses specifically on security issues. It looks for vulnerabilities and weaknesses in the code that could be exploited by hackers or malicious users.

## Now, let's break down why SAST is important and how it's done:

- Identifying Vulnerabilities: Just like you'd want to find any weak spots in your house before you move in, SAST helps developers find vulnerabilities in their code before the software is deployed. This could include things like SQL injection, cross-site scripting (XSS), or insecure data storage.

- Automated Analysis: SAST tools automatically scan the codebase, looking for patterns and indicators of potential security issues. This is much faster than manually reviewing every line of code, especially in large projects.

- Early Detection: By catching security flaws early in the development process, SAST helps developers address them before they become bigger problems. It's like fixing a crack in the foundation of your house before it causes serious damage.

- Integration into Development Workflow: SAST tools can be integrated into the development process, running automatically whenever code is committed or deployed. This ensures that security is considered throughout the entire development lifecycle.

- Educational Tool: For someone new to security, SAST can also be a learning tool. By highlighting vulnerabilities and explaining why they're risky, it helps developers understand security best practices and how to write more secure code in the future.

Overall, SAST is a valuable tool for developers, helping them build software that's not only functional and efficient but also secure from potential threats. Just like you wouldn't want to move into a house with hidden dangers, you wouldn't want to deploy software without first ensuring its security through tools like SAST.

---

## SAST tools and commands

```bash
# Semgrep — fast, rule-based, multi-language (great default choice)
semgrep --config=auto ./src
semgrep --config="p/owasp-top-ten" ./src

# Bandit — Python
bandit -r ./app

# gosec — Go
gosec ./...

# Brakeman — Ruby on Rails
brakeman -o report.html

# CodeQL — deep semantic analysis (GitHub)
codeql database create db --language=javascript
codeql database analyze db --format=sarif-latest -o results.sarif
```

## Strengths and limits

| Strength | Limitation |
|----------|------------|
| Finds bugs early (shift-left) | High false-positive rate |
| Full code coverage | Can't find runtime/config/auth logic flaws |
| No running app needed | Language-specific tooling |
| Pinpoints exact file/line | Misses issues that only appear at runtime (that's [DAST](DAST.md)) |

## Where it fits for a pentester

You'll often review SAST output during a code-assisted (white-box) test — use it to *guide* manual review, then confirm exploitability by hand. A SAST alert is a lead, not a confirmed finding.

## Related

- [DAST](DAST.md) · [IAST](IAST.md) · [SCA](SCA.md)
- [Testing Approaches](Testing%20Approaches.md)
