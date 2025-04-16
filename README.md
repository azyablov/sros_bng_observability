# SROS BNG Observability Lab

# Introduction

## Overview
This repository contains clab that implements BNG test-bed and collects telemetry, events and state data from a BNG (Broadband Network Gateway), aggregation and core network elements via SROS MDM interfaces and syslog channels. 
The our immediate objectives are to show how BNG telemetry data can be collected and visualized using open-source tools such as Prometheus, Grafana, Fluent Bit, Loki, gNMIc, Smokeping and Alertmanager. The lab also includes a BNG Blaster, which is a traffic generator that can be used to generate traffic on the BNG devices. The lab is designed to be easy to use and can be deployed using Containerlab.
The long-term goal is to create a comprehensive observability concept solution for BNG devices that can be used in lab environments and test-beds.
| <img src="pic/BNG_State_and_Redundancy.png" height="220" width="450"/> | <img src="pic/Subscriber_Inside.png" height="220" width="450"/> |
|----------------------------------------|----------------------------------------|
| <img src="pic/Core_and_Aggregation_Network.png" height="200" width="450"/> | <img src="pic/BNG_Resources.png" height="200" width="450"/> |

Let's move step by step via the principle and pillars, since every endeavor should start with a plan and vision behind.

## Authors

[Kivanc Imer](mailto:kivanc.imer@nokia.com) - IP Solution Architect

[Anton Zyablov](mailto:anton.zyablov@nokia.com) - Solution Architects Lead


## Softare Versions

| Software    | Version  |
|-------------|----------|
| Nokia SROS  | 24.7.R1  |
| gnmic       | 0.39.1   |
| bngblaster  | 0.9.17   |
| fluent-bit  | 3.2.10   |
| prometheus  | 2.53.4  |
| grafana     | 11.5.2   |
| freeradius-server | 3.2.3 |
| syslog-ng | 4.8.1-r1-ls153 |
| promtail |  3.4.3 |
| loki | 3.4.2 |
| alertmanager | 0.28.1 |
| smokeping_prober | 0.9.0 |


## Requirements

