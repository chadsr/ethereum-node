# Dockerized Ethereum / Swarm Node

A dockerized Ethereum related node setup, using docker-compose.

**Warning(s):**
- The docker-compose setup builds images locally, since official docker images are only built for x86_64 and I wanted this to compile onto ARM variants as well. Make sure the system has enough resources to do the compilation (1GB of RAM should do).
- This setup was designed for running on locally networked hardware. I take no responsibility if services are exposed to the Internet and you are not adequately firewalled.

## Config
Secrets are handled through environment variables, which can be set by renaming `.config.env.example` to `.config.env` and changing account values to your own.

Grafana default credentials are `admin:admin`. Change them as soon as Grafana is up and running.

## Requirements
- Adequate storage space (Ideally >300Gb. This is a full-node setup, but could be tweaked otherwise)
- Minimum of 4GB RAM (If you have 4GB, enable a swap partition to avoid issues!)
- `systemd` (or some equivalent such as `supervisord`)
- `docker` (With systemd daemon, or equivalent running)
- `docker-compose`
- An [Ethereum account](https://github.com/ethereum/go-ethereum/wiki/Managing-your-accounts#creating-an-account)

## Installing
1. Clone this repository:
```
git clone https://github.com/Chadsr/ethereum_node.git
```

2. Init and update Git submodules
```
git submodule init
git submodule update
```

3. Copy the example config to `.config.env`:
```
cp .config.env.example .config.env
```


4. Edit the values in `.config.env` to your preference.

**Note: An Ethereum account will NOT automatically be generated. It is assumed that you have already [created one](https://github.com/ethereum/go-ethereum/wiki/Managing-your-accounts#creating-an-account).**

### systemd installation
An example systemd unit file:
```
[Unit]
Description=Ethereum Dockerized Services
Requires=docker.service
After=docker.service

[Service]
EnvironmentFile=/path/to/ethereum_node/.config.env
WorkingDirectory=/path/to/ethereum_node
Restart=always

ExecStart=/usr/bin/docker-compose -f compose_files/geth.yml -f compose_files/swarm.yml -f compose_files/metrics.yml up --build

ExecStop=/usr/bin/docker-compose down

[Install]
WantedBy=multi-user.target
```

1. Write the above to a file named `/etc/systemd/system/ethereum.service`
 and update the working directory to point to the cloned repository.

2. Enable the systemd service:
```
sudo systemctl enable ethereum.service
```

3. Start the systemd service:
```
sudo systemctl start ethereum.service
```

4. (Optionally) monitor the output of your newly running service:
```
sudo journalctl -f -u ethereum.service
```

## Notes
### Architecture Support
Since this setup is designed to build the docker images locally, it should work on any common CPU architecture. I am personally running it on aarch64 (ARMv8).

### Geth
**Current version:** v1.9.19


### Swarm
**Current version:** v0.5.7
