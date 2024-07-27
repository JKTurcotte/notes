# Reverse Proxy

Decided to use Caddy because it looks nice.

Learned setup with [Caddyfile Tutorial](https://caddyserver.com/docs/caddyfile-tutorial)

- If using a docker container it's important to expose any ports required for a multi-site setup. E.g. I want my actual server accessible at port 9000. Port 9000 should be exposed to the Caddy docker container.

# Actual server

Server setup for budgeting purposes.

Comes with instructions for using a reverse proxy: [Actual with Reverse Proxy](https://actualbudget.org/docs/config/reverse-proxies/).