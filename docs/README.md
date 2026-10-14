# Secure Pipes Documentation

Use these guides to control saved Secure Pipes connections from scripts or Terminal.

| Guide | What it covers |
| --- | --- |
| [AppleScript](AppleScript.md) | Script Editor, `osascript`, connection and group commands, status polling, and application controls |
| [spcli](spcli.md) | CLI installation, commands, JSON output, exit codes, and shell automation |

Both interfaces use the same Secure Pipes automation commands. Secure Pipes Pro includes automation; the Free edition offers a shared 7-day AppleScript and `spcli` trial starting with the first automation command.

Before starting, install and open Secure Pipes, create a connection, and verify that it works in the app. Approve the macOS Automation permission request when prompted. Normal password and host-key prompts may still require interaction in the app.

Connection start commands return when the request is accepted. Check the connection status before using a tunnel. Groups refer to connection tags assigned to local forwards, remote forwards, and SOCKS proxies.

[Back to the repository README](../README.md) · [Website and downloads](https://secure-pipes.app)
