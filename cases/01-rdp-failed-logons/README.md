# Investigation: Failed RDP Logons from an External IP

> Personal SOC lab / practice case using synthetic data.  
> This project does not represent professional SOC experience.

## TL;DR

Caso práctico de SOC L1 sobre múltiples intentos fallidos de autenticación RDP desde una IP externa hacia varias cuentas.

El objetivo es analizar las evidencias, comprobar si hubo algún acceso exitoso desde la IP sospechosa y realizar un triage inicial antes de decidir si la alerta debe escalarse.

## Scenario

A security alert reports multiple failed login attempts against a Windows host.

Initial information:

- Destination host: `192.168.1.25`
- Service: RDP
- Destination port: `3389/TCP`
- Failed attempts: `24`
- Time window: `3 minutes`
- Accounts targeted:
  - `administrator`
  - `admin`
  - `backup`
- Successful login from the suspicious source observed: No

The events used in this investigation are synthetic and were created specifically for this personal lab.

## Investigation goals

The investigation will try to determine:

1. What happened?
2. Where did the activity originate?
3. Which accounts were targeted?
4. Was the activity normal or suspicious?
5. Did any authentication succeed from the suspicious source?
6. Does the source IP have relevant reputation information?
7. Is escalation required?

## Evidence

Evidence files are stored in:

`evidence/`

The dataset contains both suspicious and normal authentication events so the analysis is not based only on the alert itself.

## Analysis

The source IP `203.0.113.45` generated 24 failed RDP authentication attempts in approximately 3 minutes against three different accounts: `administrator`, `admin` and `backup`.

The high frequency of attempts and the use of multiple administrative-style accounts suggest automated credential guessing activity rather than a normal user error.

No successful logon from the same source IP was observed in the reviewed dataset.

## Timeline

- **14:27:10** — Successful RDP logon by user `irene` from internal IP `192.168.1.10`.
- **14:28:02** — First failed RDP authentication attempt from external IP `203.0.113.45`.
- **14:28:02 – 14:31:06** — 24 failed RDP authentication attempts against `administrator`, `admin` and `backup`.
- **14:31:40** — Successful SMB-related logon by `backup` from internal IP `192.168.1.15` using port `445`.
- **14:32:05** — Separate failed RDP logon by `guest` from internal IP `192.168.1.20`.
- **Reviewed result** — No successful logon from the suspicious source IP `203.0.113.45` was observed.

## Verdict

**Suspected malicious credential guessing activity with high confidence.**

The pattern of 24 failed RDP logon attempts in approximately 3 minutes against multiple administrative-style accounts is consistent with automated credential guessing.

No successful authentication from the suspicious source IP was observed in the reviewed dataset, so no account compromise was confirmed.

## MITRE ATT&CK

Possible related techniques:

- **T1110 — Brute Force**
  - Possible credential guessing activity based on repeated failed authentication attempts.

- **TA0006 — Credential Access**
  - The observed activity is consistent with an attempt to obtain valid credentials.

- **T1133 — External Remote Services**
  - RDP is being used as the remote access service in this scenario.

These mappings are based on the observed behavior and are not treated as confirmed techniques beyond the available evidence.

## Recommended actions

### L1 actions

- Validate the alert and review the full authentication log.
- Check whether the same source IP targeted other hosts or accounts.
- Confirm whether any successful logon occurred after the failed attempts.
- Enrich the source IP using reputation services such as VirusTotal or AbuseIPDB.
- Document the findings and escalate the case with supporting evidence.

### Actions for L2 / administration

- Review whether RDP needs to be exposed externally.
- Consider blocking the source IP according to the organisation's playbook.
- Review firewall, VPN, MFA and remote access controls.
- Consider account protection measures if further suspicious activity is found.

## Escalation note

**Alert:** Multiple failed RDP authentication attempts

**Source IP:** `203.0.113.45`  
**Destination:** `192.168.1.25:3389`  
**Accounts targeted:** `administrator`, `admin`, `backup`

24 failed RDP authentication attempts were observed in approximately 3 minutes from the same external source IP against multiple administrative-style accounts.

No successful authentication from the suspicious source IP was identified in the reviewed dataset.

The activity is consistent with suspected automated credential guessing.

**Recommendation:** Escalate for further investigation and review the external exposure of RDP and related remote access controls.

## Limitations

- This is a personal SOC training scenario using synthetic data.
- The source IP belongs to a documentation range and does not represent a real attacker.
- The dataset contains a limited number of authentication events and does not include full endpoint, firewall or network telemetry.
- No real SIEM or EDR platform was used for this first investigation.
- Conclusions are based only on the evidence available in the provided dataset.

The purpose of this project is to practise SOC L1 investigation, documentation and alert triage.
