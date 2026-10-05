# Investigation: Failed RDP Logons from an External IP

> Personal SOC lab / practice case using synthetic data.  
> This project does not represent professional SOC experience.

## TL;DR

Caso práctico de SOC L1 sobre múltiples intentos fallidos de autenticación RDP desde una IP externa hacia varias cuentas.

El objetivo es analizar las evidencias, comprobar si hubo algún acceso exitoso y realizar un triage inicial antes de decidir si la alerta debe escalarse.

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
- Successful login observed: No

The events used in this investigation are synthetic and were created specifically for this personal lab.

## Investigation goals

The investigation will try to determine:

1. What happened?
2. Where did the activity originate?
3. Which accounts were targeted?
4. Was the activity normal or suspicious?
5. Did any authentication succeed?
6. Does the source IP have relevant reputation information?
7. Is escalation required?

## Evidence

Evidence files will be stored in:

`evidence/`

The dataset will contain both suspicious and normal authentication events so the analysis is not based only on the alert itself.

## Analysis

To be completed after reviewing the event dataset.

## Timeline

To be completed during the investigation.

## Verdict

Pending investigation.

## MITRE ATT&CK

Possible techniques will be mapped only after analysing the evidence.

## Recommended actions

Pending investigation.

## Limitations

This is a personal training scenario using synthetic data.

The purpose of the project is to practise SOC L1 investigation, documentation and alert triage.
