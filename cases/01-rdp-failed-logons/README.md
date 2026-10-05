# Investigation: Suspected RDP Credential Guessing from an External Source

> Personal SOC lab / practice case using synthetic data.  
> This project does not represent professional SOC experience.

## TL;DR

Caso práctico de SOC L1 sobre una alerta de múltiples intentos fallidos de autenticación RDP contra un host Windows.

El análisis identifica un patrón automatizado de intentos contra varias cuentas. No se observa ningún acceso exitoso desde la IP sospechosa, aunque un inicio de sesión posterior de la cuenta `backup` desde un host interno requiere validación adicional.

## Scenario

A security alert was triggered after multiple failed authentication events were observed against a Windows host within a short period of time.

Alert information:

- Destination host: `192.168.1.25`
- Service: RDP
- Destination port: `3389/TCP`
- Alert condition: Multiple failed logons from the same source within a short time window

The dataset used in this investigation is synthetic and was created specifically for this personal SOC lab.

The event schema is simplified and normalized for training purposes and does not represent the complete raw structure of Windows Security Event Logs.

## Investigation goals

The investigation aims to determine:

1. What activity triggered the alert?
2. Where did the activity originate?
3. Which accounts were targeted?
4. Does the pattern appear normal or automated?
5. Did any authentication succeed from the suspicious source?
6. Are there related successful authentications that require validation?
7. Is IP reputation enrichment possible?
8. Should the alert be escalated?

## Evidence

The synthetic dataset used for this investigation is available here:

[`evidence/rdp_failed_logons.csv`](evidence/rdp_failed_logons.csv)

The dataset contains suspicious and unrelated authentication events so that the investigation requires distinguishing relevant activity from background activity.

### Evidence summary

| Finding | Result |
|---|---|
| Suspicious source | `203.0.113.45` |
| Failed logons from suspicious source | 24 |
| Accounts targeted | `administrator`, `admin`, `backup` |
| Attempts per account | 8 each |
| Approximate duration | 3 minutes |
| Destination service | RDP / TCP 3389 |
| Successful authentication from suspicious source | None observed |
| Related event requiring validation | Successful `backup` authentication from `192.168.1.15` via SMB |

## Analysis

The source IP `203.0.113.45` generated **24 failed authentication attempts** between `14:28:02` and `14:31:06`.

The activity targeted three accounts:

- `administrator` — 8 attempts
- `admin` — 8 attempts
- `backup` — 8 attempts

The accounts were targeted in a repeating sequence:

`administrator → admin → backup`

The attempts also occurred at relatively regular intervals of approximately 7–9 seconds.

This repeated account rotation and timing pattern is more consistent with automated credential guessing than with a normal user repeatedly entering an incorrect password.

### Successful authentication review

No successful authentication from the suspicious source IP `203.0.113.45` was observed.

However, at `14:31:40`, approximately 34 seconds after the final suspicious attempt, the account `backup` successfully authenticated to the same destination host from the internal IP `192.168.1.15` using SMB (`TCP/445`).

This event does **not** prove that the external activity resulted in compromise because:

- the source IP is different;
- the source is internal;
- the service is SMB rather than RDP.

However, because the `backup` account had just been targeted, this successful authentication should be validated.

Possible explanations include:

- legitimate scheduled backup activity;
- expected authentication from a known internal server;
- use of compromised credentials from another internal host.

The available dataset does not contain enough historical information to determine which explanation is correct.

### Other event review

A single failed RDP logon against `guest` from internal IP `192.168.1.20` occurred at `14:32:05`.

Because it is a single isolated failure from a different source, it is treated as unrelated background activity unless additional events indicate otherwise.

## Investigation goals — results

| Question | Finding |
|---|---|
| What triggered the alert? | Repeated failed authentication attempts |
| Where did they originate? | `203.0.113.45` |
| Which accounts were targeted? | `administrator`, `admin`, `backup` |
| Normal or automated? | Pattern is consistent with automated credential guessing |
| Successful login from suspicious IP? | No |
| Related events? | Successful `backup` SMB authentication requires validation |
| IP reputation available? | No — documentation-range IP used for synthetic lab |
| Escalation required? | Yes |

## IP enrichment

The source IP `203.0.113.45` belongs to the TEST-NET-3 documentation range.

