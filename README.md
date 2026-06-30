<div align="center" width="100%">
    <img src="./public/icon.svg" width="128" alt="Uptime Kuma Logo" />
</div>

# Uptime Kuma - Subpath Edition

A fork of [Uptime Kuma](https://github.com/louislam/uptime-kuma) with built-in subpath support via `UPTIME_KUMA_BASE_PATH`.

## Features

All upstream Uptime Kuma features, plus:

- **Subpath deployment** — run behind a reverse proxy at `/uptime`, `/monitoring`, etc.
- `UPTIME_KUMA_BASE_PATH` environment variable for configuration

## Quick Start

### Docker Compose

```bash
mkdir uptime-kuma && cd uptime-kuma
curl -o compose.yaml https://raw.githubusercontent.com/wdlid/uptime-kuma-subpath/master/compose.yaml
docker compose up -d
```

Access at `http://localhost:3001`

### Docker Compose with Subpath

```yaml
services:
  uptime-kuma:
    image: wdlid/uptime-kuma-subpath:latest
    restart: unless-stopped
    volumes:
      - ./data:/app/data
    ports:
      - "3001:3001"
    environment:
      UPTIME_KUMA_BASE_PATH: /uptime
```

Access at `http://localhost:3001/uptime`

### Docker Run

```bash
docker run -d \
  --restart=always \
  -p 3001:3001 \
  -v uptime-kuma:/app/data \
  --name uptime-kuma \
  wdlid/uptime-kuma-subpath:latest
```

### Docker Run with Subpath

```bash
docker run -d \
  --restart=always \
  -p 3001:3001 \
  -v uptime-kuma:/app/data \
  -e UPTIME_KUMA_BASE_PATH=/uptime \
  --name uptime-kuma \
  wdlid/uptime-kuma-subpath:latest
```

## Reverse Proxy Example (Nginx)

When using a subpath, configure your reverse proxy to forward the path:

```nginx
location /uptime/ {
    proxy_pass http://127.0.0.1:3001/uptime/;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

## Updating

```bash
docker compose pull
docker compose up -d
```

## License

[MIT](LICENSE)
