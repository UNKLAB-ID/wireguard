# WireGuard Easy Setup with Docker Compose

This repository provides a simple setup for running [WireGuard Easy](https://github.com/wg-easy/wg-easy) using Docker Compose. WireGuard Easy is a web-based interface to manage WireGuard VPN.

## Requirements

- Docker and Docker Compose installed on your system.
- An existing Docker network named `unklab` for external communication.

## Configuration

### Environment Variables

Update the following environment variables as needed:

- `WG_HOST`: The public domain or IP address of your WireGuard server.
- `PASSWORD_HASH`: A [bcrypt hash](https://github.com/wg-easy/wg-easy/blob/master/How_to_generate_an_bcrypt_hash.md) for securing the WG-Easy admin UI.

### Network

Ensure the external network `unklab` exists before running the service:

```bash
docker network create unklab
```

## Usage 

1. Clone this repository

```bash
git clone https://github.com/yourusername/your-repo.git
cd your-repo
```

2. Create a `.env` file and define the following variables:

```bash
WG_HOST=your-public-ip-or-domain
```

3. Start the service

```bash
docker-compose up -d
```

## Docker Compose File

The `docker-compose.yml` file is configured as follows:
```bash
networks:
  unklab:
    external: true

volumes:
  etc_wireguard:

services:
  wg-easy:
    image: ghcr.io/wg-easy/wg-easy
    pull_policy: always
    container_name: wireguard
    restart: unless-stopped
    networks:
      - unklab
    environment:
      - LANG=en
      - WG_HOST=${WG_HOST}
      - PASSWORD_HASH=$$2a$$12$$JKwX2vVjfB4yugoFomJ27uceVHH/VK6yXo9z/F2U/Oy/gOEhw2OBi
      - WG_DEFAULT_ADDRESS=10.7.0.x
      - WG_DEFAULT_DNS=1.1.1.1
      - UI_TRAFFIC_STATS=true
      - UI_CHART_TYPE=1
    ports:
      - "51820:51820/udp"
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.ip_forward=1
      - net.ipv4.conf.all.src_valid_mark=1
```

## Notes

- Make sure the 51820 UDP port is open on your server's firewall.
- Adjust the `WG_DEFAULT_ADDRESS` and `WG_DEFAULT_DNS` if needed.

