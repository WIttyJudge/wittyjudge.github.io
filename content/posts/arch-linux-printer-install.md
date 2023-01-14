---
title: Arch Linux Printer Install with CUPS
date: 2023-01-14
cover:
  image: 'img/arch-linux-printer-install/cover.avif'
tags: ["Arch Linux", "Linux", "CUPS", "Printer"]
---

I have been using Arch Linux for about 3 years now and only recently I needed to print and scan documents with my office printer (Canon MF260).

Preconfigured Linux distros like Ubuntu, Mint, Manjaro, etc.. already provided you with some functionality to use your printer/scanner.
All you need to do is just log in to your favorite DE, search for "printer" in the application launcher, click on the "Add a printer" button and it will magically discover your Wi-Fi wireless or plugged-in printer, install all drivers for that specific printer and... it's ready for use.

In this guide, we will archive similar easy-to-use functionality with some pretty good [GTK](https://wiki.archlinux.org/title/GTK) applications.

## Installation

Setting up a printer on Arch Linux can be done using the [CUPS (Common UNIX Printing System)](https://openprinting.github.io/cups/) software.
Here are the general steps to set up a printer on Arch Linux:

Install CUPS:

```bash
$ sudo pacman -S cups
```

Then, start the CUPS service, and enable it automatically at boot in systemd:

```bash
$ sudo systemctl enable --now cups
```

Add your user to the `lp` group.
This will give your user permission to access the printer:

```
$ sudo usermod -aG lp $USER
```

Don't forget to connect your printer to your computer using a USB cable or via a network connection.

At this moment, we are ready to install the graphical program.
There are several programs available but I typically install simple and small GTK program called [system-config-printer](https://github.com/OpenPrinting/system-config-printer).

```bash
$ sudo pacman -S system-config-printer
```

So open up the program and you will see the "Add" button on the center of the screen.
Click on that button and it should detect the printer, select the model of your detected printer, and in the next step it should detect the drivers for that printer.

I had a `Canon MF260` printer and I didn't face any issues, but as I heard someone else did.
If you have any issues, maybe your printer or drivers for that printer wasn't detected, [Troubleshooting](#troubleshooting) may help you.

## Troubleshooting

If you have any problems, here are some arch wiki guides that may help you:

[CUPS/Printer-specific problems](https://wiki.archlinux.org/title/CUPS/Printer-specific_problems)

[CUPS/Troubleshooting](https://wiki.archlinux.org/title/CUPS/Troubleshooting)

## Useful links

**Guides:**

[CUPS Arch Wiki](https://wiki.archlinux.org/title/CUPS)

**Youtube:**

[How To Install Printers On Linux](https://www.youtube.com/watch?v=jnmCbEWNV1w)

[How to Install a Printer in Arch Linux!](https://www.youtube.com/watch?v=utK889gYAmM)

