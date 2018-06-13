
Debian
====================
This directory contains files used to package bpgcoind/bpgcoin-qt
for Debian-based Linux systems. If you compile bpgcoind/bpgcoin-qt yourself, there are some useful files here.

## bpgcoin: URI support ##


bpgcoin-qt.desktop  (Gnome / Open Desktop)
To install:

	sudo desktop-file-install bpgcoin-qt.desktop
	sudo update-desktop-database

If you build yourself, you will either need to modify the paths in
the .desktop file or copy or symlink your bpgcoin-qt binary to `/usr/bin`
and the `../../share/pixmaps/bpgcoin128.png` to `/usr/share/pixmaps`

bpgcoin-qt.protocol (KDE)

