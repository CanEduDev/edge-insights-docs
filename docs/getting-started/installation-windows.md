# Installing on Windows

## Download

Download `edge-insights-<version>-windows-x86_64-setup.exe` from the
release.

## Install

Run the downloaded installer. A few things to know:

- It installs per-user, into `%LOCALAPPDATA%\CanEduDev\EdgeInsights`. No
  administrator rights are required for the Edge Insights install itself.
- The installer checks for a Kvaser CAN driver and the Kvaser CANlib
  runtime. If either is missing, it installs them for you automatically
  (silently, in the background). Installing the Kvaser driver does require
  administrator approval (a Windows UAC prompt) — this is the only step in
  the install that needs elevated permissions.
- If the Kvaser driver was just installed, a reboot may be required before
  you can use your CAN interface for the first time. The installer will
  prompt you to restart if needed.
- You can optionally add a desktop shortcut during setup.

## Launch

Start Edge Insights from the Start menu (or the desktop shortcut, if you
created one).

## Uninstall

Uninstall Edge Insights from **Settings → Apps → Apps & Features**, like
any other Windows application.
