## 👋 Welcome to mqtt 🚀

Self-hosted mqtt application

## 📋 Description

This stack runs the eclipse-mosquitto MQTT broker, a lightweight open-source
message broker that accepts publish and subscribe connections from MQTT
clients and relays messages between them, optionally with persistence,
retained messages and TLS. The config, data and log directories are bind
mounted under the project tree.

Note that port 1883 is the plain MQTT TCP listener, not an HTTP endpoint. The
published address is useful for local MQTT clients and health checks; the
reverse proxy variants only make sense if the broker also has an HTTP or
WebSocket listener enabled in its config.

## 🚀 Services

- **app**: eclipse-mosquitto:latest

### Infrastructure Components

None, this is a single container stack.

## 📦 Installation

### Option 1: Quick Install
```bash
curl -q -LSsf "https://raw.githubusercontent.com/composemgr/mqtt/main/docker-compose.yaml" -o compose.yml
```

### Option 2: Git Clone
```bash
git clone "https://github.com/composemgr/mqtt" ~/.local/srv/docker/mqtt
cd ~/.local/srv/docker/mqtt
cp default.env .env
docker compose --env-file .env up -d
```

### Option 3: Using composemgr
```bash
composemgr install mqtt
```

## 🔧 Configuration

### Environment Variables

```shell
TZ=America/New_York
BASE_DOMAIN_NAME=example.com
BASE_HOST_NAME=mqtt.example.com
```

Mosquitto itself is configured through `mosquitto.conf` in the mounted config
directory, not through environment variables.

See `docker-compose.yaml` for complete list of configurable options.

### Environment Files

`default.env.sample` holds the full set of variables and is copied to `.env`
before starting the stack:

```bash
cp default.env .env
docker compose --env-file .env up -d
```

`app.env.sample` holds the app-specific overrides and is copied to `app.env`:

```bash
cp app.env.sample app.env
```

`composemgr up` applies `app.env` and `default.env` automatically. Raw
`docker compose` commands need `--env-file .env` to pick up the same values.

### Reverse Proxy

Three standalone compose files ship in this repo:

| File | Use |
|------|-----|
| `docker-compose.yaml` | Default, published on `1883` |
| `docker-compose.traefik.yaml` | Behind an existing external `traefik` network |
| `docker-compose.tunnel.yaml` | Behind an existing external `cloudflare` tunnel network |

```bash
docker compose -f docker-compose.traefik.yaml --env-file .env up -d
```

## 🌐 Access

- **TCP endpoint**: mqtt://172.17.0.1:1883

Port `1883` is the plain MQTT TCP listener, not an HTTP endpoint. MQTT clients
connect to `172.17.0.1:1883` on the default compose file.

As a protocol service, the broker publishes its native `1883` port on the
host; the assigned `64262` slot is not used.

## 📂 Volumes

- `./volumes/data/mqtt` - Persisted messages and the persistence database
- `./volumes/config/mqtt` - `mosquitto.conf` and any TLS material
- `./volumes/data/log/mqtt` - Broker log files

## 🔐 Security

- Put a `mosquitto.conf` in `./volumes/config/mqtt` and set
  `allow_anonymous false` plus a password file before exposing the broker
- Enable the WebSocket listener with TLS if clients connect over HTTPS
- The broker is published on the dockerhost bind IP only, it is not exposed on
  any other host interface
- Use the reverse proxy in production for TLS terminated access
- Keep the persistence directory on a backed-up volume
- Regularly update Docker images for security patches

## 🔍 Logging

```shell
docker compose --env-file .env logs -f app
```

## 🛠️ Management

```bash
# Start services
docker compose --env-file .env up -d

# Stop services
docker compose --env-file .env down

# Update to latest images
docker compose --env-file .env pull && docker compose --env-file .env up -d

# View logs
docker compose --env-file .env logs -f

# Restart services
docker compose --env-file .env restart
```

## 📋 Requirements

- Docker Engine 20.10+
- Docker Compose V2+

## 🤝 Author

🤖 casjay: [Github](https://github.com/casjay) 🤖  
🦄 composemgr: [Github](https://github.com/composemgr) 🦄
