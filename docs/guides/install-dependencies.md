# Installing Gorai Dependencies on Ubuntu

This guide covers installing the runtime dependencies needed by gorai services on Ubuntu (amd64). For arm64 (Raspberry Pi), substitute the architecture in download URLs.

## Go Toolchain

Gorai requires Go 1.24+.

```bash
curl -LO https://go.dev/dl/go1.24.0.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.24.0.linux-amd64.tar.gz
rm go1.24.0.linux-amd64.tar.gz
```

Add to `~/.bashrc` or `~/.zshrc`:

```bash
export PATH=$PATH:/usr/local/go/bin
```

Verify:

```bash
go version
```

## NATS Server with JetStream

### Install

```bash
NATS_VERSION=v2.10.24
curl -LO https://github.com/nats-io/nats-server/releases/download/${NATS_VERSION}/nats-server-${NATS_VERSION}-linux-amd64.tar.gz
tar xzf nats-server-${NATS_VERSION}-linux-amd64.tar.gz
sudo mv nats-server-${NATS_VERSION}-linux-amd64/nats-server /usr/local/bin/
rm -rf nats-server-${NATS_VERSION}-linux-amd64 nats-server-${NATS_VERSION}-linux-amd64.tar.gz
```

### Configure

Create the data directory and config file:

```bash
sudo mkdir -p /usr/local/var/nats/jetstream
sudo mkdir -p /etc/nats
```

Create `/etc/nats/nats-server.conf`:

```
server_name: gorai-nats
listen: 0.0.0.0:4222

jetstream {
  store_dir: /usr/local/var/nats/jetstream
  max_mem: 256MB
  max_file: 2GB
}
```

Adjust `max_mem` and `max_file` based on available resources. On a Raspberry Pi with 4GB RAM, `max_mem: 128MB` and `max_file: 1GB` are reasonable starting points.

### Systemd Service

Create `/etc/systemd/system/nats-server.service`:

```ini
[Unit]
Description=NATS Server
After=network.target

[Service]
ExecStart=/usr/local/bin/nats-server -c /etc/nats/nats-server.conf
Restart=always
RestartSec=5
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable nats-server
sudo systemctl start nats-server
```

### Verify

```bash
sudo systemctl status nats-server
```

The server should be listening on port 4222 with JetStream enabled. Check the journal for confirmation:

```bash
journalctl -u nats-server --no-pager -n 20
```

Look for a line containing `JetStream` and `ready`.

## VictoriaMetrics

### Install

```bash
VM_VERSION=v1.108.1
curl -L https://github.com/VictoriaMetrics/VictoriaMetrics/releases/download/${VM_VERSION}/victoria-metrics-linux-amd64-${VM_VERSION}.tar.gz -o vm.tar.gz
tar xzf vm.tar.gz
sudo mv victoria-metrics-prod /usr/local/bin/victoria-metrics
rm vm.tar.gz
```

For arm64 (Raspberry Pi):

```bash
VM_VERSION=v1.108.1
curl -L https://github.com/VictoriaMetrics/VictoriaMetrics/releases/download/${VM_VERSION}/victoria-metrics-linux-arm64-${VM_VERSION}.tar.gz -o vm.tar.gz
tar xzf vm.tar.gz
sudo mv victoria-metrics-prod /usr/local/bin/victoria-metrics
rm vm.tar.gz
```

### Configure

Create the data directory:

```bash
sudo mkdir -p /usr/local/var/victoria-metrics
```

### Systemd Service

Create `/etc/systemd/system/victoria-metrics.service`:

```ini
[Unit]
Description=VictoriaMetrics
After=network.target

[Service]
ExecStart=/usr/local/bin/victoria-metrics -storageDataPath=/usr/local/var/victoria-metrics -retentionPeriod=90d -httpListenAddr=:8428
Restart=always
RestartSec=5
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable victoria-metrics
sudo systemctl start victoria-metrics
```

### Verify

```bash
sudo systemctl status victoria-metrics
curl http://localhost:8428/health
```

The health endpoint should return `OK`.

## Verifying Everything

After installing and starting both services:

```bash
# NATS is listening
sudo systemctl is-active nats-server

# VictoriaMetrics is listening
sudo systemctl is-active victoria-metrics
curl http://localhost:8428/health

# Both survive reboot
sudo systemctl is-enabled nats-server
sudo systemctl is-enabled victoria-metrics
```

After gorai-historian has been running and writing data:

```bash
curl 'http://localhost:8428/api/v1/query?query=gorai_sensor_data'
```
