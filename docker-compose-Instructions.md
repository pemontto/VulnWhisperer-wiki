The docker-compose provides the instant solution of deploying the whole VulnWhisperer + ELK stack without needing to install anything other than Docker on your server; the structure's purpose is to store locally the data from the scanners, letting VulnWhisperer update the records and Logstash feed them to ElasticSearch, so it requires a local storage folder.

To launch docker-compose, do:
```shell
docker-compose -f docker-compose.yml up
```

The VulnWhisperer container stops after ending the sync; in order to make vulnwhisperer run periodically, add to crontab the following:
```shell
0 8 * * * /usr/bin/docker-compose run vulnwhisp-vulnwhisperer
```

## Dependencies
In order to work as expected, the docker-compose needs:
- The latest version of Dockerfile
- A local storage data folder (stored at ./data)
- A logstash config file (stored at ./docker/logstash.yml)
- ElasticSearch host configuration

### Local Storage "data"
In order to have persistent data storage, a local volume is mapped both in VulnWhisperer and Logstash containers.

The raw docker-compose will run out of the box using the folder ./data. This folder needs to be created, and as it will be manipulated by another user in the containers, it needs permissions for other users to read/write/execute in order to sync:

```shell
mkdir data && chmod -R 666 data 
#data/database/report_tracker.db will need 777 to use with local vulnwhisperer
```
otherwise the users running inside the docker containers will not be able to work with it properly. If you don't apply chmod recursively, it will still work to sync the data, but only root use in localhost will have access to the created data (if you run local vulnwhisperer with the same data will break).
> If you want to change the "data" folder for storing the results, remember to change it from both the docker-compose.yml file and the logstash files that are in the root "docker/" folder.

### Logstash config file
./docker/logstash.yml file will need other read/write permissions in order for logstash container to use the configuration file; youll need to run:
```shell
chmod 666 docker/logstash.yml
```

### ElasticSearch host configuration
ElasticSearch's container requires been allowed to create many memory mapped area's; otherwise starting Elasticsearch will fail. This is done by changing in the host the value vm.max_map_count to a minimum of 262144.
To solve this in Linux:
```shell
sysctl -w vm.max_map_count=262144
```

This is solved in different ways on each OS, for more information please check how to configure it at https://elk-docker.readthedocs.io/#prerequisites.

## Tested Environments
* Ubuntu Server 18.04 LTE
* Debian Stretch
* docker-ce v.18.06

## Issues

* Dockerfile is configured with some standards in order to be able to run out of the box; if you have different configuration (e.g. data folder location, some specific VulnWhisperer command to execute), you might need to review the Dockerfile/docker-compose or ./docker/logstash.yml files.
* Paths in the docker-compose file configuration or ./docker/logstash.yml are referring to paths **INSIDE** the containers, not in the local host.
* Hostnames do NOT allow _ (underscores) on it, if you change the hostname configuration from the docker-compose file and add underscores, config files from logstash will fail.
* If you are having issues with the connection between hosts, to troubleshoot them you can spawn a shell in said host doing the following:
```shell
docker ps #check the images from the containers
docker exec -i -t vulnwhisp-vulnwhisperer /bin/bash
```
You can also make sure that all ELK components are working by doing "curl -i host:9200 (elastic)/ host:5601 (kibana) /host:9600 (logstash). WARNING! It is possible that logstash is not exposing to the external network the port but it does to its internal docker network "esnet".
* If Kibana is not showing the results, check that you are searching on the whole ES range, as by default it shows logs for the last 15 minutes (you can choose up to last 5 years)
* X-Pack has been disabled by default due to the noise, plus being a trial version. You can enable it modifying the docker-compose.yml and docker/logstash.conf files. Logstash.conf contains the default credentials for the X-Pack enabled ES.
* On Logstash container, "/usr/share/logstash/pipeline/" is the default path for pipelines and "/usr/share/logstash/config/" for logstash.yml file, instead of "/etc/logstash/conf.d/" and "/etc/logstash/".
* The vulnwhisperer container inside of docker-compose is using network_mode=host instead of the bridge mode by default; this is due to issues encountered when the container is trying to pull data from your scanners from a different VLAN than the one you currently are. The host network mode uses the DNS and interface from the host itself, fixing those issues, but it breaks the network isolation from the container (this is due to docker creating bridge interfaces to route the traffic, blocking both container's and host's network). If you change this to bridge, you might need to add your DNS to the config in order to resolve internal hostnames.
