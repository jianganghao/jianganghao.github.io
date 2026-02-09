---
layout: post
title: Running Tasks as a System Daemon with systemd
date: 2026-02-09 10:00:00-0400
description: A practical guide to running Streamlit apps (or any task) as a systemd service
categories: data-science
tags: linux, systemd, devops, streamlit
giscus_comments: false
related_posts: false
related_publications: true
---

If you want to run a Streamlit dashboard, you can use `screen`, but a more reliable approach is to run it as a system daemon with `systemd`. This post is based on a helpful tutorial by Shubham Dipt and adds a few practical details for real-world setups.

### 1. Create a Service File

SSH into your EC2 instance and move to the systemd unit directory:

```bash
cd /etc/systemd/system
```

Create a service file, for example `epcaldash.service`, and add the following:

```ini
[Unit]
Description=EPCAL Dashboard

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/epcal/epcal_dashboard
ExecStart=/home/ubuntu/miniconda3/envs/py39/bin/streamlit run epcal_dashboard.py
Restart=always

[Install]
WantedBy=multi-user.target
```

Notes:
- `WorkingDirectory` should point to your Streamlit app directory.
- `ExecStart` must use the correct Streamlit executable (e.g., from your virtual environment).

### 2. Optional: Run a Shell Script First

If you need extra commands (e.g., `iptables` for port forwarding), you can point `ExecStart` to a shell script instead:

```ini
[Unit]
Description=EPCAL Dashboard

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/epcal/epcal_dashboard
ExecStart=/bin/bash epcaldash_service.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

Example `epcaldash_service.sh`:

```bash
#! /bin/bash

sudo iptables -A PREROUTING -t nat -p tcp --dport 80 -j REDIRECT --to-ports 8501
/home/ubuntu/miniconda3/envs/py39/bin/streamlit run /home/ubuntu/epcal/epcal_dashboard/epcal_dashboard.py
```

Make it executable:

```bash
chmod +x epcaldash_service.sh
```

### 3. Reload, Start, and Enable

```bash
sudo systemctl daemon-reload
sudo systemctl start epcaldash.service
sudo systemctl status epcaldash.service
```

Enable on boot:

```bash
sudo systemctl enable epcaldash.service
```

Disable on boot:

```bash
sudo systemctl disable epcaldash.service
```

### 4. Logs

```bash
journalctl -u epcaldash.service
journalctl -u epcaldash.service -n 1000
```

---

With this setup, your Streamlit app runs as a managed service and will restart automatically if it fails.
