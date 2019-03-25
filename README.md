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

```
curl -L https://bootstrap.saltstack.com -o install_salt.sh
```

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

## Create required folders

```
mkdir -p /srv/salt/{package,user,file,directory,service}
```

## Create main configuration file
```
sudo vi /srv/salt/top.sls
```
```
---
base:
  '*':
    - package
    - user
    - directory
  'node*’:
    - file
  'G@os:ubuntu':
    - service
```

## Create package manifest
```
sudo vi /srv/salt/package/init.sls
```
```
---
install rsync package:
  pkg.installed:
    - name: rsync
```

## Create user manifest

```
sudo vi /srv/salt/user/init.sls
```
```
---
create nice user:
  user.present:
    - name: nice
    - shell: /bin/bash
    - home: /home/nice
    - groups:
      - sudo
```

## Create directory manifest
```
sudo vi /srv/salt/directory/init.sls
```
```
---
create simple directory:
 file.directory:
   - name: /opt/simple_directory
   - user: root
   - group: root
   - mode: 755
```

## Create file manifest
```
sudo vi /srv/salt/file/init.sls
```
```
---
create very nice file:
  file.managed:
    - name: /opt/simple_directory/nice.txt
    - contents: |
        Welcome to very nice file
        #saltstackworkshops
```

## Create service manifest
```
sudo vi /srv/salt/service/init.sls
```
```
---
make sure ssh service is running:
  service.running:
    - name: ssh
    - enable: True
```

## Testing single state
```
sudo salt 'node01' state.apply package test=True
sudo salt 'node01' state.apply user test=True
sudo salt 'node01' state.apply directory test=True
sudo salt 'node01' state.apply service test=True
sudo salt 'node01' state.apply file test=True
```
## Testing all states (highstate)
```
sudo salt 'node01' state.apply test=True
sudo salt 'node01' state.highstate test=True
```

## Apply single state
```
sudo salt 'node01' state.apply package queue=True
sudo salt 'node01' state.apply user queue=True
sudo salt 'node01' state.apply directory queue=True
sudo salt 'node01' state.apply service queue=True
sudo salt 'node01' state.apply file queue=True
```

## Apply all states (highstate)
```
sudo salt 'node01' state.apply queue=True
sudo salt 'node01' state.highstate queue=True
```

## Create jinja manifest
```
sudo vi /srv/salt/jinja/init.sls
```
```
# Jinja2 Conditionals
{%- if grains['os_family'] == 'RedHat' %}
  {%- set package  = 'httpd' %}
{%- elif grains['os_family'] == 'Debian' %}
 {%- set package  = 'apache2' %}
{%- endif %}

install {{ package }} server:
  pkg.installed:
    - name: {{ package }}

# Jinja2 Loops
{%- for usr in ['moe','larry','curly'] %}
create user {{ usr }}:
 user.present:
    - name: nice
{%- endfor %}
```

## Testing single jinja state
```
sudo salt 'node01' state.apply jinja test=True
```

## Apply single jinja state
```
sudo salt 'node01' state.apply jinja queue=True
```

## Cleanup salt environment in docker

```
docker-compose –f docker-salt-cluster-ubuntu.yaml down -v
```
