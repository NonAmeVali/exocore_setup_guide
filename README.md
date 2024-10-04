# Exocore
---

# Exocore Node Installation Guide

This guide provides instructions for preparing and setting up the Exocore binary. You will manage three keys throughout the process: the **account key**, **consensus key**, and **ETH private key**. Please keep this in mind as you proceed through the steps.

## 1. Installing Exocore Binary

The binary for running an Exocore node is called `exocored` (Exocore daemon). You can either download it from the releases page or compile it from source.

### Option 1: Downloading the Binary

Ensure you are using **version 1.0.3** for compatibility with the `exocoretestnet_233-5` network.

```bash
VERSION="1.0.3"
wget -O exocored_${VERSION}.tar.gz "https://github.com/ExocoreNetwork/exocore/releases/download/v${VERSION}/exocore_${VERSION}_$(uname -s)_$(uname -m | sed 's/x86_64/amd64/' | sed 's/aarch64/arm64/').tar.gz"
```

Extract and move the binary to your `$PATH` (e.g., `/usr/bin/`):

```bash
tar -xvzf exocored_${VERSION}.tar.gz
mv bin/exocored /usr/bin/
```

Verify the installation:

```bash
exocored version
# Output should be: 1.0.3
```

### Option 2: Compiling from Source

Please refer to the detailed instructions [here](https://github.com/ExocoreNetwork/exocore) for compiling from source.

---

## 2. Firewall Configuration

Configure the firewall to protect your node. Allow only necessary ports, such as the p2p port (defaults to 26656 over TCP).

### Option 1: Using UFW

```bash
# Block all incoming connections
ufw default deny incoming
# Allow SSH on port 22
ufw allow ssh
# Allow P2P connections on port 26656
ufw allow 26656/tcp
# Enable the firewall
ufw enable
```

### Option 2: Using Firewalld

```bash
# Block all incoming connections
firewall-cmd --set-default-zone=drop
# Allow SSH on port 22
firewall-cmd --permanent --add-service=ssh
# Allow P2P connections on port 26656
firewall-cmd --permanent --add-port=26656/tcp
# Reload the firewall configuration
firewall-cmd --reload
```

---

## 3. Initializing the Node

After downloading the binary, initialize the node:

```bash
CHAIN_ID="exocoretestnet_233-5"
MONIKER="your_moniker" # Replace with your chosen moniker (ASCII alphanumeric)
HOMEDIR="/home/$USER/.exocored" # Default home directory
exocored --home $HOMEDIR init $MONIKER --chain-id $CHAIN_ID
exocored --home $HOMEDIR config chain-id $CHAIN_ID
```

The validator's private key is stored in `$HOMEDIR/config/priv_validator_key.json`. Backup this key or the mnemonic phrase if using `--recover` during initialization.

---

## 4. Key Management

The **account key** is used to sign transactions. Change the keyring backend to `file` or `pass` for headless systems:

```bash
exocored --home $HOMEDIR config keyring-backend file
```

### Adding an Account

Create or recover an account key:

```bash
ACCOUNT_KEY_NAME="my_key"
exocored --home $HOMEDIR keys add $ACCOUNT_KEY_NAME
```

Backup the generated mnemonic phrase securely.

---

## 5. Seeds and Configuration

Edit `$HOMEDIR/config/config.toml` to set seed nodes:

```bash
seeds = "5dfa2ddc4ce3535ef98470ffe108e6e12edd1955@seed2t.exocore-restaking.com:26656,4cc9c970fe52be4568942693ecfc2ee2cdb63d44@seed1t.exocore-restaking.com:26656"
```

Set minimum gas prices and enable gRPC in `$HOMEDIR/config/app.toml`:

```bash
minimum-gas-prices = "0.0001aexo"
grpc.enable = true
```

---

For more detailed configuration, refer to the full documentation and the [Cosmos SDK documentation](https://docs.cosmos.network/).
