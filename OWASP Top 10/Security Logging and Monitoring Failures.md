# Security Logging and Monitoring Failures (A09:2021)

This category is about **not being able to detect or respond** to an attack because logging is missing, incomplete, or nobody is watching. It rarely causes the breach itself, but it lets breaches go undetected for months and cripples incident response and forensics. Studies repeatedly show breaches take ~200 days to detect - usually a logging/monitoring failure.

## Common failures

- **Login attempts, access-control failures, and input-validation failures are not logged**
- Logs contain **no useful context** (no timestamp, user, source IP, action)
- Logs stored **only locally** (an attacker deletes them) - not shipped to a central SIEM
- **No alerting** - logs exist but nobody is notified of suspicious patterns
- **No integrity protection** - logs can be modified/deleted
- **Sensitive data logged** in plaintext (passwords, tokens) - itself a risk

## What to log (with enough detail)

```text
- Authentication: success AND failure (who, when, from where)
- Authorization failures (403s, access-control denials)
- Input validation failures / suspected injection attempts
- High-value actions: password change, role change, money transfer, data export
- Admin actions and config changes
Each event: timestamp (UTC), user/session id, source IP, action, outcome
```

## Good vs bad logging (developer view)

```python
# BAD - no context, and logging the password!
print("login failed")

# GOOD - structured, contextual, no secrets
logger.warning("auth.login.failure", extra={
    "user": username, "ip": request.remote_addr,
    "ts": datetime.utcnow().isoformat(), "reason": "bad_password"
})
```

## How a pentester assesses this

- During testing, note whether your **attacks trigger any visible response** (blocking, alerts, account lockouts).
- Check for verbose errors that leak data (opposite problem, but same area).
- In an assumed-breach/purple-team engagement, verify whether SOC detects your actions.

## Tools (defensive / blue team)

- **SIEM**: [Wazuh](https://wazuh.com/), Splunk, Elastic (ELK), Microsoft Sentinel
- **Endpoint/telemetry**: Sysmon, auditd, osquery
- **Alerting**: Grafana/Prometheus, SIEM correlation rules
- See [SIEM](../Terminology/SIEM.md) for more.

## Mitigation - the fix

- Log all security-relevant events with **sufficient, structured context** (avoid secrets).
- Ship logs to a **central, tamper-resistant** store (SIEM); protect integrity.
- Set up **real-time alerting** on suspicious patterns (brute force, privilege changes).
- Have an **incident response plan** and test it (tabletop + drills).
- Retain logs long enough for investigations; monitor and review regularly.

## Practice

- Build a home SOC lab with Wazuh/ELK + Sysmon; TryHackMe SOC/Blue Team paths

## Reference

- [OWASP A09:2021](https://owasp.org/Top10/A09_2021-Security_Logging_and_Monitoring_Failures/)
