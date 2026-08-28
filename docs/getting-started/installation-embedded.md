# Installing on an Embedded Device

Edge Insights can run headless on an embedded Linux device, for example an
arm64 industrial PC. The services run continuously in the background, and
the portal is accessed from a browser on another machine on the same
network.

Embedded devices install from a release tarball. This is also the only
way to install on arm64. If you're installing on a laptop or desktop PC
instead, see [Installing on a Linux desktop](installation-linux.md).

## Requirements

- An arm64 or x86_64 device. Tarballs are published for both
  architectures
- Ubuntu 22.04+ or Debian 12+. Other systemd-based distributions may work
  but are untested.
- `systemd` with user services (`systemctl --user`)
- The `can` kernel module, i.e. SocketCAN, also `vcan` if you want a virtual
  bus for testing without hardware
- `sudo` / root access to run the installer
- About 1 GB of free space for the install location, `/opt` by default

## Install

1. Download the tarball for your architecture,
   `edge-insights-<version>-linux-arm64.tar.gz` or
   `...-linux-x86_64.tar.gz`, extract it, and enter the extracted
   directory. It contains `install.sh`, `uninstall.sh`, `dist/`, and
   `systemd/`:

    ```bash
    tar -xzf edge-insights-*-linux-*.tar.gz
    cd edge-insights-*-linux-*
    ```

2. Run the installer with `--start --access network --boot-autostart`. It
   installs the stack into `/opt/edge-insights`, registers the systemd
   `--user` services, starts them immediately, makes the portal reachable
   over the network, and brings the stack back up after every reboot:

    ```bash
    sudo ./install.sh --start --access network --boot-autostart
    ```

    - `--access network` makes the portal reachable from other machines on
      the network. See [Network access](#network-access).
    - `--boot-autostart` brings the stack up when the device boots, with
      nobody logged in, so it keeps recording on its own. See
      [Starting at boot](#starting-at-boot).
    - Omit `--start` to install without starting. Start later with
      `systemctl --user start analytics.target`.
    - Add `--with-vcan` to load the `vcan` kernel module during install.
      Useful for a virtual test bus when no CAN hardware is attached.
    - Add `--prefix <dir>` to install somewhere other than
      `/opt/edge-insights`. Use it when the device's root filesystem is
      small and your storage is on another disk. Pass the same directory to
      `uninstall.sh` later.

    Edge Insights is installed once and shared by every user on the
    device. What it records is stored per user, in
    `~/.local/share/edge-insights`.

3. Open the portal from a browser on another machine on the same network:

    `http://<device-hostname-or-ip>:36300`

    On the device itself it is also available at
    <http://localhost:36300>. From there, select the CAN interface to
    capture and upload a DBC file to start decoding signals.

    Reaching the device through a custom DNS name, a reverse proxy, or a
    tunnel instead of its hostname or IP needs an extra step: see
    [The portal loads, but every save or upload fails](../troubleshooting/index.md#the-portal-loads-but-every-save-or-upload-fails).

## Upgrade

Extract the new tarball and run the installer again. The installer
remembers the `--access` and `--boot-autostart` choices from the previous
install, so a plain re-run keeps the device configured the way it was:

```bash
sudo ./install.sh --start
```

An upgrade does not touch your data. If you installed with `--prefix`,
pass it again.

## Managing the stack

Start or stop everything:

```bash
systemctl --user start analytics.target
systemctl --user stop  analytics.target
```

Follow the logs:

```bash
journalctl --user -u 'analytics-*' -f --no-hostname -o short-iso
```

## Network access

`--access network` binds the portal and the Grafana dashboards to all
network interfaces, so you can reach them from another machine. Without it
the portal is reachable only on the device itself, at `localhost`.

Use network access only on a trusted network. The portal has no login.

The installer remembers the choice, so you do not need to repeat the flag
when you install again. To make the portal local to the device again:

```bash
sudo ./install.sh --access local
```

## Starting at boot

Use the installer's `--boot-autostart` flag to start the stack at boot.
Do not enable the services by hand:

```bash
sudo ./install.sh --boot-autostart
```

The installer remembers this choice, and `--no-boot-autostart` turns it
off again. With it on, the CAN Settings page gains a **Boot Autostart**
option that controls whether capture also starts, or only the portal. See
[CAN Settings](../portal/can-settings.md#interface-and-bus-settings).

## Uninstall

```bash
sudo ./uninstall.sh
```

This stops the services and removes the installed program. It prompts
before deleting your data, e.g. analytics database, uploaded DBC files,
imported logs. To remove everything without prompting:

```bash
sudo ./uninstall.sh --purge
```

If you installed with `--prefix`, pass the same directory. Only the data
of the user running the uninstaller is removed. Other users' data is
kept.
