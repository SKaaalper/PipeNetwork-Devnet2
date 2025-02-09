# **Pipe Network Devnet2 Node Setup Guide**

![banner](https://github.com/SKaaalper/PipeNetwork-Devnet2/blob/main/image3.png)

To set up a Pipe Network Node, you need to allocate sufficient disk space and RAM to cache data for egress. The more disk space you allocate, the more data your node can store, which can improve egress performance over time. Follow the steps below to configure your system for optimal performance.

## System Requirements

- OS: Linux
- RAM: Minimum `4GB` (configurable), more RAM is recommended for higher rewards.
- Disk Space: At least `100GB` free (configurable). A sweet spot is between `200-500GB`.
- Internet Connectivity: Available 24/7.

Pipe Network official Docs: [Pipe PoP Docs](https://docs.pipe.network/devnet-2)

## Install Dependecies:

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang aria2 bsdmainutils ncdu unzip libleveldb-dev -y
```

## Installation:

1. Create the necessary directories for the Pipe Network node:
```
mkdir -p /root/pipenetwork
mkdir -p /root/pipenetwork/download_cache
cd /root/pipenetwork
```

2. Allow Port `8003`:
```
ufw allow 8003/tcp
```

3. Download and Install the Pipe Node, Depending on whether you have a whitelist spot, the installation command differs.
- With whitelist: To obtain the URL `Right-click the 'Download PoP Node' in your email` and Click `Copy link address`
```
curl -L -o pop "<URL_FROM_EMAIL>"
```

- Without whitelist:
```
wget -O pop "https://dl.pipecdn.app/v0.2.4/pop"
```

4. Make the Node Executable:
```
chmod +x pop
```

5. Sign Up and Start the Node:
```
./pop --signup-by-referral-route 9ce6d2351acf8d1f
```

6. Configure the Pipe Network Service:
```
nano /etc/systemd/system/pipe-pop.service
```

In the service file, adjust the following values:

- RAM Size: Minimum 4GB, higher values are better.
- Max Disk: Minimum 100GB, 200-500GB recommended.
- PubKey: Replace `<Your_Pubkey_Address>` with your Solana wallet public key.

Copy the content of the service file:
```
[Unit]
Description=Pipe POP Node Service
After=network.target
Wants=network-online.target

[Service]
User=root
Group=root
ExecStart=/root/pipenetwork/pop --ram=12 --pubKey <Your_Pubkey_Address> --max-disk 300 --cache-dir /var/cache/pop/download_cache
Restart=always
RestartSec=5
LimitNOFILE=65536
LimitNPROC=4096
StandardOutput=journal
StandardError=journal
SyslogIdentifier=dcdn-node
WorkingDirectory=/root/pipenetwork

[Install]
WantedBy=multi-user.target
```

7. Reload systemd and Start the Node Service:
```
sudo systemctl daemon-reload
sudo systemctl enable pipe-pop
sudo systemctl start pipe-pop
```

Check the status of the service to ensure it is running properly:
```
sudo systemctl status pipe-pop
```

8. Verify Your Setup:
- Check if the `pop` process is running:
```
./pop --status
```
![banner](https://github.com/SKaaalper/PipeNetwork-Devnet2/blob/main/image1.png)
- Check your points (if applicable):
```
./pop --points-route
```
- Generate a referral code:
```
./pop --gen-referral-route
```
![banner](https://github.com/SKaaalper/PipeNetwork-Devnet2/blob/main/image2.png)

9. Check the logs:
```
journalctl -u pipe-pop -f
```


## Updating the Node for Existing Users:

1. Stop the Service:
```
sudo systemctl stop pipe-pop
```

2. Remove the Old Node:
```
rm pop
```

3. Download the Latest Version:
```
wget -O pop "https://dl.pipecdn.app/v0.2.4/pop"
chmod +x pop
```

4. Reload systemd and Restart the Service:
```
sudo systemctl daemon-reload
sudo systemctl restart pipe-pop
```

5. Check the Logs:
```
journalctl -u pipe-pop -f
```
```
./pop --status
```

Check your node status: [https://dashboard.pipenetwork.com/](https://dashboard.pipenetwork.com)


## Additional Notes
- Ensure that your node has enough disk space and RAM for optimal performance.
- Regularly check the status of your node to ensure it is functioning correctly.
- If you are using a VPS, make sure to adjust the configuration according to your system's resources.