In order to run this clab, you need to have the following installed:
- [Docker](https://docs.docker.com/get-docker/)
- [Containerlab](https://containerlab.dev/getting-started/installation/)

Additional licenses and API tokens:
- SROS images (vr-sros should obtained from official Nokia representatives).
- SROS license is required to run the SROS containers (should obtained from official Nokia representatives).
- Telegram API token is required to send messages to a Telegram channel [Telegram Bots Tutorial](https://core.telegram.org/bots/tutorial).

# What is Observability?

## Observability vs Monitoring

Not the same as monitoring (practically collection and visualisation), so the aim of this setup is to provide observability of the BNG devices and the network elements connected to it. Observability is a measure of how well internal states of a system can be inferred from knowledge of its external outputs. In other words, observability is the ability to understand what is happening inside a system based on the data that is available from outside the system. Collection of everything creates a mess and makes observation and troubleshooting hard.

Our magic formula would be the following:

**Observability** = **real-time onitoring of relevant metrics and states** + **context-rich dashboards** + **proactive view**. 

## What Should I Collect?

The observability stack should be able to collect the following data:
- Metrics or Telemetry: performance, resource usage, and other operational metrics.
- State Data: very important to create a rich-context, like what's subscriber IP@ now, redundancy state, etc.
- Logs: NE logs to trace the events and errors.
- Synthetic monitoring: execute OAM to measure performance of relevant services.

## Why do we need it?

- Faster Root Cause Analysis: quick identification and resolution performance issues.
- Higher Productivity: automation of collection and analysis tasks for network operations teams.
- Improved User Experience: catch and fix problems before it affects users.
- Better Capacity Planning: understanding of network usage patterns and trends.
- Enhanced Security: detection of anomalies and potential security threats.

While many of them are quite obvious, implementation may be not that easy.

## The Three Pillars of Observability 

### Logs

Records of events (often human-readable text) with timestamps.
They are easy to generate and read. Provide granular details of events or transactions.
Enable retrospective replay of incidents and can reveal errors/exceptions.

***BNG use-case applicability:*** Practically it's our syslog messages, which are collected from the NE devices and sent to the syslog server. 


### Metrics and State

Numerical measurements (e.g., CPU/memory usage, response times) that track system health over time. They are usually aggregated and stored in time-series databases.
Great for detecting trends and setting alert thresholds.
Offer a high-level view of performance (e.g., error rates, latency).

***BNG use-case applicability:*** In case of network streaming telemetry we should consider sample and on-change depends on a semantic of the metric. Apart from that the state data is also important to collect, since it provides a context for the metrics. For example, if you have a saturation for the particular subscriber, you may want to know what was an IP address or the state of the BNG or link state.

### Traces

End-to-end records of a single request’s journey through a distributed system.
Ideal for pinpointing bottlenecks in complex, microservices-based workflows.
Provide context-specific details (e.g., which user made a request).
Help debug distributed systems by correlating requests across different services.


***BNG use-case applicability:*** Has this something to do with BNG? At the first glance no, but if we look deeper and start considering the whole network and distributed system, which is the case, then things like traffic path, apprearance of subcriber on the different NEs with relevant state and metrics data could be considered as a trace.

# Alerting Pillars of BNG

What are the best practices to alert?
Well, let's not reinvent a wheel again, since we have quite nice guidelines and best practices from  Niall Richard Murphy, Betsy Beyer, Chris Jones, Jennifer Petoff [Site Reliability Engineering](https://learning.oreilly.com/library/view/site-reliability-engineering/9781491929117/).
Let's summarise here some key technical and operational aspects of the alerting process:

1. Alert on the data (state and metrics) from BNG devices, CORE and Aggregation layer routers connected to Fixed Access NEs:
   - High CPU usage and memory usage.
   - High latency: reach out an NE.
   - Links saturation.
   - High packet loss: synthetics tests, OAM.
   - High number of error / dropped packets on interfaces.
   - High number of errors in logs.
   - Unexpected grow or drop in number sessions.
   - Approaching capacity / scalability limits.
   - Multi-chassis state and loss or redundanc between OLT and Aggregation routers.
   - Unexpected changes in number of routes.
   - Fluctuations on upstream BGP peering.
   - General criteria applicable to moderm IP/MPLS networks.
2. Alerts should be real: it should not be a false positive or a false negative.
3. Alerts should be urgent: it should be sent to the right people at the right time.
4. Alerts should be actionable: it should provide enough information to help the operator understand the issue and take appropriate action.
5. Alerts should be clear: it should be easy to understand and interpret.

# BNG Observability (HOWTO)

## What and Why to collect?

TODO Kivanc: Describe xpath, what and why to collect, how to alert on it.

## How to collect?

TODO Anton

## What's the next? (TODO List)

While we are tyring to release the first version of the lab, a number of things are in our TODO list:
- Alerts on: scalability limits, CPU, memory, latency, packet loss, errors in logs, errors on interfaces, errors on BNG sessions.
- Alerts on: unexpected changes in number of routes, unexpected grow or drop in number sessions, high number of errors in logs.
- Alerts on: high number of errors in logs, unexpected changes in number of routes, unexpected grow or drop in number sessions.
- OAM dashboards and alerts.
- Subscriber traceability.

# BNG Setup

Topology below should repesent the BNG setup and it's key functional elements w/o Telemetry Stack since it's all considered to be part of the observability stack.

![bng_topology](pic/bngt.clab.png)

The following nodes are the key functional and observability elements of the BNG setup:
- 2 SROS BNG devices (bng1 and bng2)
- 1 Aggregation device: client side (agg)
- 1 Core device: for upsteam peering (HSI service for example)
- 1 BNG Blaster: traffic generator which allows us to spawn 10 PPPoE Dual-Stack IPiv4/IPv6 sessions
- 1 Syslog server: aggregates logs and feed it toward the Promtail
- 1 Loki server: accumulate log and state data
- 1 Promtail server: takes logs from the syslog server and sends them to Loki
- 1 Prometheus server: where all metrics are living
- 1 Grafana server: our visualisation
- 1 Alertmanager server: where you can play with prometheus alerts, if you have Telegram API token, you can send alerts to a Telegram channel
- 1 Fluent Bit server: collects state from gnmic and sends them to Loki
- 1 Smokeping server: collects latency data and sends them to Prometheus
- 1 gNMIc server: collects telemetry data from the BNG devices and sends them to Prometheus
- 1 gNMIc server: collects state data from the BNG devices and sends them to Fluent Bit
- 1 FreeRadius server: our lovely AAA "classic"

TODO Kivanc: Describe functional setup: AAA, agg, core, bng, bngbalster, services, bng functions , bng design readout/


# Getting Started

In order to bring up the lab execute `sudo clab deploy`.

```bash
❯ sudo clab deploy
15:23:47 INFO Containerlab started version=0.67.0
15:23:47 INFO Parsing & checking topology file=bngt.clab.yaml
15:23:47 INFO Pulling quay.io/prometheus/prometheus:v2.53.4 Docker image
15:23:52 INFO Done pulling quay.io/prometheus/prometheus:v2.53.4
15:23:52 INFO Pulling docker.io/grafana/promtail:3.4.3 Docker image
15:23:53 INFO Done pulling docker.io/grafana/promtail:3.4.3
15:23:53 INFO Pulling quay.io/prometheus/alertmanager:v0.28.1 Docker image
15:23:54 INFO Done pulling quay.io/prometheus/alertmanager:v0.28.1
15:23:54 INFO Pulling quay.io/superq/smokeping-prober:v0.9.0 Docker image
15:23:56 INFO Done pulling quay.io/superq/smokeping-prober:v0.9.0
15:23:56 INFO Creating lab directory path=/home/azyablov/clab/sros_bng_observability/clab-sros_bngt
15:23:56 INFO unable to adjust Labdir file ACLs: operation not supported
15:23:56 INFO node "tel-fb" is being delayed for 20 seconds
15:23:56 INFO node "gnmic-metrics" is being delayed for 40 seconds
15:23:56 INFO node "tel-am" is being delayed for 30 seconds
15:23:56 INFO node "tel-sping" is being delayed for 20 seconds
15:23:56 INFO node "tel-syslog" is being delayed for 10 seconds
15:23:56 INFO node "radius" is being delayed for 10 seconds
15:23:56 INFO node "tel-prom" is being delayed for 40 seconds
15:23:56 INFO node "tel-loki" is being delayed for 20 seconds
15:23:56 INFO node "tel-pt" is being delayed for 20 seconds
15:23:56 INFO node "tel-graf" is being delayed for 60 seconds
15:23:56 INFO node "gnmic-state" is being delayed for 30 seconds
15:23:56 INFO Using existing config file (/home/azyablov/clab/sros_bng_observability/clab-sros_bngt/bng1/tftpboot/config.txt) instead of applying a new one
15:23:56 INFO Using existing config file (/home/azyablov/clab/sros_bng_observability/clab-sros_bngt/bng2/tftpboot/config.txt) instead of applying a new one
15:23:56 INFO Using existing config file (/home/azyablov/clab/sros_bng_observability/clab-sros_bngt/agg/tftpboot/config.txt) instead of applying a new one
15:23:56 INFO Using existing config file (/home/azyablov/clab/sros_bng_observability/clab-sros_bngt/core/tftpboot/config.txt) instead of applying a new one
15:23:56 INFO Creating container name=core
15:23:56 INFO Creating container name=bng1
15:23:56 INFO Creating container name=bng2
15:23:56 INFO Creating container name=agg
15:23:56 INFO Adding public keys configuration node=clab-sros_bngt-bng2
15:23:56 INFO Created link: bng2:eth2 (1/1/2) ▪┄┄▪ core:eth2 (1/1/2)
15:23:56 INFO Waiting for node to be ready. This may take a while node=clab-sros_bngt-bng2 log="docker logs -f clab-sros_bngt-bng2"
15:23:57 INFO Created link: bng1:eth1 (1/1/1) ▪┄┄▪ agg:eth2 (1/1/2)
15:23:57 INFO Created link: bng1:eth2 (1/1/2) ▪┄┄▪ core:eth1 (1/1/1)
15:23:57 INFO Adding public keys configuration node=clab-sros_bngt-core
15:23:57 INFO Waiting for node to be ready. This may take a while node=clab-sros_bngt-core log="docker logs -f clab-sros_bngt-core"
15:23:57 INFO Created link: bng1:eth3 (1/1/3) ▪┄┄▪ bng2:eth3 (1/1/3)
15:23:57 INFO Created link: bng2:eth1 (1/1/1) ▪┄┄▪ agg:eth3 (1/1/3)
15:23:57 INFO Adding public keys configuration node=clab-sros_bngt-agg
15:23:57 INFO Adding public keys configuration node=clab-sros_bngt-bng1
15:23:57 INFO Waiting for node to be ready. This may take a while node=clab-sros_bngt-agg log="docker logs -f clab-sros_bngt-agg"
15:23:57 INFO Waiting for node to be ready. This may take a while node=clab-sros_bngt-bng1 log="docker logs -f clab-sros_bngt-bng1"
15:24:06 INFO Creating container name=radius
15:24:06 INFO Creating container name=tel-syslog
15:24:06 INFO Created link: radius:eth1 ▪┄┄▪ core:eth4 (1/1/4)
15:24:16 INFO Creating container name=tel-sping
15:24:16 INFO Creating container name=tel-fb
15:24:16 INFO Creating container name=tel-pt
15:24:16 INFO Creating container name=tel-loki
15:24:26 INFO Creating container name=tel-am
15:24:26 INFO Creating container name=gnmic-state
15:24:36 INFO Creating container name=gnmic-metrics
15:24:36 INFO Creating container name=tel-prom
15:24:56 INFO Creating container name=tel-graf
15:24:56 INFO node "bngblaster" is being delayed for 10 seconds
15:25:06 INFO Creating container name=bngblaster
15:25:06 INFO Created link: bngblaster:eth1 ▪┄┄▪ agg:eth1 (1/1/1)
15:25:06 INFO Created link: bngblaster:eth2 ▪┄┄▪ core:eth3 (1/1/3)
15:25:08 INFO Adding host entries path=/etc/hosts
15:25:08 INFO Adding SSH config for nodes path=/etc/ssh/ssh_config.d/clab-sros_bngt.conf
╭──────────────────────────────┬───────────────────────────────────────────────────────────────────┬─────────┬────────────────╮
│             Name             │                             Kind/Image                            │  State  │ IPv4/6 Address │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-agg           │ nokia_sros                                                        │ running │ 172.20.20.21   │
│                              │ registry.srlinux.dev/pub/vr-sros:24.7.R1                          │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-bng1          │ nokia_sros                                                        │ running │ 172.20.20.11   │
│                              │ registry.srlinux.dev/pub/vr-sros:24.7.R1                          │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-bng2          │ nokia_sros                                                        │ running │ 172.20.20.12   │
│                              │ registry.srlinux.dev/pub/vr-sros:24.7.R1                          │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-bngblaster    │ linux                                                             │ running │ 172.20.20.2    │
│                              │ azyablov/bng-blaster:latest                                       │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-core          │ nokia_sros                                                        │ running │ 172.20.20.22   │
│                              │ registry.srlinux.dev/pub/vr-sros:24.7.R1                          │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-gnmic-metrics │ linux                                                             │ running │ 172.20.20.42   │
│                              │ harbor.nice.nokia.net/ghcr/openconfig/gnmic:0.39.1                │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-gnmic-state   │ linux                                                             │ running │ 172.20.20.41   │
│                              │ harbor.nice.nokia.net/ghcr/openconfig/gnmic:0.39.1                │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-radius        │ linux                                                             │ running │ 172.20.20.111  │
│                              │ harbor.nice.nokia.net/docker/freeradius/freeradius-server:3.2.3   │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-tel-am        │ linux                                                             │ running │ 172.20.20.35   │
│                              │ quay.io/prometheus/alertmanager:v0.28.1                           │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-tel-fb        │ linux                                                             │ running │ 172.20.20.33   │
│                              │ cr.fluentbit.io/fluent/fluent-bit:3.2.10                          │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-tel-graf      │ linux                                                             │ running │ 172.20.20.38   │
│                              │ grafana/grafana:11.5.2                                            │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-tel-loki      │ linux                                                             │ running │ 172.20.20.34   │
│                              │ grafana/loki:3.4.2                                                │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-tel-prom      │ linux                                                             │ running │ 172.20.20.37   │
│                              │ quay.io/prometheus/prometheus:v2.53.4                             │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-tel-pt        │ linux                                                             │ running │ 172.20.20.32   │
│                              │ grafana/promtail:3.4.3                                            │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-tel-sping     │ linux                                                             │ running │ 172.20.20.36   │
│                              │ quay.io/superq/smokeping-prober:v0.9.0                            │         │ N/A            │
├──────────────────────────────┼───────────────────────────────────────────────────────────────────┼─────────┼────────────────┤
│ clab-sros_bngt-tel-syslog    │ linux                                                             │ running │ 172.20.20.31   │
│                              │ harbor.nice.nokia.net/docker/linuxserver/syslog-ng:4.8.1-r1-ls153 │         │ N/A            │
╰──────────────────────────────┴───────────────────────────────────────────────────────────────────┴─────────┴────────────────╯
```

Then it's a time to let in your subscribers by running BNG Blaster, just execute following command:

```bash
docker exec -it clab-sros_bngt-bngblaster bash
```

Then, you can run the following command to generate traffic:
```bash
bngblaster -C pppoe.json -I -l dhcp
```

OR do it in one go:

```bash
docker exec -it clab-sros_bngt-bngblaster bash -c 'bngblaster -C pppoe.json -I -l dhcp'
```

Then you can perform SSH port forwarding to your remote machine toward ro connect locally to `clab-sros_bngt-tel-graf:3000` and voilà... your nice dashboard are ready to use.

***_BNG State and Redundancy_***

![BNG State and Redundancy](pic/BNG_State_and_Redundancy.png)

***_BNG Resources_***

![BNG Resrouces](pic/BNG_Resources.png)

***_Core and Aggregation Network_***

![Core and Aggregation Network](pic/Core_and_Aggregation_Network.png)

***__Subscriber Inside__***

![Subscriber Inside](pic/Subscriber_Inside.png)

***__Traffic__***

![Traffict](pic/Traffic.png)
