# Installing on an Embedded Device

Edge Insights can run headless on an embedded Linux device, for example an
arm64 industrial PC. The services run continuously in the background, and
the portal is accessed from a browser on another machine on the same
network.

If you're installing on a desktop PC instead, see
[Installing on Linux](installation-linux.md).

## Requirements

- An arm64 or x86_64 device — tarballs are published for both
  architectures
- Ubuntu 22.04+ or Debian 12+ (other systemd-based distributions may work
  but are untested)
- `systemd` with user services (`systemctl --user`)
- The `can` kernel module (SocketCAN); also `vcan` if you want a virtual
  bus for testing without hardware
- `sudo` / root access to run the installer

## Install

1. Download the tarball for your architecture
   (`edge-insights-<version>-linux-arm64.tar.gz` or
   `...-linux-x86_64.tar.gz`), extract it, and enter the extracted
   directory (it contains `install.sh`, `uninstall.sh`, `dist/`, and
   `systemd/`):

   ```
   tar -xzf edge-insights-*-linux-*.tar.gz
   cd edge-insights-*-linux-*
   ```

2. Run the installer with `--start`. It copies the stack into your home
   directory, registers the systemd `--user` services, and starts them
   immediately — no desktop session needed:

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

3. Open the portal from a browser on another machine on the same network:

   `http://<device-hostname-or-ip>:36300`

   (On the device itself it is also available at
   <http://localhost:36300>.) From there, select the CAN interface to
   capture and upload a DBC file to start decoding signals.

Other endpoints once the stack is running (same host, different ports):

- Grafana — port `36301`
- ReductStore — port `36302`

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
