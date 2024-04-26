# Ethereum Wallet Monitoring Project

## Introduction

Welcome to the Ethereum Wallet Monitoring project! This guide will walk you through setting up a system to monitor an Ethereum wallet's balance over time. We'll be using Docker to containerize our services, including ETHexporter for fetching wallet balances, Prometheus for data storage, and Grafana for visualization.

## Project Setup

### Step 1: Obtain Ethereum on the Testnet

Before we start, you'll need to get some free Ether on the Ethereum testnet. This will allow you to test the monitoring setup without using real funds.

### Step 2: Setting Up ETHexporter

ETHexporter is a tool that allows us to fetch Ethereum wallet balances and expose them in a format that Prometheus can scrape.

**Docker Compose Setup for ETHexporter:**


Docker image is availeble at : http://20.108.47.166:9015/metrics

```yaml
version: '3'
services:
  ethexporter:
    image: hunterlong/ethexporter
    ports:
      - "9015:9015"
    environment:
      - GETH=https://mainnet.infura.io/f9452131f24a4b5c9cf700547af9d571
    volumes:
      - ./addresses.txt:/app/addresses.txt

```

### Step 3: Setting Up Prometheus server

Docker image is available at : http://20.108.47.166:9090/

```yaml
version: '3.7'

services:
  ethexporter:
    image: hunterlong/ethexporter:latest
    ports:
      - "9015:9015"
    environment:
      - GETH=https://mainnet.infura.io/f9452131f24a4b5c9cf700547af9d571
    volumes:
      - ./addresses.txt:/app/addresses.txt

  prometheus:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.enable-lifecycle'

```

### Step 4: Setting Up Grafana


http://20.108.47.166:3000/



### Step 4: Setting Up Traefik


```yaml
version: '3.7'

services:
  reverse-proxy:
    # The official v2 Traefik docker image
    image: traefik:v2.11
    # Enables the web UI and tells Traefik to listen to docker
    command: --api.insecure=true --providers.docker
    ports:
      # The HTTP port
      - "80:80"
      # The Web UI (enabled by --api.insecure=true)
      - "8080:8080"
    volumes:
      # So that Traefik can listen to the Docker events
      - /var/run/docker.sock:/var/run/docker.sock
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.your-service.rule=Host(`20.108.47.166`) && PathPrefix(`/`)"
      - "traefik.http.routers.your-service.entrypoints=web"
      - "traefik.http.routers.your-service.middlewares=test-ipwhitelist"
      - "traefik.http.middlewares.test-ipwhitelist.ipwhitelist.sourcerange=197.8.1.185"
      - "traefik.http.services.your-service.loadbalancer.server.port=3000"


```

