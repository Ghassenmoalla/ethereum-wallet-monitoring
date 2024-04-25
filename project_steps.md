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
![Uploading image.png…]()

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

