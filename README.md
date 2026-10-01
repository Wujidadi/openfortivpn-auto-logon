# Openfortivpn Auto Logon Script

## Quick Start

1. **Install Dependencies**:
   - Ensure you have `openfortivpn` and `expect` installed on your system.
   - `npm i` to install Node.js dependencies, like `crypto-js`.
2. **Set Up Environment Variables**:
   - `cp .env.example .env` and fill in your VPN details and TOTP secret.
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
