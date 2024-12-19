# Run a full node with docker
This guide will help you start a full node running in the docker container using run-morph-node

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
cd run-morph-node/morph-node
make run-node
```

The command `make run-node` takes the `../mainnet` as your node's Home directory by default. There will be two folders in the Home directory named `geth-data` and `node-data`, serving as data directories for the execution client and consensus client of the morph ndoe, respectively.

This command will also generate the `secret-jwt.txt` file under Home directory for the authentication during RPC calls between the execution client and consensus client.

#### **If you receive the following message:**

```
✔ Container morph-geth                                                                                                                                   Created0.4s 
! geth The requested image's platform (linux/arm64/v8) does not match the detected host platform (linux/amd64/v3) and no specific platform was requested 0.0s 
✔ Container morph-node                                                                                                                                   Created0.0s 
! node The requested image's platform (linux/arm64/v8) does not match the detected host platform (linux/amd64/v3) and no specific platform was requested 0.0s 
Attaching to morph-node
WARN[0323] could not start menu, an error occurred while starting. 
morph-node  | exec /usr/local/bin/morphnode: exec format error
```

It means that the server you are using is not compatible with the ARM64 architecture of the Docker image. To resolve this, you need to enable emulation for ARM64 using QEMU.

#### **Steps to Fix the Issue**
**1. Install QEMU**
To enable multi-platform emulation, follow these steps:

```
sudo apt update
sudo apt install qemu qemu-user-static
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
```

**2. Enable Docker Buildx**
Docker Buildx allows you to run ARM64 images on other platforms. You can enable it by running the following commands:

```
docker buildx create --use
docker buildx inspect --bootstrap
```

**3. Run ARM64 Image on Docker**
Once Buildx is enabled, you can force Docker to run ARM64 images even on an incompatible platform. Use the following command to run the ARM64 image:
If you are using Docker Compose, you can specify the platform like this:

```
docker compose up --build --platform linux/arm64
```

**4. Re-clone the Project**
If you haven't already, clone the project again to make sure you have the latest files:

```
git clone https://github.com/morph-l2/run-morph-node.git
cd run-morph-node
```

**5. Run Docker Compose for ARM64**
Finally, run Docker Compose to build and start the containers using ARM64:

```
docker compose up --build
```

**6. Running make run-node**
After the Docker containers have been successfully started, you can navigate to the morph-node directory and run the following command to start the validator node:

```
cd morph-node
make run-node
```

This command will start the node in your Docker container after the build process completes, and it should now run without any architecture-related issues.

Source : https://docs.morphl2.io/docs/build-on-morph/developer-resources/node-operation/full-node/run-in-docker#quick-start
