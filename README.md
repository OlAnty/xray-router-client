
```yaml
 __  _____   ___   __  ___  ___  _   _ _____ ___ ___    ___ _    ___ ___ _  _ _____ 
 \ \/ | _ \ /_\ \ / / | _ \/ _ \| | | |_   _| __| _ \  / __| |  |_ _| __| \| |_   _|
  >  <|   // _ \ V /  |   | (_) | |_| | | | | _||   / | (__| |__ | || _|| .` | | |  
 /_/\_|_|_/_/ \_|_|   |_|_\\___/ \___/  |_| |___|_|_\  \___|____|___|___|_|\_| |_|  
                                                                                    
```                                                                     
# Xray VLESS Router Client (Entware/OpenWRT)
A one-stop shell script toolkit to install, configure, and manage a VLESS proxy client on routers running Entware (like OpenWRT).

You can choose what kind of internet traffic goes through your Xray VPN connection:
- **All internet traffic** from your router
- **Traffic to specific websites**, like `youtube.com`, `netflix.com`, etc. — all other sites will use your normal connection
- **Traffic from specific devices** on your network (like a Smart TV or gaming console), while other devices stay on the regular internet

## Requirements
- Router running **Entware** (e.g., OpenWRT)
- Comfigured **Xray VPN tunnel** supporting VLESS + Reality
- A valid **VLESS configuration** (server IP,UUID, PubKey, short ID, serverName)

> ⚠️ **Warning**  
> This is not an Xray VPN server configuration. This client is intended to connect to an existing remote Xray server. To configure Xray VPN server, visit https://github.com/XTLS/Xray-core

## Install
Choose a folder to download and unpack the scripts.
```sh
cd /tmp
```
Download and unpack.
```sh
curl -L https://github.com/OlAnty/Xray-router-client/archive/refs/heads/main.tar.gz | tar -xz
cd xray-router-client-main
```
Start the Xray admin.
```sh
sh xray-proxy-admin.sh
```
On first run, this will install itself as `xray-proxy-admin` globally.
Follow the menu to install the proxy client.

If it fails to install itself as `xray-proxy-admin`, run `install_all.sh`

```sh
sh install_all.sh
```

### Install xray proxy
Use option `1) Installation` from the menu to:
- Install required packages
- Prompt you to configure your VPN
- Generate all config and helper scripts
- Set up firewall redirect rules
- Start the proxy and watchdog
- Test the setup automatically

The script will auto-generate:
  - VLESS config
  - iptables routing script
  - watchdog and log management script
  - init.d client launcher

### Filesystem overview
- `/opt/sbin/xray` — Xray binary
- `/opt/etc/xray/vless.json` — configuration file
- `/opt/var/log/xray-access.log` — access log
- `/opt/var/log/xray-error.log` — error log
- `/opt/etc/init.d/S99xray-client` — manages Xray start/stop
- `/opt/etc/init.d/S99xray-routes` — sets up routing rules
- `/opt/etc/init.d/S99xray-watchdog` — watchdog to trim log files
- `/opt/bin/xray-proxy-admin` — global command to start CLI

### Finding related domains
When routing only specific domains, keep in mind that many services rely on multiple related domains for full functionality — such as video content, images, and APIs. Add all the domains to ensure full proxy support.
For example:
- **YouTube** may also use: `googlevideo.com`, `ytimg.com`, `youtubei.googleapis.com`, etc.  
- **Netflix** may also use: `nflxvideo.net`, `nflximg.net`, and others.

## Iptables behavior
The proxy works by creating a custom `XRAY_REDIRECT` chain and adding:

- `PREROUTING` rules for LAN traffic to direct it to `XRAY_REDIRECT` chain.
- `XRAY_REDIRECT` rules to redirect traffic to Xray dokodemo-door 1081 port. 
- `OUTPUT` rules for 443 and 1081 ports during the connectivity test.

## Uninstallation
Use menu option `1) Installation → 2) Uninstall Xray` to:

- Stop all proxy and watchdog processes
- Remove iptables rules
- Delete init.d scripts and config

## Disclaimer
> This tool modifies system-level iptables and adds startup scripts.
> Ensure you fully understand its effects before deploying on production routers.

Test on a secondary device or virtual instance if unsure.
The scripts are fully compatible and tested on Keenetic Giga (with opkg and BusyBox sh) and Debian 11.
