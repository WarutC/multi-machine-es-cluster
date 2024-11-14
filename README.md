# Setup project
## Edit the following .env files for each node:

1. For Node 1 (192.168.1.26) - First Machine

```bash
.
.
.
NODE_NAME=es01
NODE_IP=192.168.1.26
SEED_HOSTS=192.168.1.26,192.168.1.27,192.168.1.28
INITIAL_MASTER_NODES=es01,es02,es03
```
2. For Node 2 (192.168.1.27) - Second Machine

```bash
.
.
.
NODE_NAME=es02
NODE_IP=192.168.1.27
SEED_HOSTS=192.168.1.26,192.168.1.27,192.168.1.28
INITIAL_MASTER_NODES=es01,es02,es03
```

3. For Node 3 (192.168.1.28) - Third Machine

```bash
.
.
.
NODE_NAME=es03
NODE_IP=192.168.1.28
SEED_HOSTS=192.168.1.26,192.168.1.27,192.168.1.28
INITIAL_MASTER_NODES=es01,es02,es03
```

## Setup steps for each machine:

1. Prepare the environment:

```bash
# Create directories
mkdir -p ~/elasticsearch/data

# Move to the elasticsearch directory
cd ~/elasticsearch

# Create docker-compose.yml and .env files
# Copy the contents from above for each machine

# Set permissions
sudo chown -R 1000:1000 data
```

2. Set system configuration:
```bash
# Set vm.max_map_count
sudo sysctl -w vm.max_map_count=262144
# Make it permanent
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf

# Verify the setting
cat /proc/sys/vm/max_map_count
```

3. Check connectivity between nodes:
```bash
# Run this on each machine to test connectivity
for ip in 192.168.1.26 192.168.1.27 192.168.1.28; do
    echo "Testing $ip..."
    ping -c 2 $ip
    echo "Testing Elasticsearch ports..."
    nc -zv $ip 9200
    nc -zv $ip 9300
done
```

4. Start the cluster:
```bash
# On each machine:
docker-compose down  # If already running
docker-compose up -d
```

5. Monitor the cluster formation:
```bash
# Watch the logs
docker-compose logs -f

# Check cluster health (after about 30-60 seconds)
curl -X GET "localhost:9200/_cluster/health?pretty"

# List nodes in the cluster
curl -X GET "localhost:9200/_cat/nodes?v"
```
