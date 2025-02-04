---
layout: post
title: Configuring Promtail Log Agent
subtitle: 
cover-img:
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Promtail, Logging, Log Aggregation, Log Shipping, Logging And Monitoring]
author: keylearn
---

This is a continuation of a story published about [centralized logging](https://lahirumw.github.io/2025-02-03-centralized-log-grafana-loki-prom/) . Let’s talk about promtail today.

Promtail is an open-source log shipper and tailer that is part of the Grafana Loki project, which is designed for collecting, processing, and storing log data. Promtail is primarily used for aggregating logs from various sources, parsing them, and forwarding them to a centralized log storage system, like Loki. It is often used in conjunction with other tools in the observability and monitoring ecosystem, especially in environments where Kubernetes and Docker are prevalent.

Below code block show you how to spin up a promtail container using docker compose.

~~~
version: '3.8'

services:

  promtail:
    image:  grafana/promtail:2.8.4
    container_name: promtail
    volumes:
      - ./config/promtail.yaml:/etc/promtail/docker-config.yaml
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock
    command: -config.file=/etc/promtail/docker-config.yaml
    networks:
      - loki

networks:
  loki:
~~~

Configuration file `promtail.yaml` for promtail is available in config folder in root directory.

~~~
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: f_scrape
    docker_sd_configs:
      - host: unix:///var/run/docker.sock
        refresh_interval: 5s
        filters:
          - name: label
            values: ["logging=promtail"] 
    relabel_configs:
      - source_labels: ['__meta_docker_container_name']
        regex: '/(.*)'
        target_label: 'container_name'
      - source_labels: ['__meta_docker_container_id']
        target_label: 'container_id'
      - source_labels: ['__meta_docker_container_log_stream']
        target_label: 'logstream'
      - source_labels: ['__meta_docker_container_label_logging_env']
        target_label: 'env'
      - source_labels: ['__meta_docker_container_label_logging_instance']
        target_label: 'instance'
~~~

This Promtail configuration is used for log scraping and forwarding to Loki, a log aggregation and storage system. Here’s a summary of its key components:

**Server Configuration:**

`http_listen_port: 9080`: Promtail listens on port 9080 for incoming HTTP requests.

`grpc_listen_port: 0`: The gRPC server is disabled (port 0), indicating that Promtail won't accept gRPC connections.

**Positions:**

`filename: /tmp/positions.yaml`: Promtail maintains a position file at /tmp/positions.yaml to keep track of the log positions it has read. This helps in resuming log scraping after restarts.

**Clients:**

Promtail is configured to send scraped logs to Loki via HTTP.

`- url: http://gateway:3100/loki/api/v1/push`: Specifies the URL where Promtail should push log entries. It's typically the Loki gateway.

**Scrape Configurations:**

`job_name: f_scrape`: This is the name of the scrape job, used for identification.

`docker_sd_configs`: This section defines how Promtail should discover Docker containers and scrape their logs.

`- host: unix:///var/run/docker.sock`: Promtail uses the Docker socket (/var/run/docker.sock) to access container metadata.

`refresh_interval: 5s`: Specifies how often Promtail should refresh its list of containers (every 5 seconds in this case).

`filters`: Defines filters to select which containers to scrape based on labels.

`- name: label`: Selects containers with the label `logging=promtail`.

`relabel_configs`: These configurations define how to manipulate metadata and labels for the logs before sending them to Loki.

Various `source_labels` and `target_label` mappings are applied, renaming or copying container metadata to more meaningful labels for log entries in Loki.

`container_name`: Extracted from `__meta_docker_container_name`.

`container_id`: Extracted from `__meta_docker_container_id`.

`logstream`: Extracted from `__meta_docker_container_log_stream`.

`env`: Extracted from `__meta_docker_container_label_logging_env`.

`instance`: Extracted from `__meta_docker_container_label_logging_instance`.

In summary, this Promtail configuration is designed to scrape logs from Docker containers, transform and label them appropriately, and then send them to Loki for further processing and storage. It’s typically used in a containerized environment where logs need to be collected and centralized for monitoring and analysis.

To better grasp the entire concept of [centralized logging](https://lahirumw.github.io/2025-02-03-centralized-log-grafana-loki-prom/), I encourage you to explore the narrative regarding [centralized logging](https://lahirumw.github.io/2025-02-03-centralized-log-grafana-loki-prom/) in conjunction with this story.

That’s all for this story. Thank you!