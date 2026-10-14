# spcli Guide

`spcli` controls Secure Pipes from Terminal using the app's Apple Event interface. It uses saved connections and the same commands as [AppleScript](AppleScript.md).

## Install

1. Install and open Secure Pipes.
2. Open the **General** tab.
3. In **Command-Line Tool**, click **Install spcli**. macOS may request administrator authorization to install the command link.
4. Open a new Terminal window and run:

```sh
command -v spcli
spcli --version
spcli --help
```

If your shell cannot find the command, check that the installation directory (`/usr/local/bin` or `/opt/homebrew/bin`) is in your `PATH`.

You can also run the bundled tool directly without installing a command link, if Secure Pipes is in `/Applications`:

```sh
"/Applications/Secure Pipes.app/Contents/Helpers/spcli" --help
```

Use **General → Command-Line Tool → Uninstall spcli** to remove the command link. This does not uninstall Secure Pipes.

Automation is included in Secure Pipes Pro. The Free edition offers a shared 7-day AppleScript and `spcli` trial starting with the first automation command. `--help` and `--version` do not send automation commands.

## Quick start

Create and test a connection in the app first. List available connections, then replace the example name below with your own:

```sh
spcli list connections
spcli connect "Database Tunnel (Local Forward)"
spcli status "Database Tunnel (Local Forward)"
spcli disconnect "Database Tunnel (Local Forward)"
```

`connect` returns when the start request is accepted. Wait until `status` reports `connected` before using the tunnel. The app may display normal password or host-key prompts. macOS may also request permission for your terminal to control Secure Pipes.

## Command reference

| Command | Behavior |
| --- | --- |
| `spcli start` | Start Secure Pipes without showing its window |
| `spcli show` | Show and activate the application window |
| `spcli hide` | Hide windows without stopping connections |
| `spcli shutdown` | Quit without interactive confirmation and stop active connections |
| `spcli list connections [--json]` | List saved connection names with their types |
| `spcli list groups [--json]` | List tags used as connection groups |
| `spcli connect "<connection>"` | Request a connection start |
| `spcli disconnect "<connection>"` | Request a connection stop |
| `spcli status "<connection>" [--json]` | Return the current connection status |
| `spcli connect-group "<group>"` | Request starts for tagged tunnel connections |
| `spcli disconnect-group "<group>"` | Request stops for tagged tunnel connections |
| `spcli --help` | Show command help |
| `spcli --version` | Show the CLI version |

Quote names containing spaces or parentheses. A unique connection name alone is accepted; otherwise use the full name and type returned by `list connections`. `spcli` launches the registered Secure Pipes app when needed.

Groups use tags assigned to local forwards, remote forwards, and SOCKS proxies, not navigation folders. Inspect per-member results: a group start can stop processing after a failed request. A successful CLI exit does not guarantee that every group member started or connected. Poll individual statuses if your workflow depends on them.

## JSON output

List commands return JSON arrays of strings:

```sh
spcli list connections --json
spcli list groups --json
```

For example:

```json
[
  "Database Tunnel (Local Forward)",
  "Work Proxy (SOCKS Proxy)"
]
```

Status returns an object:

```sh
spcli status "Work Proxy (SOCKS Proxy)" --json
```

```json
{
  "connection": "Work Proxy (SOCKS Proxy)",
  "status": "connected"
}
```

Status values are `disconnected`, `connecting`, `connected`, `authentication failed`, `unknown host key`, `host key changed`, `invalid`, `error`, and `unknown`. `--json` applies to list and status commands; other commands return text.

## Wait for a connection in a shell script

Save this example as `wait-for-tunnel.sh`, replace the connection name, and run `sh wait-for-tunnel.sh`. It polls up to 60 times and leaves a successful connection running.

```sh
#!/bin/sh
target_connection='Database Tunnel (Local Forward)'
spcli connect "$target_connection" || exit 1

attempt=0
while [ "$attempt" -lt 60 ]; do
    state=$(spcli status "$target_connection") || exit 1
    case "$state" in
        connected)
            printf 'Connected: %s\n' "$target_connection"
            exit 0
            ;;
        'authentication failed'|'unknown host key'|'host key changed'|invalid|error)
            printf 'Connection failed: %s\n' "$state" >&2
            exit 1
            ;;
    esac
    attempt=$((attempt + 1))
    sleep 1
done

printf 'Timed out waiting for %s\n' "$target_connection" >&2
exit 1
```

Each status request can take additional time, so this is a polling limit rather than a strict 60-second deadline. If the script times out, the connection attempt may still be active. Stop it explicitly if needed:

```sh
spcli disconnect "Database Tunnel (Local Forward)"
```

## Exit codes

| Code | Meaning |
| --- | --- |
| `0` | Command completed successfully; a connect request was accepted, or a status query succeeded |
| `1` | Secure Pipes reported an error, such as an unknown connection or an expired automation trial |
| `2` | Invalid usage, missing argument, or unknown option |
| `3` | App not found, app could not launch, or Automation permission denied |
| `4` | Apple Event send, timeout, reply, or JSON encoding failure |

Check the status text as well as the exit code: `spcli status` can return `0` while reporting `disconnected` or a connection failure state. Errors are written to standard error.

## Troubleshooting

- **Command not found:** install the CLI from General, open a new Terminal window, and check `PATH`; alternatively use the bundled tool's full path.
- **App not found:** install and open Secure Pipes once so macOS registers it.
- **Automation denied:** enable permission for the caller under System Settings → Privacy & Security → Automation. On macOS 12, use System Preferences → Security & Privacy → Privacy → Automation.
- **Name not found or ambiguous:** copy the full value from `spcli list connections`.
- **Connection stays pending:** inspect the app for authentication, host-key, or network errors; test the connection manually.
- **Pro required:** check the automation trial or activate Pro in the License page.

Credentials remain managed by Secure Pipes. Do not place SSH passwords, private-key contents, or OTP codes in shell commands.

[Documentation index](readme.md) · [AppleScript guide](AppleScript.md)
