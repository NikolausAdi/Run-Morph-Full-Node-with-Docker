## **1. Install Dependencies**

Install Docker

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
docker version
```

Install Docker-Compose

```
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)

curl -L "https://github.com/docker/compose/releases/download/"$VER"/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

## **2. Quick Start**
   
Clone the dockerfile repository
```
git clone https://github.com/morph-l2/run-morph-node.git
```

Run the following command
```
cd morph-node
make run-node
```

The command `make run-node` takes the `../mainnet` as your node's Home directory by default. There will be two folders in the Home directory named `geth-data` and `node-data`, serving as data directories for the execution client and consensus client of the morph ndoe, respectively.

This command will also generate the `secret-jwt.txt` file under Home directory for the authentication during RPC calls between the execution client and consensus client.

Source : https://docs.morphl2.io/docs/build-on-morph/developer-resources/node-operation/full-node/run-in-docker#quick-start
