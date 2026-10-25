# Openfortivpn Auto Logon Script

## Quick Start

1. **Install Dependencies**:
   - Ensure you have `openfortivpn` and `expect` installed on your system.
   - `npm i` to install Node.js dependencies, like `crypto-js`.
2. **Set Up Environment Variables**:
   - `cp .env.example .env` and fill in your VPN details and TOTP secret.
   - `LOG_DIR` and `LOG_RETENTION_DAYS` are optional; see [Logs](#logs).
3. **Run the Script**:
   - It's recommended to create an alias for `connect-vpn` in your shell configuration file (e.g., `.bashrc`, `.zshrc`):
     ```sh
     alias connect-vpn='path/to/your/script/connect-vpn'
     ```
   - After sourcing the rc file, you can run it from anywhere using `connect-vpn`.

## Auto Reconnect

- When the tunnel drops, `connect-vpn` logs in again with a fresh TOTP code.
- It gives up after 3 consecutive failed reconnect attempts; a successful connection resets the count.
- Press `Ctrl+C` to log out and quit.

## Background Mode

| Command                | Description                                                         |
| ---------------------- | ------------------------------------------------------------------- |
| `connect-vpn`          | Connect in the foreground                                           |
| `connect-vpn -b`       | Connect in the background; closing the terminal does not disconnect |
| `connect-vpn --status` | Show whether an instance is running                                 |
| `connect-vpn --log`    | Follow the log file                                                 |
| `connect-vpn --stop`   | Log out and stop the running instance                               |

Only one instance runs at a time.

## Bypass Routes

- The VPN takes over all IPv4 traffic, and some sites reject its exit address.
- Hosts listed in `bypass-routes.txt` skip the tunnel and go through the local gateway: `cp bypass-routes.example bypass-routes.txt`, then add one IPv4 address, CIDR range, or domain name per line.
- `connect-vpn` applies the list each time the tunnel comes up; domain names are resolved to IPv4 at that moment.
- `connect-vpn` removes the applied routes each time the tunnel goes down, since they point at the gateway of the network in use at connect time.
- To apply an edited list without reconnecting, run `sudo ./bypass-routes <VPN_IP>`.
- To remove leftover routes after an abnormal exit, run `sudo ./bypass-routes --clear`.
- Applied routes are recorded in `/var/run/bypass-routes.applied`; entries later commented out or deleted from the list are removed on the next run.
- Requires passwordless `sudo`; otherwise the step is skipped and the error is logged.

## Logs

- Each connection attempt, reconnect, and stop is preceded by an ISO 8601 timestamp, both on screen and in the log.
- The log is written to `connect-vpn.log` in `LOG_DIR`, which defaults to `~/Library/Logs/openfortivpn-auto-logon` on macOS and `~/.local/state/openfortivpn-auto-logon` elsewhere.
- At the first connection attempt of each day, the previous log is archived as `connect-vpn.YYYY-MM-DD.log`, and archives older than `LOG_RETENTION_DAYS` (default 14) are deleted.
