# Traefik

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

## Configuration

Configure the following environment variables:

```bash
TRAEFIK_HOST=monitor.pokestudio.it
TRAEFIK_ENABLE_UI=true
```

## Docker Swarm and security.

### Create networks

Create the `socket-proxy` network which will be used for communications between traefik and docker via the` docker-proxy` service.

This is a security practice. For more information visit:
- [rockyourcode](https://www.rockyourcode.com/traefik-2-docker-swarm-setup-with-docker-socket-proxy-and-more/)
- [doc.traefik](https://doc.traefik.io/traefik/providers/docker/#docker-api-access)

```shell
docker network create --driver overlay --scope swarm --opt encrypted --attachable socket-proxy
```

Create the `web` network necessary to allow communication between traefik and the containers to be exposed on the web.

```shell
docker network create --driver overlay --scope swarm --opt encrypted --attachable web
```

### Multi-node configuration

TODO: TestMe

Create a node label to ensure that `Traefik` is deployed on the same node that has the volume for SSL certificates.

```shell
export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}')
docker node update --label-add web.traefik-certificates=true $NODE_ID
```

Also add the following label in the docker-compose file of traefik:

```shell
services:
  traefik:
    deploy:
      labels:
          # Make the traefik service run only on the node with this label
          # as the node with it has the volume for the certificates
          - node.labels.traefik.traefik-public-certificates: true
```

# Deploy

## 1) Docker compose

```shell
docker-compose up -d
```

## 2) Docker swarm

Create aliases docker-stack to maintain compatibility with docker-compose.

```shell
alias docker-stack='env $(cat .env | grep ^[A-Z] | xargs) docker stack'
alias docker-stack-deploy='docker-stack deploy --compose-file docker-compose.yml ${PWD##*/}'
```

```shell
docker-stack-deploy
```
