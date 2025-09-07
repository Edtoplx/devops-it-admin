# Create Docker Network
```
sudo docker network create \
  --driver=bridge \
  --subnet=172.20.88.0/24 \
  --ip-range=172.20.88.0/24 \
  --gateway=172.20.88.1 \
 docker-network-name
```