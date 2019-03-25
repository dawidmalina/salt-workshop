# salt-workshop

## Install docker
### on Ubuntu
https://docs.docker.com/install/linux/docker-ce/ubuntu/
### on Fedora
https://docs.docker.com/install/linux/docker-ce/fedora/
### on CentOS
https://docs.docker.com/install/linux/docker-ce/centos/
## Install docker-compose
https://docs.docker.com/compose/install/

## Validate installation
### docker

```
docker --version
```

### docker-compose

```
docker-compose --version
```

## Download compose manifest

```
curl –LO https://raw.githubusercontent.com/dawidmalina/salt-workshop/master/docker-salt-cluster-ubuntu.yaml
```

## Run salt environment in docker

```
docker-compose –f docker-salt-cluster-ubuntu.yaml up -d
```

## Login (exec) into
### master node

```
docker exec -it salt bash
```

### minion node01

```
docker exec -it node01 bash
```

### minion node02

```
docker exec -it node02 bash
```

## Install dependencies

```
sudo apt-get update
sudo apt-get install -y curl gnupg-agent iputils-ping
```

## Download salt bootstrap shell script
curl -L https://bootstrap.saltstack.com -o install_salt.sh

## Install salt master

```
sudo sh install_salt.sh -P -M
sudo service salt-master status
```

## Install salt minion

```
sudo sh install_salt.sh -P
sudo service salt-minion status
```

## View all keys

```
sudo salt-key --list-all
```

## Accept a specific key

```
salt-key --accept=<key>
```

## Accept all keys

```
sudo salt-key --accept-all
```

## Cleanup salt environment in docker

```
docker-compose –f docker-salt-cluster-ubuntu.yaml down -v
```
