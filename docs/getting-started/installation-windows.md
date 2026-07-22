# Installing on Windows

## Download

Download `edge-insights-<version>-windows-x86_64-setup.exe` from the
release.

## Install

Run the downloaded installer. Notes:

- The application is installed per-user, into
  `%LOCALAPPDATA%\CanEduDev\EdgeInsights`. No administrator rights are
  required for the Edge Insights installation itself.
- The installer checks for the Kvaser CAN driver and the Kvaser CANlib
  runtime. If either is missing, it installs them automatically.
  Installing the Kvaser driver requires administrator approval (a Windows
  UAC prompt). This is the only step that needs elevated permissions.
- If the Kvaser driver was installed, a reboot may be required before the
  CAN interface can be used. The installer prompts for a restart if
  needed.
- A desktop shortcut can be added during setup (optional).

## Launch

Start Edge Insights from the Start menu (or the desktop shortcut, if you
created one).

## Uninstall

Uninstall Edge Insights from **Settings → Apps → Apps & Features**, like
any other Windows application.
