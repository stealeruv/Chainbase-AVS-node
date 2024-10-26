Chainbase Node update

# Join Crypto Console Community

Join TG : [Crypto Console Telegram](https://t.me/cryptoconsol) 

Follow X : [Crypto Console Twitter](https://www.x.com/cryptoconsol) 

Subscribe : [Crypto Console Youtube](https://www.youtube.com/@cryptoconsole)


---

# VPS Options

Credit Card/Paypal/crypto credit card : 

VPS 1 : [Contabo: Cloud VPS 1](https://www.jdoqocy.com/click-101278318-15692486) 

VPS 2 : [Contabo: Cloud VPS 2](https://www.tkqlhce.com/click-101278318-13796472)

VPS 3 : [Contabo: Cloud VPS 3](https://www.dpbolvw.net/click-101278318-13796474)

VPS 4 : [Contabo: Cloud VPS 4](https://www.anrdoezrs.net/click-101278318-13796476)


### Download Eigenlayer
```
curl -sSfL https://raw.githubusercontent.com/layr-labs/eigenlayer-cli/master/scripts/install.sh | sh -s
export PATH=$PATH:~/bin
eigenlayer --version
```

### BACKUP ECDSA Key
```
cd .eigenlayer/operator_keys
cat opr.ecdsa.key.json
```
### Create Bls key 
```
eigenlayer operator keys create --key-type bls opr
```
### Delete the Old Chainbase
```
cd
rm -rf chainbase-avs-setup
```
# Setup New Chainbase AVS 

### Clone the Chainbase Repo :
```
git clone https://github.com/chainbase-labs/chainbase-avs-setup && cd chainbase-avs-setup/holesky
```
### Make env:
```
cp .env.example .env
```
###  Edit the env
```
nano .env
```
```
NODE_ECDSA_KEY_FILE_PATH=/root/.eigenlayer/operator_keys/opr.ecdsa.key.json
NODE_BLS_KEY_FILE_PATH=/root/.eigenlayer/operator_keys/opr.bls.key.json
OPERATOR_ECDSA_KEY_PASSWORD=ECDSAKeyPassword
OPERATOR_BLS_KEY_PASSWORD=BlsKeyPassword
OPERATOR_ADDRESS=avswalletaddress
NODE_SOCKET=ip:8011
OPERATOR_NAME=cryptoconsole
```

### Make Executable 
```
chmod +x ./chainbase-avs.sh
```

## Operating the Chainbase AVS

Register as an Operator

```
./chainbase-avs.sh register
```

### Start Node

```
./chainbase-avs.sh run
```

### Test Node
```
./chainbase-avs.sh test
```
If you see the output All systems are working for your manuscript node in the command line output, it indicates that your node is running correctly.

​
### Update Node socket

```
./chainbase-avs.sh socket
```

If your server’s public IP address is changed after you register as an operator, you need to update the node socket. Configure NODE_SOCKET in .env file, then run the following command:

### Update Node version

```
./chainbase-avs.sh stop
./chainbase-avs.sh update
./chainbase-avs.sh run
```

### Check Logs

```
docker logs -f <container_id>
```

Dashboard
You can visit **<your_server_public_ip>:3010** on browser to view the dashboard to confirm the status of your node. 

If you are unable to access the page in your browser, please verify that port 3010 on your server is open and properly configured in firewall settings.
