# Installing on Linux

This tarball installs the full stack as systemd `--user` services on a
single Linux PC.

## Requirements

- Ubuntu 22.04+ or Debian 12+ (other systemd-based distributions may work
  but are untested)
- `systemd` with user services (`systemctl --user`)
- The `can` kernel module (SocketCAN); also `vcan` if you want a virtual
  bus for testing without hardware
- `sudo` / root access to run the installer

## Install

1. Extract the tarball and enter the extracted directory (it contains
   `install.sh`, `uninstall.sh`, `dist/`, and `systemd/`):

   ```
   tar -xzf edge-insights-*-linux-*.tar.gz
   cd edge-insights-*-linux-*
   ```

2. Run the installer. It copies the stack into your home directory and
   registers the systemd `--user` services:

   ```
   sudo ./install.sh --start
   ```

   - Omit `--start` to install without starting; start later with
     `systemctl --user start analytics.target`.
   - Add `--with-vcan` to load the `vcan` kernel module during install
     (useful for a virtual test bus when no CAN hardware is attached).

   The installer also grants the CAN capture service the `CAP_NET_ADMIN`
   capability, so it can configure CAN interfaces on its own — no manual
   setup needed.

3. Open the portal:

   <http://localhost:36300>

   From there, select the CAN interface to capture and upload a DBC file
   to start decoding signals.

Other endpoints once the stack is running:

- Grafana — <http://localhost:36301>
- ReductStore — <http://localhost:36302>

## Managing the stack

Start or stop everything:

```
systemctl --user start analytics.target
systemctl --user stop  analytics.target
```

Start automatically at boot (opt-in):

```
systemctl --user enable analytics.target
```

Follow the logs:

```
journalctl --user -u 'analytics-*' -f --no-hostname -o short-iso
```

## Uninstall

```
sudo ./uninstall.sh
```

This stops the services and removes the installed binaries. It prompts
before deleting your data (analytics database, uploaded DBC files,
imported logs). To remove everything without prompting:

```
sudo ./uninstall.sh --purge
```
