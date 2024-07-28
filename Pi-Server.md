# Reverse Proxy

Decided to use Caddy because it looks nice.

Learned setup with [Caddyfile Tutorial](https://caddyserver.com/docs/caddyfile-tutorial)

- If using a docker container it's important to expose any ports required for a multi-site setup. E.g. I want my actual server accessible at port 9000. Port 9000 should be exposed to the Caddy docker container.

# Actual server

Server setup for budgeting purposes.

Comes with instructions for using a reverse proxy: [Actual with Reverse Proxy](https://actualbudget.org/docs/config/reverse-proxies/).

# Pi-hole

- [Docker Setup](https://github.com/pi-hole/docker-pi-hole/#running-pi-hole-docker)
	- Custom environment variables:
		- `TZ: "Canada/Eastern"` for EST.
			- [Timezone Database](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)
		- `WEBPASSWORD` stored in password manager.
		- `INTERFACE: "wlan0"` because Pi was not connected to Ethernet. WiFi is the only option.
	- Set `network_mode: "host"` to allow for easy DHCP. But causes some inconveniences with Caddy routing later.
- [Caddy Setup Instructions](https://docs.pi-hole.net/guides/webserver/caddy/)
	- `/etc/lighttpd/external.conf` is old now. Can make a `/etc/lighttpd/conf-enabled/*.conf` file for configurations.
		- [Pi-hole upgrade notes](https://discourse.pi-hole.net/t/changing-default-listening-port-of-lighttpd-in-raspberry-pi-os-no-longer-honors-external-conf/62235)
		- [Pi-hole forum question: external.conf does not work](https://discourse.pi-hole.net/t/changing-default-listening-port-of-lighttpd-in-raspberry-pi-os-no-longer-honors-external-conf/62235)
	- In my case I just edited `/etc/lighttpd/lighttpd.conf` because I needed to override the admin page port (since Caddy is using port 80 and 443). But overriding in the `conf-enabled` directory doesn't work because the custom configs are loaded too late in `lighttpd.conf`.
		- [Pi-hole forum: can't bind to port 80](https://discourse.pi-hole.net/t/changing-default-listening-port-of-lighttpd-in-raspberry-pi-os-no-longer-honors-external-conf/62235) - look at the last response.
		- [Pi-hole forum question: port 80 on ipv6 despite custom config](https://discourse.pi-hole.net/t/lighttpd-refuses-to-let-go-of-port-80-on-ipv6-unless-i-edit-lighttpd-conf/69106/3) - same thing.
	- In Caddy, I had to set the `reverse_proxy` field to the IP of the Pi since Pi-hole was running in host network mode. `rpi.local` did not work.
		- [Pi-hole forum: can't access pi-hole with reverse proxy](https://discourse.pi-hole.net/t/can-t-access-pihole-externally-using-reverse-proxy-on-docker/65888/2)
	- I wanted the user to go to `rpi.local:9001` and get re-directed to `rpi.local:1080/admin` (Pi-hole dashboard) but that was not working. Instead I'm re-directing `rpi.local` to `rpi.local/admin` and using `reverse_proxy` to send to the Pi-hole dashboard on port 1080.
		- [Pi-hole forum: re-directing to pi-hole dashboard](https://www.reddit.com/r/pihole/comments/xf7059/pihole_behind_caddy_reverse_proxy_redirecting_to/)
	- I tried to create a volume for the `/etc` directory in Pi-hole but apparently this is a nono. Instead I had to target the exact file I wanted. So the volume is actually for `/etc/lighttpd/lighttpd.conf` where I am giving a custom configuration that uses port 1080 instead of port 80 for the dashboard.
		- [Pi-hole forum: binding /etc/lighttpd breaks Pi-hole](https://discourse.pi-hole.net/t/binding-volume-to-etc-lighttpd-breaks-pi-hole/58163)
