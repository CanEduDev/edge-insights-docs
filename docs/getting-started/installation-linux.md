# Installing on Linux

Edge Insights runs on Linux in two ways:

- **Desktop PC** — covered on this page. Edge Insights is installed as a
  normal desktop application and started from the application menu.
- **Embedded / headless device** — the device monitors a bus continuously
  and the portal is accessed over the network. See
  [Installing on an embedded device](installation-embedded.md).

## Requirements

- An x86_64 PC (arm64 builds are also available; see the
  [embedded install page](installation-embedded.md))
- Ubuntu 22.04+ or Debian 12+ (other systemd-based distributions may work
  but are untested)
- `systemd` with user services (`systemctl --user`)
- The `can` kernel module (SocketCAN); also `vcan` if you want a virtual
  bus for testing without hardware
- `sudo` / root access to run the installer

## Install

1. Download the tarball for your architecture
   (`edge-insights-<version>-linux-x86_64.tar.gz`), extract it, and enter
   the extracted directory (it contains `install.sh`, `uninstall.sh`,
   `dist/`, and `systemd/`):

   ```
   tar -xzf edge-insights-*-linux-*.tar.gz
   cd edge-insights-*-linux-*
   ```

2. Run the installer. It copies the stack into your home directory,
   registers the background services, and adds **Edge Insights** to your
   application menu:

   ```
   sudo ./install.sh
   ```

   - Add `--with-vcan` to load the `vcan` kernel module during install
     (useful for a virtual test bus when no CAN hardware is attached).

   The installer also grants the CAN capture service the `CAP_NET_ADMIN`
   capability, so it can configure CAN interfaces on its own — no manual
   setup needed.

## Launch

Start **Edge Insights** from the application menu, like any other desktop
application.

The application starts the analytics services and opens the portal in its
own window. When you close the window, the services it started are stopped
again. Nothing keeps running in the background.

## Uninstall

From the extracted tarball directory:

```
sudo ./uninstall.sh
```

This stops the services and removes the installed application. It prompts
before deleting your data (analytics database, uploaded DBC files,
imported logs). To remove everything without prompting:

```
sudo ./uninstall.sh --purge
```
