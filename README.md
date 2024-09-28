# Chainbase-AVS-node
$15M chainbase AVS node setup guide

# Chainbase Setup Operator Testnet

X : https://x.com/ChainbaseHQ

website : https://chainbase.com/

Connect your wallet to get genisis zircon points

testnet : https://genesis.chainbase.com/referral?referral_code=6T1FR6863

Refferal code : 6T1FR6863

---

### **System Requirement**

---

| Class | vCPUs (10th gen+) | Memory | Networking Capacity |
| --- | --- | --- | --- |
| General Purpose - large | 2 | 8 GB | 5 Mbps |
| General Purpose - xl | 4 | 16 GB | 25 Mbps |
| General Purpose - 4xl | 16 | 64 GB | 5 Gbps |

---

Join our Telegram [`@cryptoconsol`](https://t.me/cryptoconsol)
Follow twitter : https://x.com/cryptoconsol
---

Youtube guide : https://www.youtube.com/watch?v=k6j7UMvTYq4

## **1. Install Dependencies**

Update & Install Packages :

```
sudo apt update & sudo apt upgrade -y
sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev curl git wget make jq build-essential pkg-config lsb-release libssl-dev libreadline-dev libffi-dev gcc screen unzip lz4 -y
```

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

Add Permission

```
sudo groupadd docker
sudo usermod -aG docker $USER
```

Install GO

```
sudo rm -rf /usr/local/go
curl -L https://go.dev/dl/go1.22.4.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> $HOME/.bash_profile
source .bash_profile
go version
```

---

## **2. Install EigenLayer**

```
curl -sSfL https://raw.githubusercontent.com/layr-labs/eigenlayer-cli/master/scripts/install.sh | sh -s

export PATH=$PATH:~/bin

eigenlayer --version
```

---

## 3. Clone Repo

```
git clone https://github.com/chainbase-labs/chainbase-avs-setup

cd chainbase-avs-setup/holesky
```

## **4. Create Eigenlayer wallet**

```
eigenlayer operator keys create --key-type ecdsa opr
```

## **5. Config & Register Operator**

```
eigenlayer operator config create
```

- `operator address`: Your Eigenlayer ETH address
- `earnings address`: press `Enter`
- `ETH rpc url`: [https://ethereum-holesky-rpc.publicnode.com](https://ethereum-holesky-rpc.publicnode.com/)
- `network`: holesky
- `signer type`: local_keystore
- `ecdsa key path:`: /root/.eigenlayer/operator_keys/opr.ecdsa.key.json

## 6. Setup Metadata and Operator


```
nano metadata.json
```
Fill all the required data, check youtube video for reference

```
nano operator.yaml
```

## **7. Run Node**

Go to : https://cloud.google.com/application/web3/faucet/ethereum/holesky

Paste the operator EVM address and get faucet.

```
eigenlayer operator register operator.yaml
```
# Check status
```
eigenlayer operator status operator.yaml
```

## **8. Config Chainbase AVS**

---

do file .env

# Delete old files

```
rm -rf .env
```

# Open Edit menu

```
nano .env
```

## paste this in .env 

fill your password `NODE_ECDSA_KEY_PASSWORD`

```bash
# Chainbase AVS Image
MAIN_SERVICE_IMAGE=repository.chainbase.com/network/chainbase-node:testnet-v0.1.7
FLINK_TASKMANAGER_IMAGE=flink:latest
FLINK_JOBMANAGER_IMAGE=flink:latest
PROMETHEUS_IMAGE=prom/prometheus:latest

MAIN_SERVICE_NAME=chainbase-node
FLINK_TASKMANAGER_NAME=flink-taskmanager
FLINK_JOBMANAGER_NAME=flink-jobmanager
PROMETHEUS_NAME=prometheus

# FLINK CONFIG
FLINK_CONNECT_ADDRESS=flink-jobmanager
FLINK_JOBMANAGER_PORT=8081
NODE_PROMETHEUS_PORT=9091
PROMETHEUS_CONFIG_PATH=./prometheus.yml

# Chainbase AVS mounted locations
NODE_APP_PORT=8080
NODE_ECDSA_KEY_FILE=/app/operator_keys/ecdsa_key.json
NODE_LOG_DIR=/app/logs

# Node logs configs
NODE_LOG_LEVEL=debug
NODE_LOG_FORMAT=text

# Metrics specific configs
NODE_ENABLE_METRICS=true
NODE_METRICS_PORT=9092

# holesky smart contracts
AVS_CONTRACT_ADDRESS=0x5E78eFF26480A75E06cCdABe88Eb522D4D8e1C9d
AVS_DIR_CONTRACT_ADDRESS=0x055733000064333CaDDbC92763c58BF0192fFeBf

###############################################################################
####### TODO: Operators please update below values for your node ##############
###############################################################################
# TODO: Operators need to point this to a working chain rpc
NODE_CHAIN_RPC=https://rpc.ankr.com/eth_holesky
NODE_CHAIN_ID=17000

# TODO: Operators need to update this to their own paths
USER_HOME=$HOME
EIGENLAYER_HOME=${USER_HOME}/.eigenlayer
CHAINBASE_AVS_HOME=${EIGENLAYER_HOME}/chainbase/holesky

NODE_LOG_PATH_HOST=${CHAINBASE_AVS_HOME}/logs

# TODO: Operators need to update this to their own keys
NODE_ECDSA_KEY_FILE_HOST=${EIGENLAYER_HOME}/operator_keys/opr.ecdsa.key.json

# TODO: Operators need to add password to decrypt the above keys
# If you have some special characters in password, make sure to use single quotes
NODE_ECDSA_KEY_PASSWORD=xxx
```


do **`docker-compose.yml` file**


# Remove old file

```
rm -rf docker-compose.yml
```

# Open edit menu

```
nano docker-compose.yml
```

paste this in **`docker-compose.yml`**

You can change ports if any of them are in used in another node: 8081, 9091, 8080, 9092
If you want to change 8081 to 35081 (Eg: 35081:8081)

```
services:
  prometheus:
    image: ${PROMETHEUS_IMAGE}
    container_name: ${PROMETHEUS_NAME}
    env_file:
      - .env
    volumes:
      - "${PROMETHEUS_CONFIG_PATH}:/etc/prometheus/prometheus.yml"
    command: 
      - "--enable-feature=expand-external-labels"
      - "--config.file=/etc/prometheus/prometheus.yml"
    ports:
      - "${NODE_PROMETHEUS_PORT}:9090"
    networks:
      - chainbase
    restart: unless-stopped

  flink-jobmanager:
    image: ${FLINK_JOBMANAGER_IMAGE}
    container_name: ${FLINK_JOBMANAGER_NAME}
    env_file:
      - .env
    command: jobmanager
    networks:
      - chainbase
    restart: unless-stopped

  flink-taskmanager:
    image: ${FLINK_JOBMANAGER_IMAGE}
    container_name: ${FLINK_TASKMANAGER_NAME}
    env_file:
      - .env
    depends_on:
      - flink-jobmanager
    command: taskmanager
    networks:
      - chainbase
    restart: unless-stopped

  chainbase-node:
    image: ${MAIN_SERVICE_IMAGE}
    container_name: ${MAIN_SERVICE_NAME}
    command: ["run"]
    env_file:
      - .env
    ports:
      - "${NODE_APP_PORT}:${NODE_APP_PORT}"
      - "${NODE_METRICS_PORT}:${NODE_METRICS_PORT}"
    volumes:
      - "${NODE_ECDSA_KEY_FILE_HOST:-./opr.ecdsa.key.json}:${NODE_ECDSA_KEY_FILE}"
      - "${NODE_LOG_PATH_HOST}:${NODE_LOG_DIR}:rw"
    depends_on:
      - prometheus
      - flink-jobmanager
      - flink-taskmanager
    networks:
      - chainbase
    restart: unless-stopped

networks:
  chainbase:
    driver: bridge
```

create a folder for docker
```
source .env && mkdir -pv ${EIGENLAYER_HOME} ${CHAINBASE_AVS_HOME} ${NODE_LOG_PATH_HOST}
```

give permission 
```
chmod +x ./chainbase-avs.sh
```

**update `prometheus.yml`**input your operator address

```
nano prometheus.yml
```

## **9. Run Chainbase AVS**

---

Register first

```
./chainbase-avs.sh register
```

Run AVS

```
./chainbase-avs.sh run
```

## Optional

---

Check logs

```
docker compose logs chainbase-node -f
```

Check AVS Link

```
export PATH=$PATH:~/bin

eigenlayer operator status operator.yaml
```

Check Operator Health

# If your port is 8080
```
curl -i localhost:8080/eigen/node/health
```
Operator setup is complete, fill in this Google form and post your operator address to Chainbase discord : https://discord.gg/wMXe4GDR

https://docs.google.com/forms/d/e/1FAIpQLSd-ikaoIrOMd-L_pTvr8e-BIOTxQjkRh8ENGClyl7m1IFbqPQ/viewform


--
## Update Node
```
cd chainbase-avs-setup/holesky
```
```
docker pull repository.chainbase.com/network/chainbase-node:v0.1.9
```
### Edit .env file with latest version
```
nano .env
```


