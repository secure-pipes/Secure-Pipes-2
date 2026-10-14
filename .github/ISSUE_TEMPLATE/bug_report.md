---
name: Bug Report
about: Report a reproducible problem with Secure Pipes
title: '[BUG] '
labels: bug
assignees: ''
---

<!--
Search existing issues before opening a new report.
Never include passwords, private keys, one-time codes, share passwords, or license keys.
Redact usernames, server addresses, and personal file paths in logs and screenshots.
-->

## Description
A clear, concise description of the problem.

## Steps to Reproduce
1. Describe the relevant setup, without secrets.
2. Explain the actions that trigger the problem.
3. Describe what happens.

## Expected Behavior
What did you expect to happen?

## Actual Behavior
What happened instead? Include the exact error message, if available.
Does it happen every time or only occasionally?

## Environment
- Secure Pipes version and build number:
- macOS version:
- Mac architecture: Apple silicon / Intel
- Affected feature: SSH / local forwarding / remote forwarding / SOCKS proxy / transparent proxy / DNS / SFTP / terminal / other
- Connection or proxy type, if applicable: SSH SOCKS / SOCKS5 / HTTP
- Authentication method, if applicable: password / identity file / other (do not include credentials)

## Connection Details (if applicable)
- Remote operating system and SSH server, if known:
- Affected traffic: TCP / UDP / DNS
- Relevant routing rule and DNS mode, with sensitive values redacted:
- Can the same destination be reached without Secure Pipes?
- Does stopping the affected connection or transparent proxy restore connectivity?

## Logs or Screenshots (optional)
Include only the relevant, redacted log lines and the approximate time of the problem.
For a crash, attach a redacted crash report if available.
Do not attach configuration backups or sharing QR codes.

## Additional Context (optional)
Mention recent changes, network conditions, sleep/wake behavior, or any workaround you tried.
