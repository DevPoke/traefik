# Traefik

Utilizzato per seguenti funzionalità:
- reverse proxy
- https

# Quick start

```shell
# Clone the repository
git clone git@github.com:DevPoke/traefik.git
# Move to main directory
cd traefik
# Copy .en configuration and setup it
cp .env.example .env
# Generate a secure password file for the UI
htpasswd -cbB traefik-conf/.htpasswd-users <username> <password>
# Start container
docker-compose up -d
```
## Configurazione

Configurare le seguenti variabili d'ambiente

- TRAEFIK_HOST
- TRAEFIK_ENABLE_UI

Default:
```bash
TRAEFIK_HOST=monitor.pokestudio.it
TRAEFIK_ENABLE_UI=true
```
