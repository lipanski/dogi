# Dogi

A simple way to deploy your pet projects using Docker, Git and Caddy.

Let's say you've built a web app to showcase your coin collection. It runs on port 3001 and you've added a Dockerfile. You'd like to see it running behind https://coins.example.com in order to show it to your numismat friends.

Say no more -- Dogi is here to help.

Assuming you can connect to your server by calling `ssh dogi@1.2.3.4` and assuming you've installed Docker and Dogi on that server, you can create your application by calling:

```sh
ssh -t dogi@1.2.3.4 dogi create -n coins -d coins.example.com -p 3001
```

Edit your environment variables (if needed):

```sh
ssh -t dogi@1.2.3.4 dogi env -n coins
```

Once everything is in place, you'll want to connect the newly created remote repository to your app's local Git repository:

```sh
git remote add dogi dogi@1.2.3.4:/home/dogi/apps/coins/git
```

...and trigger your first deployment by making a simple push to the remote repository:

```sh
git push dogi master
```

Once the deployment is over, your website should be available under `https://coins.example.com`.

## About

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

