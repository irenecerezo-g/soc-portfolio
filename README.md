# SOC Portfolio

Personal SOC / Blue Team learning portfolio with practice cases and lab notes.

## About me

I am a DAM and DAW graduate currently transitioning into cybersecurity and SOC environments.

I am currently studying:

- IBM SkillsBuild – Cybersecurity Fundamentals
- Microsoft SC-900 – exam preparation
- Cisco CCST Cybersecurity – upcoming

This repository contains personal labs and practice cases focused on SOC L1 skills such as log analysis, alert triage, incident documentation and basic security investigation.

> All cases in this repository are personal labs or synthetic scenarios. They do not represent professional SOC experience.

## Projects

### [01. Suspected RDP Credential Guessing from an External Source](cases/01-rdp-failed-logons/)

Investigation of repeated failed RDP authentication attempts against a Windows host, including analysis of a related successful login requiring validation.

Includes:

- synthetic event evidence
- evidence summary
- timeline
- triage analysis
- MITRE ATT&CK mapping
- escalation note
- recommended actions
- limitations

**Status:** Completed

## Skills demonstrated

- Log analysis
- IP and port analysis
- RDP authentication analysis
- Alert triage
- Incident documentation
- Basic escalation workflow

## Next cases

- Phishing email investigation
- SIEM-based alert investigation

## Contact

LinkedIn: [https://www.linkedin.com/in/irene-cerezo-gomez-it/](https://www.linkedin.com/in/irene-cerezo-gomez-it/)

## Resumen en español

Portfolio personal de aprendizaje orientado a SOC / Blue Team.

Incluye casos prácticos de laboratorio y escenarios sintéticos documentados con análisis, evidencias y criterios de escalado.

Mi objetivo es desarrollar experiencia práctica para optar a mi primera posición como SOC Analyst L1 / Junior.

## What I learned

This lab helped me understand that a successful login occurring shortly after failed attempts does not automatically confirm compromise.

The `backup` authentication initially looked especially suspicious because the account had just been targeted. However, the different source IP and SMB service meant that it had to be treated as an event requiring validation rather than direct evidence of compromise.

I also learned the importance of separating confirmed observations from hypotheses when documenting and escalating a SOC alert.
