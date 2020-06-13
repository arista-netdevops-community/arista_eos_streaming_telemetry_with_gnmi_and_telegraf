# Play with data

- [Play with data](#play-with-data)
  - [Verify Telegraf logs](#verify-telegraf-logs)
  - [InfluxDB query examples using CLI](#influxdb-query-examples-using-cli)
    - [Start an interactive session](#start-an-interactive-session)
    - [List databases](#list-databases)
    - [Select a database](#select-a-database)
  - [List measurements](#list-measurements)
    - [List series](#list-series)
    - [Count series](#count-series)
  - [Query ifcounters measurement](#query-ifcounters-measurement)
    - [Returns the list of keys](#returns-the-list-of-keys)
    - [Returns the list of values for a specified key](#returns-the-list-of-values-for-a-specified-key)
    - [Select fields](#select-fields)
  - [Query openconfig_bgp measurement](#query-openconfig_bgp-measurement)
    - [Returns the list of keys](#returns-the-list-of-keys-1)
    - [Returns the list of values for a specified key](#returns-the-list-of-values-for-a-specified-key-1)
    - [Select fields](#select-fields-1)
  - [Query eos_bgp measurement](#query-eos_bgp-measurement)
    - [Returns the list of keys](#returns-the-list-of-keys-2)
    - [Returns the list of values for a specified key](#returns-the-list-of-values-for-a-specified-key-2)
    - [Select fields](#select-fields-2)

## Verify Telegraf logs

```shell
docker logs telegraf

2020-06-09T23:46:34Z I! Starting Telegraf 1.14.3
2020-06-09T23:46:34Z I! Using config file: /etc/telegraf/telegraf.conf
2020-06-09T23:46:34Z I! Loaded inputs: cisco_telemetry_gnmi cisco_telemetry_gnmi
2020-06-09T23:46:34Z I! Loaded aggregators:
2020-06-09T23:46:34Z I! Loaded processors:
2020-06-09T23:46:34Z I! Loaded outputs: influxdb
2020-06-09T23:46:34Z I! Tags enabled: host=bfe273b6b299
2020-06-09T23:46:34Z I! [agent] Config: Interval:10s, Quiet:false, Hostname:"bfe273b6b299", Flush Interval:10s
```

## InfluxDB query examples using CLI

### Start an interactive session

```shell
docker exec -it influxdb bash

root@c3ead2edcf5a:/# influx
Connected to http://localhost:8086 version 1.8.0
InfluxDB shell version: 1.8.0
>
```

### List databases

```influx
> SHOW DATABASES
name: databases
name
----
arista
_internal
```

### Select a database

```influx
> USE arista
Using database arista
```

## List measurements

```
> SHOW MEASUREMENTS

name: measurements
name
----
eos_bgp
ifcounters
openconfig_bgp
>
```

### List series

- Get all entries part of `ifcounters` table

```influx
> SHOW SERIES FROM "ifcounters"

key
---
ifcounters,host=bfe273b6b299,name=Ethernet1,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet1,source=10.83.28.125
ifcounters,host=bfe273b6b299,name=Ethernet10,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet10,source=10.83.28.125
ifcounters,host=bfe273b6b299,name=Ethernet11,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet11,source=10.83.28.125
ifcounters,host=bfe273b6b299,name=Ethernet12,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet12,source=10.83.28.125
ifcounters,host=bfe273b6b299,name=Ethernet13,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet13,source=10.83.28.125
[ ... Output Truncated ... ]
```

- You can select only a subset of entries. Example below take only entries from `10.83.28.122`

```influx
> SHOW SERIES FROM "ifcounters" WHERE "source" = '10.83.28.122'

key
---
ifcounters,host=bfe273b6b299,name=Ethernet1,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet10,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet11,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet12,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet13,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet14,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet15,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet16,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet17,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet18,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet19,source=10.83.28.122
ifcounters,host=bfe273b6b299,name=Ethernet2,source=10.83.28.122
[ ... Output Truncated ... ]
```

### Count series

```shell
> SHOW SERIES EXACT CARDINALITY ON arista

name: eos_bgp
count
-----
24

name: ifcounters
count
-----
130

name: openconfig_bgp
count
-----
22
```

- Add conditions to request:

```influx
> SHOW SERIES EXACT CARDINALITY ON arista FROM "ifcounters" WHERE "source" = '10.83.28.122'

name: ifcounters
count
-----
65
>
```

## Query ifcounters measurement

### Returns the list of keys

```influx
> SHOW TAG KEYS FROM "ifcounters"

name: ifcounters
tagKey
------
host
name
source
```

### Returns the list of values for a specified key

```influx
> SHOW TAG VALUES FROM "ifcounters" with KEY = "name"

name: ifcounters
key  value
---  -----
name Ethernet1
name Ethernet10
name Ethernet11
name Ethernet12
name Ethernet13
name Ethernet14
name Ethernet15
[ ... Output Truncated ... ]
```

```influx
> SHOW TAG VALUES FROM "ifcounters" with KEY = "source"

name: ifcounters
key    value
---    -----
source 10.83.28.122
source 10.83.28.125
```

### Select fields

- Last 3 entries from `ifcounters` for host `10.83.28.122`

```influx
> SELECT * FROM "ifcounters" WHERE "source" = '10.83.28.122'  ORDER BY DESC LIMIT 3

name: ifcounters
time                host         in_broadcast_pkts in_discards in_errors in_multicast_pkts in_octets in_unicast_pkts name        out_broadcast_pkts out_discards out_errors out_multicast_pkts out_octets out_unicast_pkts source
----                ----         ----------------- ----------- --------- ----------------- --------- --------------- ----        ------------------ ------------ ---------- ------------------ ---------- ---------------- ------
1591747175792506596 bfe273b6b299                                                           286938640                 Management1                                                                                           10.83.28.122
1591747175792493464 bfe273b6b299 243616                                                                              Management1                                                                                           10.83.28.122
1591747175792483320 bfe273b6b299                                                                                     Management1                                                                          5768915          10.83.28.122
>
```

- Last 3 entries for IN and OUT octets from `ifcounters` for host `10.83.28.122`

```influx
> SELECT "in_octets","out_octets", "name" \
    FROM "ifcounters" WHERE "source" = '10.83.28.122' ORDER BY DESC LIMIT 3

name: ifcounters
time                in_octets out_octets name
----                --------- ---------- ----
1591747265864892858           6146169517 Management1
1591747265864883043 289180556            Management1
1591747257622763374           32223976   Ethernet4
>
```

- Last 3 entries from `ifcounters` for host `10.83.28.122` and `Ethernet24`

```influx
> SELECT "in_octets","out_octets" FROM "ifcounters" \
    WHERE ("source" = '10.83.28.122' AND "name"='Ethernet24') ORDER BY DESC LIMIT 3

name: ifcounters
time                in_octets out_octets
----                --------- ----------
1591747285684621082 362551
1591747285684577516           352034
1591747269675996035 362325
>
```

- Last minute for IN and OUT octets from `ifcounters` for host `10.83.28.122` and `Ethernet24`

```
> SELECT "in_octets","out_octets" FROM "ifcounters" \
    WHERE ("source" = '10.83.28.122' AND "name"='Ethernet24' AND time >= now() - 60s)

name: ifcounters
time                in_octets out_octets
----                --------- ----------
1591747315702875678           352260
1591747315702934394 362777
1591747329711620129           352349
1591747329711708507 362936
1591747345721372958 363162
1591747345721389797           352575
>
```

- Last minute for IN and OUT octets from `ifcounters` for host `10.83.28.122` and for all `Ethernet` interfaces

```
> SELECT "in_octets","out_octets","name" FROM "ifcounters" \
    WHERE ("source" = '10.83.28.122' AND "name" =~/Ethernet.*/ AND time >= now() - 60s)

name: ifcounters
time                in_octets out_octets name
----                --------- ---------- ----
1591747315673205906           32224513   Ethernet4
1591747315702875678           352260     Ethernet24
1591747315702934394 362777               Ethernet24
1591747329711620129           352349     Ethernet24
1591747329711708507 362936               Ethernet24
1591747343692188058 32267765             Ethernet4
1591747345693238453           32224737   Ethernet4
1591747345721372958 363162               Ethernet24
1591747345721389797           352575     Ethernet24
>
```

- Last minute for IN and OUT octets from `ifcounters` for host `10.83.28.122` and for `Ethernet4` and `Ethernet24`

```
> SELECT "in_octets","out_octets","name" FROM "ifcounters" \
    WHERE ("source" = '10.83.28.122' AND "name" =~/Ethernet(4|24)/ AND time >= now() - 60s)

name: ifcounters
time                in_octets out_octets name
----                --------- ---------- ----
1591788625820821765 32685381             Ethernet4
1591788625860898158           735633     Ethernet24
1591788625860925056 782620               Ethernet24
1591788627821727530           32594193   Ethernet4
1591788651837941988           32594282   Ethernet4
1591788651838002768 32685540             Ethernet4
1591788655839354826 32685764             Ethernet4
1591788655882529037 782846               Ethernet24
1591788655882554316           735859     Ethernet24
1591788657840907669           32594506   Ethernet4
1591788669890213294           736018     Ethernet24
1591788669890274993 783005               Ethernet24
```

- Last minute for IN and OUT octets from `ifcounters` for host `10.83.28.122` and for all `Ethernet` interfaces grouped by interface name

```
> SELECT "in_octets","out_octets" FROM "ifcounters" \
    WHERE ("source" = '10.83.28.122' AND "name" =~/Ethernet(4|24)/ AND time >= now() - 60s) GROUP BY "name"

name: ifcounters
tags: name=Ethernet24
time                in_octets out_octets
----                --------- ----------
1591790545292521967 802032
1591790545292549315           755185
1591790575312599502           755411
1591790575312625709 802258
1591790589325834427 802417
1591790589325846584           755570

name: ifcounters
tags: name=Ethernet4
time                in_octets out_octets
----                --------- ----------
1591790545247869333 32704805
1591790547249414877           32611377
1591790575273111579 32705188
1591790577274579004           32611690
>
```

- Average of last minute for IN and OUT octets from `ifcounters` for host `10.83.28.122` and for all `Ethernet` interfaces

```
> SELECT mean("in_octets") FROM "ifcounters" \
    WHERE ("source" = '10.83.28.122' AND "name" = 'Ethernet24' AND time >= now() - 60s)

name: ifcounters
time                mean
----                ----
1591747439566953700 364384
>
```

- Average in bits of last minute for IN and OUT octets from `ifcounters` for host `10.83.28.122` and for all `Ethernet` interfaces

```
> SELECT mean("in_octets")*8 FROM "ifcounters" \
    WHERE ("source" = '10.83.28.122' AND "name" = 'Ethernet24' AND time >= now() - 60s)

name: ifcounters
time                mean
----                ----
1591747477741147700 2918330.6666666665
>
```

- Average in bits of last minute for IN and OUT octets from `ifcounters` for host `10.83.28.122` and for `Ethernet24` interfaces grouped by minute

```
> SELECT derivative(mean("in_octets"), 1s) *8 FROM "ifcounters" \
    WHERE ("name" = 'Ethernet24') AND ("source" = '10.83.28.122') \
    AND (time >= now() - 10m)  GROUP BY time(1m)

name: ifcounters
time                derivative
----                ----------
1591746960000000000 81.46666666666667
1591747020000000000 81.46666666666667
1591747080000000000 81.46666666666667
1591747140000000000 81.46666666666667
1591747200000000000 81.46666666666667
1591747260000000000 81.46666666666667
1591747320000000000 81.46666666666667
1591747380000000000 81.46666666666667
1591747440000000000 81.46666666666667
1591747500000000000 81.46666666666667
>
```

```
> SELECT derivative(mean("in_unicast_pkts"), 1s) FROM "ifcounters" \
    WHERE ("source" = '10.83.28.122' AND "name" = 'Ethernet4') \
    AND (time >= now() - 10m)  GROUP BY time(1m)

name: ifcounters
time                derivative
----                ----------
1591746960000000000 0.03333333333333333
1591747020000000000 0.03333333333333333
1591747080000000000 0.03333333333333333
1591747140000000000 0.03333333333333333
1591747200000000000 0.03333333333333333
1591747260000000000 0.03333333333333333
1591747320000000000 0.03333333333333333
1591747380000000000 0.03333333333333333
1591747440000000000 0.03333333333333333
1591747500000000000 0.03333333333333333
>
```


## Query openconfig_bgp measurement

### Returns the list of keys

```
> SHOW TAG KEYS FROM "openconfig_bgp"
name: openconfig_bgp
tagKey
------
/network-instances/network-instance/protocols/protocol/name
afi_safi_name
host
identifier
name
neighbor_address
source
>
```


### Returns the list of values for a specified key

```
> SHOW TAG VALUES FROM "openconfig_bgp" WITH KEY = "source"
name: openconfig_bgp
key    value
---    -----
source 10.83.28.122
source 10.83.28.125
>
```


```
> SHOW TAG VALUES FROM "openconfig_bgp" WITH KEY = "neighbor_address" WHERE "source"='10.83.28.122'
name: openconfig_bgp
key              value
---              -----
neighbor_address 10.10.10.2
neighbor_address 10.10.10.4
>
```


### Select fields
```
> SELECT "neighbors/neighbor/state/neighbor_address" FROM "openconfig_bgp" WHERE source='10.83.28.122' ORDER BY DESC LIMIT 10
name: openconfig_bgp
time                neighbors/neighbor/state/neighbor_address
----                -----------------------------------------
1591729949067129716 10.10.10.4
1591729949066405709 10.10.10.2
>
```

```

> SELECT "neighbors/neighbor/state/neighbor_address" FROM "openconfig_bgp" GROUP BY "source" ORDER BY DESC LIMIT 10
name: openconfig_bgp
tags: source=10.83.28.125
time                neighbors/neighbor/state/neighbor_address
----                -----------------------------------------
1591741922312763875 10.10.10.1
1591741922312042982 10.10.10.3

name: openconfig_bgp
tags: source=10.83.28.122
time                neighbors/neighbor/state/neighbor_address
----                -----------------------------------------
1591729949067129716 10.10.10.4
1591729949066405709 10.10.10.2
>
```


```
> SELECT "neighbors/neighbor/state/session_state" FROM "openconfig_bgp" \
    WHERE ("source" = '10.83.28.122'  AND "neighbor_address" = '10.10.10.4') \
    ORDER BY DESC LIMIT 1
name: openconfig_bgp
time                neighbors/neighbor/state/session_state
----                --------------------------------------
1591729949082769237 ESTABLISHED
>
```


```
> SELECT LAST("neighbors/neighbor/state/session_state") FROM "openconfig_bgp" \
    WHERE (source='10.83.28.122' AND "neighbor_address" = '10.10.10.4')
name: openconfig_bgp
time                last
----                ----
1591729949082769237 ESTABLISHED
>
```

```
> SELECT "source",  "neighbor_address", LAST("neighbors/neighbor/state/session_state") \
    FROM "openconfig_bgp" \
    WHERE (source='10.83.28.122' AND "neighbor_address" = '10.10.10.4')
name: openconfig_bgp
time                source       neighbor_address last
----                ------       ---------------- ----
1591729949082769237 10.83.28.122 10.10.10.4       ESTABLISHED
>
```

```
> SELECT "source", "neighbors/neighbor/state/neighbor_address", \
    "neighbors/neighbor/config/peer_as", \
    "neighbors/neighbor/config/enabled" \
    FROM "openconfig_bgp" \
    WHERE (source='10.83.28.122' AND "neighbor_address" = '10.10.10.4') \
    ORDER BY DESC LIMIT 10

name: openconfig_bgp
time                source       neighbors/neighbor/state/neighbor_address neighbors/neighbor/config/peer_as neighbors/neighbor/config/enabled
----                ------       ----------------------------------------- --------------------------------- ---------------------------------
1591729949079717195 10.83.28.122                                           65002
1591729949067129716 10.83.28.122 10.10.10.4
1591729949066544119 10.83.28.122                                                                             true
>
```

```
> SELECT "source", "neighbors/neighbor/state/neighbor_address" AS "neighbor_address", \
    "neighbors/neighbor/config/peer_as" AS "peer-as", \
    "neighbors/neighbor/config/enabled" AS "peer_enabled" \
    FROM "openconfig_bgp" \
    GROUP BY "source", "neighbor_address" ORDER BY DESC LIMIT 10

name: openconfig_bgp
tags: neighbor_address=10.10.10.4, source=10.83.28.122
time                source       neighbor_address peer-as peer_enabled
----                ------       ---------------- ------- ------------
1591729949079717195 10.83.28.122                  65002
1591729949067129716 10.83.28.122 10.10.10.4
1591729949066544119 10.83.28.122                          true

name: openconfig_bgp
tags: neighbor_address=10.10.10.3, source=10.83.28.125
time                source       neighbor_address peer-as peer_enabled
----                ------       ---------------- ------- ------------
1591741922328551528 10.83.28.125                  65003
1591741922312042982 10.83.28.125 10.10.10.3
1591741922311446372 10.83.28.125                          true

name: openconfig_bgp
tags: neighbor_address=10.10.10.2, source=10.83.28.122
time                source       neighbor_address peer-as peer_enabled
----                ------       ---------------- ------- ------------
1591729949073383435 10.83.28.122                  65001
1591729949066405709 10.83.28.122 10.10.10.2
1591729949065837695 10.83.28.122                          true

name: openconfig_bgp
tags: neighbor_address=10.10.10.1, source=10.83.28.125
time                source       neighbor_address peer-as peer_enabled
----                ------       ---------------- ------- ------------
1591741922318698586 10.83.28.125                  65002
1591741922312763875 10.83.28.125 10.10.10.1
1591741922312187113 10.83.28.125                          true
>
```


## Query eos_bgp measurement

### Returns the list of keys


```
> SHOW TAG KEYS FROM "eos_bgp"
name: eos_bgp
tagKey
------
host
path
source
```


### Returns the list of values for a specified key

```
> SHOW TAG VALUES FROM "eos_bgp" WITH KEY = "source"
name: eos_bgp
key    value
---    -----
source 10.83.28.122
source 10.83.28.125
```

### Select fields

```
> SELECT "vrfBgpPeerInfoStatusEntryTable/default/bgpPeerInfoStatusEntry/10.10.10.4/bgpPeerEstablishedTime" \
    AS "bgpPeerEstablishedTime", \
    "vrfBgpPeerInfoStatusEntryTable/default/bgpPeerInfoStatusEntry/10.10.10.4/bgpPeerEstablishedTransitions" \
    AS "bgpPeerEstablishedTransitions" \
    FROM "eos_bgp" WHERE source='10.83.28.122' ORDER BY DESC LIMIT 3

name: eos_bgp
time                bgpPeerEstablishedTime bgpPeerEstablishedTransitions
----                ---------------------- -----------------------------
1591729945506543003 1591716173.220016      2
>
```
