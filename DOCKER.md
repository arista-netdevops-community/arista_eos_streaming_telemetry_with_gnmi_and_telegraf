# Docker workflow

## Install Docker

```shell
docker -v
Docker version 19.03.8, build afacb8b
```

## Pull the Docker images

This is optionnal as they will be pulled automatically if necessary.

```shell
# Get Telegraf
docker pull telegraf:1.14.3

# Get InfluxDB
docker pull influxdb:1.8.0

# Get Grafana
docker pull grafana/grafana:7.0.3
```

## List Docker images

```shell
docker images

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
grafana/grafana     7.0.3               22fccd4fab0a        6 days ago          158MB
telegraf            1.14.3              6fdd3e021713        2 weeks ago         251MB
influxdb            1.8.0               1bf862b66ac1        3 weeks ago         304MB
```

## Create a Docker network

```shell
docker network create tig
c9793adb35c462d82040b56906dbad191549deee4e3b6ee1917bb1928a41d27e
```

## List Docker networks

```shell
docker network ls

NETWORK ID          NAME                DRIVER              SCOPE
e728b60ba316        bridge              bridge              local
fbc63b66d4f6        host                host                local
204dc11f764d        none                null                local
c9793adb35c4        tig                 bridge              local
```

## Create Docker containers

```shell
# Run InfluxDB container
docker run -d --name influxdb -p 8083:8083 -p 8086:8086 --network=tig influxdb:1.8.0

# Run Telegraf
docker run -d --name telegraf -v $PWD/telegraf.conf:/etc/telegraf/telegraf.conf:ro --network=tig telegraf:1.14.3

# Run Grafana
docker run -d --name grafana -p 3000:3000 --network=tig grafana/grafana:7.0.3
```

## List Docker running containers

```shell
docker ps

CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                                            NAMES
bfe273b6b299        telegraf:1.14.3         "/entrypoint.sh tele…"   12 seconds ago      Up 11 seconds       8092/udp, 8125/udp, 8094/tcp                     telegraf
c3ead2edcf5a        influxdb:1.8.0          "/entrypoint.sh infl…"   20 seconds ago      Up 18 seconds       0.0.0.0:8083->8083/tcp, 0.0.0.0:8086->8086/tcp   influxdb
c818fb9ce85f        grafana/grafana:7.0.3   "/run.sh"                4 hours ago         Up 4 hours          0.0.0.0:3000->3000/tcp                           grafana
```



## Display detailed information about the Docker network

```shell
docker network inspect tig

[
    {
        "Name": "tig",
        "Id": "8c56cd4a208c8f64556963f725af786dd3f3c14ac458db503c82574307d51c0b",
        "Created": "2020-06-01T20:31:43.5383398Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "bfe273b6b2992021b911f442acca4980ce19b46e6bc0865dcba354cf8fcf0121": {
                "Name": "telegraf",
                "EndpointID": "f6dfac8660c257b437a2b5b218ee67142961c7fc087f11b664c982052b4cee98",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            },
            "c3ead2edcf5a52303f66cf38fc9a24dd80f8b50e6819357bde4541798a825308": {
                "Name": "influxdb",
                "EndpointID": "6e5bf29fba94b2db65af929c2682317b6538292250ca04097697f8659f52ab40",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            },
            "c818fb9ce85fdb91fdbac91a2135b20d50bbba93ddc1fca453c85d81677f31f6": {
                "Name": "grafana",
                "EndpointID": "a471c77ee0afc1e953206f3eefe6996f1a23b82d930a3b36a99e1fe07b0d59b0",
                "MacAddress": "02:42:ac:12:00:04",
                "IPv4Address": "172.18.0.4/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```
