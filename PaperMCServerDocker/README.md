# Docker Minecraft Java PaperMC Server 1.19+ with Log Forwarding

Docker Minecraft PaperMC server for 1.19, 1.18, 1.17 that supports forwarding logs to a logging server (like rsyslog).

## Quick Start

```sh
docker build -t minecraft-papermc-server:latest .
docker run --rm --name mcserver -e MEMORYSIZE='1G' -v /home/evseyantonovich/mcserver:/data:rw -p 25565:25565 -i minecraft-papermc-server:latest
```

The server will generate all data including the world and config files in `/home/evseyantonovich/mcserver`. Change that to an existing folder.

## Operating the server after initalizing the container: Makefile with Docker Compose

A `Makefile` is provided to easily start, stop, and attach to the container.

```sh
make start     # equivalent to `docker-compose up -d --build`
make stop      # equivalent to `docker-compose stop --rmi all --remove-orphans`
make attach    # equivalent to `docker attach mcserver`
make help      # prints a help message
```


## How do I update the container?

- Re-download the image from this repository.
- Stop the container.
- Clear the container.
- Start the container.


### Skip permission change step

If you have a big custom minecraft install (e.g. multiple plugins which generate files), changing ownership can take up a
tremendous amount of time. You can skip this, by making sure that your files have the necessary permissions for the UID/GID
that you passed using the environment variables above and then add the following variable:

```sh
-e SKIP_PERM_CHECK=true
```

## Docker Compose

If you prefer to use `docker-compose` instead of the makefile, use the following commands:

Start the server:

```sh
docker-compose up
```

Stop the server:

```sh
docker-compose stop
```

Issue server commands after attaching to the container:

```sh
docker attach mcserver
# then you can type things like "list"
list
# which will show the current players online or
help
# to see all the commands available
```

## Environment variables

MEMORYSIZE = 1G

Not more than 70% of your RAM for your container. This is important. Because this is the RAM, your Minecraft Server will use within the container WITHOUT the operating system.

TZ = Europe/Moscow

Sets the timezone for the container. A list of valid values can be found on Wikipedia: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

PAPERMC_FLAGS = --nojline

Sets the command-line flags for PaperMC. Remove `--nojline` if you want to enable color and tab-completion for the server console.

LOGGING_HOST: "172.17.0.1"

Sets the IP of the logging server to send the logs to.

LOGGING_PORT: "514"

Sets the port of the logging server to send the logs to.

LOGGING_PROTOCOL: "UDP"

Sets the protocol to use to sends logs to the logging server.

## Credits

On GitHub https://github.com/evsey9/InnoUni_SNA_B21-SD-03_DockerizedPaperMC

Based on the work of [Marc Tönsing](https://github.com/mtoensing/Docker-Minecraft-PaperMC-Server) Thanks for your help!
