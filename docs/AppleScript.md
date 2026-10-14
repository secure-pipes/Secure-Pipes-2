# AppleScript Guide

AppleScript lets you list saved Secure Pipes connections, start or stop them, inspect their status, and control tagged connection groups.

## Getting started

1. Install and open Secure Pipes once so macOS registers its scripting dictionary.
2. Create a connection and test it in the app.
3. Open Script Editor, paste an example below, and click Run.
4. Allow Script Editor to control Secure Pipes if macOS requests Automation permission.

To inspect the installed command dictionary, choose **File → Open Dictionary…** in Script Editor and select **Secure Pipes**.

Automation is included in Secure Pipes Pro. The Free edition provides a shared 7-day AppleScript and `spcli` trial, starting with the first automation command. After the trial, activate Pro to continue.

## List connections

```applescript
tell application "Secure Pipes"
    list connections
end tell
```

The result contains names with their types, such as `Database Tunnel (Local Forward)` and `Work Proxy (SOCKS Proxy)`. Replace example names with values returned by `list connections`. A unique name alone also works; use the full name and type if a name is ambiguous.

## Start, inspect, and stop a connection

```applescript
tell application "Secure Pipes"
    connect connection "Database Tunnel (Local Forward)"
end tell
```

This accepts the start request; it does not wait for the tunnel to connect.

```applescript
tell application "Secure Pipes"
    connection status "Database Tunnel (Local Forward)"
end tell
```

```applescript
tell application "Secure Pipes"
    disconnect connection "Database Tunnel (Local Forward)"
end tell
```

Status values are `disconnected`, `connecting`, `connected`, `authentication failed`, `unknown host key`, `host key changed`, `invalid`, `error`, and `unknown`.

## Wait for a connection, then open a URL

This example polls for up to 60 seconds after the start request. Replace the connection name and URL with your own. It leaves a successful connection running.

```applescript
set targetConnection to "Web Tunnel (Local Forward)"
set postConnectURL to "http://127.0.0.1:8080/"
set failureStates to {"authentication failed", "unknown host key", "host key changed", "invalid", "error"}

tell application "Secure Pipes"
    connect connection targetConnection
end tell

set deadline to (current date) + 60
repeat
    tell application "Secure Pipes"
        set currentState to connection status targetConnection
    end tell
    if currentState is "connected" then exit repeat
    if currentState is in failureStates then
        error "Connection failed: " & currentState
    end if
    if (current date) is greater than or equal to deadline then
        error "Timed out waiting for the connection."
    end if
    delay 1
end repeat

open location postConnectURL
```

A timeout ends this script, not the connection attempt. To stop the attempt, run `disconnect connection` for the same connection. Inspect authentication or host-key errors in the app and complete the normal verification flow.

## Connection groups

Assign the same tag, such as `Work`, to the local forwards, remote forwards, and SOCKS proxies you want to manage together.

```applescript
tell application "Secure Pipes"
    list connection groups
end tell
```

```applescript
tell application "Secure Pipes"
    connect group "Work"
end tell
```

```applescript
tell application "Secure Pipes"
    disconnect group "Work"
end tell
```

Groups are tag-based, not navigation folders. Start results report each accepted or skipped request. If a start request fails, remaining members may not be started; inspect the returned results and individual connection statuses. Group commands do not wait for every member to connect or enforce dependency order.

## Application controls

| Command inside `tell application "Secure Pipes"` | Behavior |
| --- | --- |
| `start` | Start Secure Pipes without showing its window |
| `show` | Show and activate the application window |
| `hide` | Hide application windows without disconnecting connections |
| `shutdown` | Quit without the interactive quit confirmation and stop active connections |

Targeting Secure Pipes from AppleScript launches it if needed. Use `shutdown` to quit; `exit` is an AppleScript loop keyword.

## Run from Terminal

```sh
osascript -e 'tell application "Secure Pipes" to list connections'
osascript -e 'tell application "Secure Pipes" to connection status "Work Proxy (SOCKS Proxy)"'
```

Save a longer script as `connect.applescript`, then run:

```sh
osascript connect.applescript
```

Allow Terminal to control Secure Pipes when macOS prompts. The approved caller can differ from Script Editor.

## Troubleshooting

| Symptom | What to check |
| --- | --- |
| Secure Pipes or its dictionary cannot be found | Install and open the app once; verify you selected the correct app in Script Editor |
| Automation permission denied | On recent macOS releases, check System Settings → Privacy & Security → Automation; on macOS 12, use System Preferences → Security & Privacy → Privacy → Automation |
| Connection not found or name is ambiguous | Use a full value returned by `list connections` |
| Group not found | Assign the tag to at least one local forward, remote forward, or SOCKS proxy |
| Request accepted but tunnel is unavailable | Poll `connection status`; check the app for password prompts, host-key verification, or connection errors |
| Trial ended | Activate Secure Pipes Pro in the License page |

Configure credentials through the app. Scripts do not need to include SSH passwords, private-key contents, or OTP codes.

[Documentation index](readme.md) · [spcli guide](spcli.md)
