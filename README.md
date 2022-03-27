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

## Docker Swarm e sicurezza.

### Crea le reti

Crea la rete `socket-proxy` che sarà utilizzata per le comunicazioni fra traefik e docker tramite il servizio 
`docker-proxy`.

Questa è una pratica di sicurezza. Per maggiori informazioni visita:
- [rockyourcode](https://www.rockyourcode.com/traefik-2-docker-swarm-setup-with-docker-socket-proxy-and-more/)
- [doc.traefik](https://doc.traefik.io/traefik/providers/docker/#docker-api-access)

```shell
docker network create --driver overlay --scope swarm --opt encrypted --attachable socket-proxy
```

Crea la rete `web` necessaria per permettere la comunicazione fra traefik e i containers che dovranno essere esposti 
nel web.

```shell
docker network create --driver overlay --scope swarm --opt encrypted --attachable web
```

### Extra configurazioni multi-node

Salta questo step se il deploy è single-node.

TODO valutare quando sarà necessario il deploy muti-node

Crea una label del nodo per essere sicuri che `Traefik` sarà deployato sullo stesso nodo che ha il volume per i 
certificati SSL.

```shell
export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}')
docker node update --label-add web.traefik-certificates=true $NODE_ID
```

Aggiungi anche la seguente label nel file docker-compose di traefik:

```shell
services:
  traefik:
    deploy:
      labels:
          # Make the traefik service run only on the node with this label
          # as the node with it has the volume for the certificates
          - node.labels.traefik.traefik-public-certificates: true
```

### Add Node Labels And Environment Variables


# Deploy

## 1) Docker compose

```shell
docker-compose up -d
```

## 2) Docker swarm

```shell
docker stack deploy -c docker-compose.yml <name-of-your-swarm>
```
