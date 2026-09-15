# Installing the Wazuh stack

## What is Needed

- Ubuntu Server 26.04 LTS (VM on VirtualBox)
- Docker & Docker Compose
- At least 4GB RAM allocated to the VM (8GB is safer)
- At least 50GB disk 

## Install steps

1. Clone the official `wazuh-docker` repo and go into the `single-node` folder
2. Start the stack:
```bash
   cd ~/wazuh-docker/single-node
   sudo docker compose up -d
```
<img width="853" height="170" alt="image" src="https://github.com/user-attachments/assets/188516d6-8102-4eb8-8299-4a3d4d7d7a9c" />

3. Wait  between 30 seconds to a minute for the indexer and manager to finish initializing
4. Open the dashboard at `https://localhost:8443`
<img width="900" height="500" alt="image" src="https://github.com/user-attachments/assets/ea9a4cda-14b4-475d-868d-fedfc73f811e" />

# Issues I ran into
## Didn't know the default admin password / needed to change it
The default password hash in `internal_users.yml` is bcrypt. Login kept failing even after I tried editing the hash directly.
Turns out editing the hash in `internal_users.yml` doesn't actually apply anything by itself, that file is just the *source config*. What actually gets checked during login is a separate *security index* living inside OpenSearch. We have to explicitly push the file's contents into that index.

**Fix:**
```bash
# 1. Generate a new bcrypt hash
sudo docker exec -it single-node-wazuh.indexer-1 bash -c \
  "JAVA_HOME=/usr/share/wazuh-indexer/jdk bash /usr/share/wazuh-indexer/plugins/opensearch-security/tools/hash.sh -p 'new_password'"

# 2. Edit internal_users.yml with the new hash (and change the username key too)
nano ~/wazuh-docker/single-node/config/wazuh_indexer/internal_users.yml

# 3. IMPORTANT: push the change into the cluster with securityadmin.sh
sudo docker exec -it single-node-wazuh.indexer-1 bash -c '
export JAVA_HOME=/usr/share/wazuh-indexer/jdk
export INSTALLATION_DIR=/usr/share/wazuh-indexer
CACERT=$INSTALLATION_DIR/config/certs/root-ca.pem
KEY=$INSTALLATION_DIR/config/certs/admin-key.pem
CERT=$INSTALLATION_DIR/config/certs/admin.pem
$INSTALLATION_DIR/plugins/opensearch-security/tools/securityadmin.sh \
  -cd $INSTALLATION_DIR/config/opensearch-security \
  -nhnv -cacert $CACERT -cert $CERT -key $KEY -icl -h 127.0.0.1
'
```

**Important Note:**
If changed the default indexer credentials, make sure to also update the `INDEXER_PASSWORD` variable inside `docker-compose.yml`[cite: 1]. If don't, the Filebeat pipeline and Dashboard will fail to connect and throw a `401 Unauthorized` error[cite: 1].
