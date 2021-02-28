**Dogi** helps you deploy your pet projects using Docker, Git, Traefik and SSH.

Get the code at <https://github.com/lipanski/dogi>.

**Goals:**

- Keep things **simple**.
- **Deploy** your application **with Git pushes** (Heroku-style).
- Use **Docker** and **Dockerfiles** to define and contain your application dependencies.
- Expose and manage a **web server** in the most simple way using **Traefik**.
- **Manage SSL certificates automatically** with Traefik and Let's Encrypt.
- **Recover from outages** or server restarts without any manual intervention.
- Pass commands through **SSH** to manage your applications.
- Handle all this with a simple, POSIX-compliant and **easy to review Shell script**.

**Non-goals:**

- Handling more than one server at a time.
- Handling complex use cases.
- Anything anywhere close to Kubernetes, Docker Swarm, build packs or Helm charts.
- Using Docker to establish network isolation (all containers are part of the same internal network).

## Quick start

Let's say you've built a web app to showcase your awesome coin collection. You've added a Dockerfile which exposes a port via `EXPOSE`. You'd like to see it running at `https://coins.example.com`.

The following instructions assume that you've [installed Dogi](#Installation), you can connect to your server by calling `ssh dogi@1.2.3.4` and you've pointed your domain's DNS records to this machine.

Let's **create** the app:

```sh
ssh -t dogi@1.2.3.4 dogi create -n coins -d coins.example.com
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

If the deployment was successful and the domain's DNS records are pointing to your server, your website should be available under `https://coins.example.com`.

If you'd like to trigger a **deployment without making a Git push** (without triggering a code change), you can call:

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

Dogi is basically just a POSIX-compliant Shell script with Docker and git as dependencies. It has been tested on Ubuntu 18.04 but it should run fine on any recent Ubuntu or Debian version.

Here's how to set it up on Ubuntu:

1. Install git:

    ```sh
    sudo apt-get install git
    ```

2. Install Docker. Please refer to [the official installation guide](https://docs.docker.com/engine/install/ubuntu/) or try your luck with:

    ```sh
    sudo apt-get install docker.io
    ```

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

7. That's it! Assuming you can reach the server by calling `ssh dogi@1.2.3.4`, let's test the installation:

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

    > Bear in mind that `scp` doesn't play nicely with `RequestTTY force` so avoid using `scp` over such a shortcut or remove the setting in favour of explicitly adding `-t` to every Dogi SSH call.

## Usage

Run `dogi help` for a list of all available commands and options.

## Development

Run [shellcheck](https://github.com/koalaman/shellcheck) (0.7+):

```sh
shellcheck dogi
```
