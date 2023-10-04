# subdomains

This tool publishes custom `.local` addresses by searching for Docker containers with Traefik host labels.

Run as a Docker container, it executes a NodeJS script at startup that lists all Docker containers, guesses what your local IPv4 address is, looks for Traefik HTTP router rules containing ``Host(`...`)``, and runs an instance of `avahi-publish-address` for each host pointing at the local IP.

__WARNING!__ This tool is very new and untested! You may need to dig into Node, Avahi, Traefik, and/or Docker if something doesn't work ;)

## Requirements

 - A Linux server, running:
   - Docker
     - [Ubuntu engine instructions](https://docs.docker.com/engine/install/ubuntu/)
   - Traefik & other containers in Docker Compose
     - They must use [Traefik's Docker Compose router `Host()` labels](https://doc.traefik.io/traefik/user-guides/docker-compose/basic-example/)
     - Example: ``"traefik.http.routers.my_container.rule=Host(`my_container.local`)"``
   - Avahi
     - Often installed & running by default, but if you have a minimized server, run:
     - ```
       sudo apt update && sudo apt install avahi-daemon && sudo service dbus start && sudo avahi-daemon -D
       ```
 - A non-Linux client
    - I haven't gotten this working on Ubuntu for some reason... Tested on MacOS, iOS, & ChromeOS. ([GitHub issue](https://github.com/ducklol2/subdomains/issues/1))

## Instructions

### Step 0: Clone repo

From your favorite directory to keep code:

```
git clone git@github.com:ducklol2/subdomains.git
cd subdomains
```

### Step 1: Start your Traefik & other containers

Or try my included example:

```
sudo docker compose -f traefik_example.yaml up -d
```

## Step 2: Start subdomains container

```
sudo docker compose up --build -d
```

You'll need to rerun this if you modify your Traefik `Host()` labels; it currently only fetches the list of containers at startup (TODO: make it loop, occasionally?).
