# Dogi

A simple way to deploy your pet projects using Docker, Git and Caddy.

Goals:

- Deploy your application with Git pushes (Heroku-style).
- Define and contain your application dependencies with Docker containers.
- Expose a web server in the most simple way with Caddy.
- Manage SSL certificates automatically with Caddy and Let's Encrypt.
- Automatic recovery after an outage or a server restart.

Non-goals:

- Handling more than one server at a time.
- Anything anywhere close to Kubernetes or Docker Swarm.
- Using containers to ensure network security: Dogi runs containers with `--net=host` so use a firewall if you want to restrict access to particular ports. 

## Installation

Dogi has been tested on Ubuntu 18.04 though other distros might work as well.

Dogi comes with following requirements:

- Docker
- Curl
- A user that can run Docker commands
- The Dogi script

## Usage

TODO: Write usage instructions here

## Development

TODO: Write development instructions here

