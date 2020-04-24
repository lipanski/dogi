# Dogi

Dogi provides a simple way to deploy your pet projects using Docker, Git, Caddy and SSH.

Goals:

- Deploy your application with Git pushes (Heroku-style).
- Use Docker to define and contain your application dependencies.
- Expose a web server in the most simple way using Caddy.
- Manage SSL certificates automatically with Caddy and Let's Encrypt.
- Recover without manual intervention after an outage or a server restart.
- Handle all this with a simple, POSIX-compliant and easy to follow Shell script.

Non-goals:

- Handling more than one server at a time.
- Handling complex use cases.
- Anything anywhere close to Kubernetes or Docker Swarm.
- Using Docker to establish network isolation: Dogi uses `--net=host` so use a firewall instead.

## Example

*This is just an example of what's in the box. Please follow the Installation and Usage guides for more.*

Let's say you've built a web app to showcase your coin collection. It runs on port 3001 and you've added a Dockerfile. You'd like to see it running behind `https://coins.example.com` in order to show it to your numismat friends. Say no more - Dogi is here to help.

Assuming you can connect to your server by calling `ssh dogi@1.2.3.4`, you've pointed the domain `coins.example.com` to your server and you've installed Dogi, let's create the app:

```sh
ssh -t dogi@1.2.3.4 dogi create -n coins -d coins.example.com -p 3001
```

> Some Dogi commands are interactive so we need to call `ssh` with the `-t` (request TTY) option.

Next, let's configure the app with some environment variables:

```sh
ssh -t dogi@1.2.3.4 dogi env -n coins
```

Connect your app's local Git repository to the newly created remote repository:

```sh
git remote add dogi dogi@1.2.3.4:/home/dogi/apps/coins/git
```

...and trigger your first deployment by making a push to the remote repository:

```sh
git push dogi master
```

If the deployment was successful, your website should be available under `https://coins.example.com`.

If you'd like to trigger a deployment without making a Git push, you can call `ssh -t dogi@1.2.3.4 dogi deploy -n coins`.

Finally, if you'd like to remove the application entirely, run `ssh -t dogi@1.2.3.4 dogi remove -n coins`.

## Installation

Dogi has been tested on Ubuntu 18.04 but it should run fine on any recent Ubuntu or Debian version.

First, install Curl and Docker on your server. When installing Docker please refer to [the official installation guide](https://docs.docker.com/engine/install/ubuntu/) and avoid the default or Snap packages.

Next, install Dogi:

```sh
curl -s https://raw.githubusercontent.com/lipanski/dogi/master/dogi > /usr/local/bin/dogi
chmod +x /usr/local/bin/dogi
```

Create a dedicated user for running Dogi (recommended, but not required):

```sh
adduser --disabled-password --shell /bin/bash dogi
```

> All relevant files will be placed inside `$HOME/apps` so ensure the user has a home directory and it uses the right permissions.

Make sure the user running Dogi belongs to the `docker` group:

```sh
addgroup docker
usermod -aG docker dogi
``` 	

Set up your public SSH key under `$HOME/.ssh/authorized_keys` so that you can access the server. Assuming you can reach the server by calling `ssh dogi@1.2.3.4`, let's test the installation:

```sh
ssh -t dogi@1.2.3.4 dogi help
```

...which should print the Dogi help message.

## Usage

Run `dogi help` for a list of all available commands and options.

## Development

Run [shellcheck](https://github.com/koalaman/shellcheck) (0.7+):

```sh
shellcheck dogi
```
