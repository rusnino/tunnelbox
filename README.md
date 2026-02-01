# tunnelbox

Unified Docker Compose stack for VPN services (WireGuard and AmneziaWG) with web UIs, DNS (Unbound + Pi-hole), and automatic HTTPS via Caddy.

## What It Does

- Runs `wg-easy` and `amnezia-wg-easy` with separate web interfaces.
- Provides DNS with Unbound + Pi-hole.
- Terminates TLS with Caddy and routes web UIs by domain.
- Uses a private Docker network for inter-container traffic and a public network for ingress.

## Prerequisites

- Linux host (Debian/Ubuntu recommended).
- Docker and Docker Compose installed.
- Public IPv4, open ports, and DNS records already created:
  - A/AAAA records for each web UI domain.
  - PTR records if required by your provider.
- Domains planned for web UIs (example):
  - `wg.example.com`
  - `awg.example.com`

## Installation

1) Clone the repository and enter it:

   - `git clone https://github.com/rusnino/tunnelbox.git`
   - `cd tunnelbox`

2) Create `.env` based on `.env.example`:

   - `cp .env.example .env`

3) Edit `.env` and fill in required values:

   Required:
   - `WG_HOST`
   - `WG_PORT`
   - `WG_USERNAME`
   - `WG_PASSWORD`
   - `AWG_HOST`
   - `AWG_PORT`
   - `AWG_PASSWORD_HASH` (see 
   - `PIHOLE_WEBPASSWORD`
     
> 💡 Replace `AWG_PASSWORD_HASH` with a bcrypt password hash to log in on the Web UI.
> See [How_to_generate_an_bcrypt_hash.md](https://github.com/w0rng/amnezia-wg-easy/blob/master/How_to_generate_an_bcrypt_hash.md) for know how generate the hash.

   Optional (recommended):
   - `WG_UI_PORT`
   - `AWG_UI_PORT`
   - `CADDY_EMAIL`
   - `AWG_KEEPALIVE`
   - `PIHOLE_TZ`
   - `PRIVATE_IPV4_SUBNET`, `PRIVATE_IPV6_SUBNET`
   - `PRIVATE_IPV4_*`, `PRIVATE_IPV6_*` (static addresses)

4) (Optional) Adjust `caddy/Caddyfile` if you want custom routing.
   The stack will create a default `caddy/Caddyfile` via `caddyfile-init`
   before Caddy starts.

## Usage

Start the stack:

- `docker compose up -d`

Check container status:

- `docker compose ps`

Access web UIs:

- `https://$WG_HOST` (wg-easy)
- `https://$AWG_HOST` (amnezia-wg-easy)

Stop the stack:

- `docker compose down`

## Notes

- WireGuard and AmneziaWG expose UDP ports defined by `WG_PORT` and `AWG_PORT`.
- Web UIs are proxied by Caddy on TCP 443.
- `public_network` is used for ingress, `private_network` for inter-container traffic.
- IPv6 addresses are pre-wired for future NAT66 work (see roadmap stages).
