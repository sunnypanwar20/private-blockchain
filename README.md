# private-blockchain
**# Private Blockchain Setup Documentation**

## **1. Introduction**
This document outlines the step-by-step process to set up a private blockchain using Geth. It includes the commands, configurations, and explanations necessary to successfully create and run a private blockchain network.

---

## **2. Prerequisites**
- **3 Linux servers** with root or sudo access.
- **Geth (Go-Ethereum) binary tools** to be installed.
- **Accounts setup** (1 shared account across servers, 1 unique account per server).
- **Genesis block configuration** (genesis.json file).

---

## **3. Server Setup**
1. **Update system packages:**
   ```bash
   sudo apt-get update && sudo apt-get upgrade
   ```

2. **Download Geth binary tools:**
   ```bash
   wget https://gethstore.blob.core.windows.net/builds/geth-alltools-linux-amd64-1.9.25-e7872729.tar.gz
   ```

3. **Extract the Geth binary files:**
   ```bash
   tar -zxvf geth-alltools-linux-amd64-1.9.25-e7872729.tar.gz
   ```

4. **Move the Geth binary to a system path:**
   ```bash
   sudo cp /root/geth-alltools-linux-amd64-1.9.25-e7872729/geth /usr/local/bin/
   ```
   This ensures the `geth` command is accessible from any directory.

---

## **4. Account Setup**
1. **Create new accounts:**
   ```bash
   geth account new --datadir foldername
   ```
   Repeat this step for each server, ensuring one shared account and one unique account per server.
   
2. **Note down the account addresses** as they will be required for mining and configuration.

---

## **5. Blockchain Initialization**
1. **Create a genesis block file (genesis.json) with the required configurations.**
   Example of `genesis.json`:
   ```json
   {
     "config": {
       "chainId": 16,
       "homesteadBlock": 0,
       "eip150Block": 0,
       "eip155Block": 0,
       "eip158Block": 0,
       "byzantiumBlock": 0
     },
     "difficulty": "1",
     "gasLimit": "9000000",
     "alloc": {
       "0xf27A40D622a544657A3B036b8De3869314D8a135": { "balance": "100000000000000000000" },
       "0x2E76ff3b83c3214C5e7e926BC0C3188E20897F74": { "balance": "100000000000000000000" }
     }
   }
   ```

2. **Initialize the blockchain on each server:**
   ```bash
   geth init genesis.json --datadir foldername
   ```
   This sets up the genesis block for the private blockchain.

---

## **6. Running the Blockchain**
1. **Run the Geth node on each server:**
   ```bash
   nohup geth --datadir foldername/data \
      --networkid 16 \
      --http \
      --http.addr 0.0.0.0 \
      --http.port 8545 \
      --http.api eth,net,web3,personal,miner,admin \
      --http.corsdomain "*" \
      --allow-insecure-unlock \
      --unlock "0xf27A40D622a544657A3B036b8De3869314D8a135,0x2E76ff3b83c3214C5e7e926BC0C3188E20897F74" \
      --password data/password.txt \
      --mine \
      --miner.threads 2 \
      --miner.etherbase 0x2E76ff3b83c3214C5e7e926BC0C3188E20897F74 \
      --miner.gasprice 1 \
      --miner.gastarget 9000000 \
      --miner.gaslimit 9000000 \
      --verbosity 3 \
      --port 30303 \
      --nodiscover >> foldername/geth.log 2>&1 &
   ```
   **Explanation of important flags:**
   - `--networkid 16`: Unique network ID for the private blockchain.
   - `--http.api`: Enables access to JSON-RPC methods.
   - `--allow-insecure-unlock`: Allows unlocking of accounts via HTTP API.
   - `--unlock`: Unlocks specific accounts for mining.
   - `--mine`: Enables mining on the blockchain.

2. **Check the status of the blockchain:**
   ```bash
   geth attach http://localhost:8545
   ```
   This attaches to the Geth console for interaction.

3. **Check if the mining is active and block height is increasing:**
   ```javascript
   eth.blockNumber
   ```

---

## **7. Peer-to-Peer Networking**
1. **Connect servers as peers:**
   Attach to the Geth console as mentioned above, then add peers using the following command:
   ```javascript
   admin.addPeer("enode://[enode_id]@[ip_address]:[port]")
   ```
   Replace `[enode_id]`, `[ip_address]`, and `[port]` with the appropriate values.

2. **View connected peers:**
   ```javascript
   admin.peers
   ```
   This command lists all the connected peers.

---

## **8. Common Commands**
- **View accounts:**
  ```javascript
  eth.accounts
  ```

- **Check current block number:**
  ```javascript
  eth.blockNumber
  ```

- **View mining status:**
  ```javascript
  miner.start()
  miner.stop()
  ```

- **Send Ether between accounts:**
  ```javascript
  eth.sendTransaction({from: "<sender_address>", to: "<receiver_address>", value: web3.toWei(1, "ether")})
  ```

---

## **9. Troubleshooting**
- **Geth process stopped:**
  Check the logs in `foldername/geth.log` to identify the issue.

- **Account unlock issues:**
  Ensure the `--allow-insecure-unlock` flag is set and the correct password file is provided.

- **Cannot connect to peers:**
  Ensure the network ID and port settings are correct, and use `admin.peers` to debug connections.

---

## **10. Best Practices**
- **Use a firewall** to protect open ports.
- **Use secure passwords** for account unlock files.
- **Monitor logs** regularly to detect issues.

---

## **11. Summary**
This document provides a complete guide to setting up and running a private blockchain using Geth. It covers everything from setting up Linux servers, creating accounts, initializing the genesis block, and mining to managing peers. Follow the steps carefully to ensure a successful setup.