Because it is not a real public attacker IP, reputation enrichment using services such as VirusTotal or AbuseIPDB is not applicable in this synthetic case.

In a real investigation, the source IP would be enriched using threat intelligence and reputation services.

## Timeline

- **14:27:10 — Baseline / unrelated:** Successful RDP logon by `user01` from internal IP `192.168.1.10`.
- **14:28:02 — Suspicious activity begins:** First failed authentication from `203.0.113.45`.
- **14:28:02–14:31:06 — Suspicious:** 24 failed authentication attempts against `administrator`, `admin` and `backup`.
- **14:31:40 — Requires validation:** Successful SMB authentication by `backup` from internal IP `192.168.1.15`.
- **14:32:05 — Likely unrelated:** Single failed RDP authentication by `guest` from internal IP `192.168.1.20`.
- **Reviewed result:** No successful authentication from `203.0.113.45` was observed.

## Verdict

**Suspected automated credential guessing — medium-high confidence.**

Confirmed observations:

- 24 failed authentication attempts occurred from the same source.
- Three accounts were targeted.
- Each account received 8 attempts.
- The accounts were targeted in a repeating sequence.
- The attempts occurred at regular short intervals.
- No successful authentication from the suspicious source was observed.

The observed pattern is consistent with automated credential guessing.

A successful authentication involving the targeted `backup` account occurred shortly afterwards from a different internal host and via a different service. This does not confirm compromise, but it requires validation before compromise can be fully ruled out.

## MITRE ATT&CK

### Tactic

- **TA0006 — Credential Access**

### Possible technique

- **T1110 — Brute Force**

The activity may be related to:

- **T1110.001 — Password Guessing**
- **T1110.003 — Password Spraying**

The available dataset does not contain password-level information, so the exact sub-technique cannot be determined confidently.

### Exposure context

RDP (`TCP/3389`) is the remote service targeted in this scenario.

Because no successful remote access was observed from the suspicious source, external remote service usage is treated as context rather than a confirmed post-compromise technique.

## Recommended actions

### L1 actions

- Validate the alert and review the authentication events.
- Search for successful authentications from the suspicious source.
- Check whether the same source targeted additional accounts or hosts.
- Review activity associated with the targeted accounts.
- Validate the successful `backup` authentication from `192.168.1.15`.
- In a real case, enrich the source IP using threat intelligence services.
- Document the investigation and escalate the alert with supporting evidence.

### L2 / administration actions

- Validate whether `192.168.1.15` is an authorised backup server or expected source.
- Review historical authentication activity for the `backup` account.
- Review whether RDP should be externally reachable.
- Consider network blocking according to organisational playbooks.
- Review VPN, MFA, firewall and remote-access controls.
- Consider account-protection actions if evidence of compromise is discovered.

## Escalation note

**Priority:** Medium  
**Alert:** Multiple failed RDP authentication attempts  
**Source:** `203.0.113.45`  
**Destination:** `192.168.1.25:3389`  
**Accounts targeted:** `administrator`, `admin`, `backup`

24 failed authentication attempts were observed over approximately three minutes from the same source against three accounts.

Each account received eight attempts, with a repeating account rotation and regular timing pattern consistent with automated credential guessing.

No successful authentication from the suspicious source was observed.

However, the targeted account `backup` successfully authenticated approximately 34 seconds after the final suspicious attempt from internal host `192.168.1.15` over SMB.

This may represent legitimate backup activity, but the current dataset does not contain sufficient historical context to confirm that assumption.

**Recommendation:** Escalate for validation of the `backup` authentication and review the external exposure of RDP.

## Limitations

- This is a personal SOC training scenario using synthetic data.
- `203.0.113.45` belongs to a documentation range and does not represent a real attacker.
- The CSV uses a simplified normalized schema rather than raw Windows Event Log format.
- The dataset contains limited authentication telemetry.
- Historical authentication baselines are not available.
- Full endpoint, firewall, Active Directory and network telemetry are not available.
- No real SIEM or EDR platform was used for this investigation.
- IP reputation enrichment cannot be performed meaningfully on the documentation-range IP.
- Conclusions are limited to the evidence contained in the synthetic dataset.

The purpose of this project is to practise SOC L1 investigation, evidence review, documentation and alert triage.
