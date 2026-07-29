# Active Directory Detection Lab

Most people learning cybersecurity do CTF challenges or follow along with tutorials. 
This isn't that. This is a full corporate network built from scratch, attacked with 
real tools, and defended with a SIEM and custom detection rules. Then an AI layer on 
top that does what junior SOC analysts spend most of their day doing.

The goal was to simply understand how real breaches actually happen, not just read about them. So that is what I have done over the summer.

---

## Why This Matters

Active Directory runs the internal networks of nearly every company on earth. When you 
hear about a major breach, the attackers almost always ended up in Active Directory. 
SolarWinds, Colonial Pipeline, the NHS ransomware attack, literally all of them involved 
attackers moving through AD undetected until it was too late.

The attacks in this lab are: Kerberoasting, Pass-the-Hash, BloodHound 
enumeration, etc. These are the exact techniques showing up in breach reports right now. 
Learning them in a controlled environment is how I can actually understand what defenders 
are up against.

The AI triage layer is where this gets more current. Security teams are drowning in 
alerts. The average SOC analyst spends hours manually reading logs and deciding what 
matters. Products like Microsoft Copilot for Security and CrowdStrike Charlotte AI are 
being built to solve this exact problem at enterprise scale. This project builds a 
stripped-down version of that from scratch, which is a better way to understand it than 
reading a product page.

---

## What's In Here

The lab has four machines talking to each other on an isolated internal network:

A Domain Controller running Windows Server 2022 with Active Directory, DNS, and a 
handful of intentionally vulnerable user accounts including a service account set up 
specifically for Kerberoasting. A Windows client joined to the domain, simulating an 
employee workstation. Kali Linux as the attacker machine. And Splunk as the SIEM 
collecting logs from both Windows machines in real time.

The project runs in four phases. First, building the environment and getting everything 
connected. Second, running attacks from Kali against the AD environment. Third, writing 
Splunk detection rules that catch each attack in the logs. Fourth, building a Python 
script that reads those Splunk alerts, sends them to an LLM, and gets back a plain 
English explanation of what happened, how severe it is, and what to do about it, which will
be delivered to Slack automatically.

---

## Attacks Covered

Every attack in this lab is a real technique with an MITRE ATT&CK mapping. After each 
one there's a writeup explaining what it looks like in logs and a Splunk query that 
catches it.

Kerberoasting targets service accounts with weak passwords. Any domain user can request 
a Kerberos ticket for a service account and take it offline to crack. There is no lockout nor any
alert by default.

AS-REP Roasting is similar, but targets accounts that don't require pre-authentication. 
The attacker doesn't even need valid credentials to start.

Password Spraying tries one common password against every account in the domain. It 
stays under lockout thresholds by going slow and wide instead of fast and narrow.

Pass-the-Hash lets an attacker authenticate as a user using their NTLM hash instead of 
their actual password. The hash gets stolen from memory and reused without ever cracking 
it.

BloodHound maps every user, group, computer, and permission relationship in the domain 
and draws the shortest path to Domain Admin. Attackers use it to find attack paths that 
no human would spot manually.

Privilege Escalation is the endgame, where attackers try to use everything above to climb from a regular user account to Domain Admin, which means total control of the entire network.

---

## Detection Engineering

For every attack there's a corresponding Splunk SPL query and Sigma rule. Sigma is the 
industry standard format for sharing detection logic across different SIEM platforms. 
The rules in this repo can be converted to work with Elastic, Microsoft Sentinel, or 
any other SIEM with a converter tool.

The detection writeups follow a consistent format: what the attack does, what EventCode 
it generates, what the query looks like, and what legitimate activity might cause false 
positives. That last part matters because a detection rule that fires constantly gets 
ignored, which is almost worse than no rule at all.

---

## AI Triage Capstone

The capstone is a Python script that sits on top of Splunk and does first-pass alert 
triage automatically.

It pulls new alerts from Splunk via the API, sends the raw log data to an LLM with a 
prompt that asks for severity, attack classification, affected accounts, and recommended 
response, then formats that into a Slack message that a human analyst can act on 
immediately.

The point is not that AI replaces the analyst. The point is that an analyst who gets a 
Slack message saying "High severity — Kerberoasting detected against svcbackup from 
192.168.10.12 at 14:32, recommend investigating service account permissions immediately" 
can respond in seconds instead of minutes. At scale, that difference matters.

---

## Project Structure
/
├── README.md
├── docs/
│   ├── 01-virtualbox-setup.md
│   ├── 02-active-directory-setup.md
│   ├── 03-splunk-setup.md
│   ├── 04-attacks.md
│   ├── 05-detections.md
│   └── 06-ai-triage.md
├── detections/
│   ├── kerberoasting.yml
│   ├── asrep-roasting.yml
│   ├── password-spraying.yml
│   ├── pass-the-hash.yml
│   ├── bloodhound-enumeration.yml
│   └── privilege-escalation.yml
├── scripts/
│   └── ai-triage.py
└── screenshots/
---

## Status

The lab environment is fully built and Splunk is collecting live logs from both Windows 
machines. Attacks and detections are in progress. The AI triage capstone will be added 
in August once the detection layer is complete.

---

## Background

This was built over the summer between my freshman and sophomore year at UNC Charlotte 
where I study Computer Science with a concentration in Cybersecurity. The AI triage 
component connects directly to my honors thesis research on AI agent security, which 
looks at how AI systems get attacked rather than how they defend. Understanding both 
sides is the only way to do either one well.

