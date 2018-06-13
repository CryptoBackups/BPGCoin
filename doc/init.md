Sample init scripts and service configuration for bpgcoind
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/bpgcoind.service:    systemd service unit configuration
    contrib/init/bpgcoind.openrc:     OpenRC compatible SysV style init script
    contrib/init/bpgcoind.openrcconf: OpenRC conf.d file
    contrib/init/bpgcoind.conf:       Upstart service configuration file
    contrib/init/bpgcoind.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three Linux startup configurations assume the existence of a "bpgcoin" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes bpgcoind will be set up for the current user.

2. Configuration
---------------------------------

At a bare minimum, bpgcoind requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, bpgcoind will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that bpgcoind and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If bpgcoind is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running bpgcoind without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/bpgcoin.conf`.

3. Paths
---------------------------------

3a) Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/bpgcoind`  
Configuration file:  `/etc/bpgcoin/bpgcoin.conf`  
Data directory:      `/var/lib/bpgcoind`  
PID file:            `/var/run/bpgcoind/bpgcoind.pid` (OpenRC and Upstart) or `/var/lib/bpgcoind/bpgcoind.pid` (systemd)  
Lock file:           `/var/lock/subsys/bpgcoind` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the bpgcoin user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
bpgcoin user and group.  Access to bpgcoin-cli and other bpgcoind rpc clients
can then be controlled by group membership.

3b) Mac OS X

Binary:              `/usr/local/bin/bpgcoind`  
Configuration file:  `~/Library/Application Support/BPGCoin/bpgcoin.conf`  
Data directory:      `~/Library/Application Support/BPGCoin`
Lock file:           `~/Library/Application Support/BPGCoin/.lock`

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start bpgcoind` and to enable for system startup run
`systemctl enable bpgcoind`

4b) OpenRC

Rename bpgcoind.openrc to bpgcoind and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/bpgcoind start` and configure it to run on startup with
`rc-update add bpgcoind`

4c) Upstart (for Debian/Ubuntu based distributions)

Drop bpgcoind.conf in /etc/init.  Test by running `service bpgcoind start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

4d) CentOS

Copy bpgcoind.init to /etc/init.d/bpgcoind. Test by running `service bpgcoind start`.

Using this script, you can adjust the path and flags to the bpgcoind program by
setting the BPGCOIND and FLAGS environment variables in the file
/etc/sysconfig/bpgcoind. You can also use the DAEMONOPTS environment variable here.

4e) Mac OS X

Copy org.bpgcoin.bpgcoind.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.bpgcoin.bpgcoind.plist`.

This Launch Agent will cause bpgcoind to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run bpgcoind as the current user.
You will need to modify org.bpgcoin.bpgcoind.plist if you intend to use it as a
Launch Daemon with a dedicated bpgcoin user.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
