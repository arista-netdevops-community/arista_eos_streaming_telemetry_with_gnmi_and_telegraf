# Docker-compose workflow

## Install Docker

```shell
docker -v
Docker version 19.03.8, build afacb8b
```

### Install Docker-compose

```shell
docker-compose -v
docker-compose version 1.25.5, build 8a1c60f6
```

## Pull the Docker images

This is optional as they will be pulled automatically if necessary.

```shell
# Get Telegraf
docker pull telegraf:1.14.3

# Get InfluxDB
docker pull influxdb:1.8.0

# Get Grafana
docker pull grafana/grafana:7.0.3
```

Then, you can list docker images

```shell
docker images

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
grafana/grafana     7.0.3               22fccd4fab0a        6 days ago          158MB
telegraf            1.14.3              6fdd3e021713        2 weeks ago         251MB
influxdb            1.8.0               1bf862b66ac1        3 weeks ago         304MB
```

## Create and start the containers

We can use this [docker-compose.yml](docker-compose.yml) file to create a TIG stack.

```shell
docker-compose -f docker-compose.yml up -d
```

And check your containers are UP and running

```shell
docker-compose ps

  Name             Command           State              Ports
-------------------------------------------------------------------------
grafana    /run.sh                   Up      0.0.0.0:3000->3000/tcp
influxdb   /entrypoint.sh influxd    Up      8083/tcp, 8086/tcp
telegraf   /entrypoint.sh telegraf   Up      8092/udp, 8094/tcp, 8125/udp
```
