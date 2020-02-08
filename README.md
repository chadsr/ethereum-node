# Dockerized Ethereum / Swarm Node

A dockerized Ethereum related node setup, using docker-compose.

**Warning(s):**
This docker-compose setup builds images locally for now, so that the binaries are compiled for the local architecture. Make sure the system has enough resources to do the compilation.

## Config
Secrets are handled through environment variables, which can be set by renaming `.config.env.example` to `.config.env` and changing account values to your own.

## Installing
1. Clone this repository:
```
git clone https://github.com/Chadsr/ethereum_node.git
```

2. Copy the example config to `.config.env`:
```
cp .config.env.example .config.env
```


3. Edit the values in `.config.env` to your preference. 

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

ExecStart=/usr/bin/docker-compose up --build

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
### Geth
**Current version:** v1.9.10


### Swarm
**Current version:** v0.5.6

#### Modifications
The dockerfile has been modified to utilise the locally built geth image, instead of pulling `ethereum/client-go`. As mentioned in the warning, this allows for other non-supported architectures such as ARM.
