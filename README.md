# Dogi

Dogi provides a simple way to deploy your pet projects using Docker, Git, Caddy and SSH.

Goals:

- Deploy your application with Git pushes (Heroku-style).
- Use Docker (and only Docker, no build packs or Helm charts) to define and contain your application dependencies.
- Expose a web server in the most simple way using Caddy.
- Manage SSL certificates automatically with Caddy and Let's Encrypt.
- Recover from outages or server restarts without any manual intervention.
- Handle all this with a simple, POSIX-compliant and easy to follow Shell script.

Non-goals:

- Handling more than one server at a time.
- Handling complex use cases.
- Anything anywhere close to Kubernetes or Docker Swarm.
- Using Docker to establish network isolation (see the [Security](#Security) section below).

## Quick start

Let's say you've built a web app to showcase your awesome coin collection. It runs on port 3001 and you've added a Dockerfile. In order to show it to your numismatist friends, you'd like to see it running behind `https://coins.example.com`. Say no more - Dogi is here to help.

Assuming you've [installed Dogi](#Installation), you can connect to your server by calling `ssh dogi@1.2.3.4` and you've pointed your domain's DNS records to your server, let's **create** the app:

```sh
ssh -t dogi@1.2.3.4 dogi create -n coins -d coins.example.com -p 3001
```

> Some Dogi commands are interactive so we need to call `ssh` with the `-t` (request TTY) option.

Next, let's configure the app with some **environment variables**:

```sh
ssh -t dogi@1.2.3.4 dogi env -n coins
```

Connect your application's local Git repository to the newly created **remote Git repository**:

```sh
git remote add dogi dogi@1.2.3.4:/home/dogi/apps/coins/git
```

...and trigger your first **deployment** by making a push to the remote repository:

```sh
git push dogi master
```

If the deployment was successful, your website should be available under `https://coins.example.com`.

If you'd like to trigger a **deployment without making a Git push** (without code changes), you can call:

```sh
ssh -t dogi@1.2.3.4 dogi deploy -n coins
```

You can stream your application's **logs** by calling:

```sh
ssh -t dogi@1.2.3.4 dogi logs -n coins
```

Finally, if you'd like to **remove** the application entirely run:

```sh
ssh -t dogi@1.2.3.4 dogi remove -n coins
```

## Installation

Dogi is basically just a POSIX-compliant Shell script with Docker and curl as dependencies. It has been tested on Ubuntu 18.04 but it should run fine on any recent Ubuntu or Debian version.

Here's how to set it up on Ubuntu:

1. Install curl:

```sh
sudo apt install curl
```

2. Install Docker. Please refer to [the official installation guide](https://docs.docker.com/engine/install/ubuntu/) and avoid the default Ubuntu packages or Snap.

3. Install Dogi:

```sh
curl -s https://raw.githubusercontent.com/lipanski/dogi/master/dogi > /usr/local/bin/dogi
chmod +x /usr/local/bin/dogi
```

> If you prefer to use a specific version of Dogi, replace the branch name (`master`) in the URL above with a [tagged version](https://github.com/lipanski/dogi/releases).

4. (Optional, but recommended) Create a dedicated user for running Dogi:

```sh
adduser --disabled-password --shell /bin/bash dogi
```

> All relevant files will be placed inside `$HOME/apps` so ensure the user has a home directory and it uses the right permissions.

5. Make sure the user running Dogi belongs to the `docker` group:

```sh
addgroup docker || true
usermod -aG docker dogi
``` 	

6. Set up your public SSH key under `$HOME/.ssh/authorized_keys` so that you can access the server and allow pushes to the Dogi git repositories.

7. Assuming you can reach the server by calling `ssh dogi@1.2.3.4`, let's test the installation:

```sh
ssh -t dogi@1.2.3.4 dogi help
```

...which should print the Dogi help message.

8. (Optional) Simplify the way you access Dogi over SSH by adding a shortcut to your `~/.ssh/config`:

```
Host my-server
  HostName 1.2.3.4
  User dogi
  RequestTTY force
```

Now you can call any Dogi command like this:

```sh
ssh my-server dogi help
```

> If you intend to use `scp` over such a shortcut, remove the `RequestTTY force` line in favour of explicitly adding `-t` to every Dogi call.

## Usage

Run `dogi help` for a list of all available commands and options.

## Security

Dogi runs containers with `docker run --net=host`. This means all ports exposed by your containers will be available to all other containers and the host. If your apps bind to `0.0.0.0`, these ports will be exposed to the public as well. To prevent this, use a firewall - like `ufw` on Ubuntu or something like [AWS Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html) or [DigitalOcean Cloud Firewalls](https://www.digitalocean.com/docs/networking/firewalls/).

## Development

Run [shellcheck](https://github.com/koalaman/shellcheck) (0.7+):

```sh
shellcheck dogi
```
