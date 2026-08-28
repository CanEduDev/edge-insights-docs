# Installing on a Linux Desktop

This page covers installing Edge Insights on a laptop or desktop PC. It
is installed as a normal desktop application and started from the
application menu.

For a device that monitors a bus continuously and is accessed over the
network, see
[Installing on an embedded device](installation-embedded.md).

## Requirements

- An x86_64 PC. For arm64, see the
  [embedded install page](installation-embedded.md).
- Ubuntu 22.04+ or Debian 12+. Other systemd-based distributions may work
  but are untested.
- `systemd` with user services (`systemctl --user`)
- The `can` kernel module, i.e. SocketCAN, also `vcan` if you want a virtual
  bus for testing without hardware
- `sudo` / root access to install
- About 1 GB of free disk space

## Install

1. Download `edge-insights_<version>_amd64.deb`.

2. Install it with `apt`:

    ```bash
    sudo apt install ./edge-insights_<version>_amd64.deb
    ```

    Keep the leading `./`. It tells `apt` that this is a file, not a
    package name. `apt` also installs anything else the package needs.

The package installs Edge Insights for every user on the machine and adds
it to the application menu. Each user has their own separate data.

The portal is reachable only from the PC itself. Nothing starts at boot.
The application starts the services when you open it, and stops them when
you close it.

## Launch

Start **Edge Insights** from the application menu, like any other desktop
application.

The application starts the analytics services and opens the portal in its
own window. When you close the window, the services it started are stopped
again. Nothing keeps running in the background.

Continue with [First run](first-run.md).

## Upgrade

Download the newer `.deb` and install it the same way:

```bash
sudo apt install ./edge-insights_<version>_amd64.deb
```

An upgrade does not touch your data. Close Edge Insights before you
upgrade. If it is open, the services are stopped, and the new version is
used the next time you start it.

## Uninstall

To remove the program and keep your data:

```bash
sudo apt remove edge-insights
```

To remove your data as well, e.g. analytics database, uploaded DBC files,
imported logs:

```bash
sudo apt purge edge-insights
```

`purge` lists the data it found and asks before it deletes anything. If
you do not answer, the data is kept.

## Where your data is

Edge Insights is installed in `/opt/edge-insights` and shared by everyone
on the machine.

Everything it records is stored per user, in
`~/.local/share/edge-insights`. This includes the analytics database,
uploaded DBC files, and imported logs. Back up that directory to keep your
recordings.

An upgrade does not touch your data. An uninstall removes it only if you
ask for it.
