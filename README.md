
# Reverse Proxy with Nginx

## Overview

This project configures Nginx as a reverse proxy for two applications.

- app1.company.com → 192.168.100.50:3000
- app2.company.com → 192.168.100.20:6000

The configuration is managed using Docker Compose.

---
## Project Structure

```text
.
├── docker-compose.yml
└── nginx
    ├── nginx.conf
    └── conf.d
        ├── app1.conf
        └── app2.conf
```

---

## Requirements

- Docker
- Docker Compose

---

## Run

Start the service:

```bash
docker compose up -d
```

Verify the Nginx configuration:

```bash
docker compose exec nginx nginx -t
```

View logs:

```bash
docker compose logs -f nginx
```

Stop the service:

```bash
docker compose down
```

---

## Adding a New Application

To add a new application behind the reverse proxy:

1. Create a new configuration file under `nginx/conf.d/`.

Example:

```text
nginx/
└── conf.d/
    ├── app1.conf
    ├── app2.conf
    └── app3.conf
```

2. Add a new server block:

```nginx
server {
    listen 80;
    server_name app3.company.com;

    location / {
        proxy_pass http://desired IP:Port;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
---
> No changes to nginx.conf are required. Nginx automatically loads all configuration files from the conf.d directory.

3. Validate the configuration:

```bash
docker compose exec nginx nginx -t
```

4. Reload Nginx:

```bash
docker compose exec nginx nginx -s reload
```

## Notes

- Nginx listens on port 80
- Requests are routed based on the Host header (`server_name`)
- Each application is defined in its own configuration file under `conf.d`
