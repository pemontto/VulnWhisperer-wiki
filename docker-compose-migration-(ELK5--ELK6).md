If you have been using the ELK5 docker-compose file, and you want to move to the ELK6 versions, there are some things that you need to take into account:
- Make sure you have the latest version of Docker CE (currently `18.09.1`, tested with 18.06.1 and there are docker issues)
- You CAN'T just spin up ELK6 version and expect it to work: VulnWhisperer docker-compose saves all the data of ElasticSearch in a Docker volume, this includes the `.kibana` index (served by ES), the ElasticSearch's license and all the VulnWhisperer data structured with the ELK5 version, which will break when trying to load it in ELK6.
- There are two options, either:
  - You delete the old volume with the ELK5 results, and spin up the new ELK6 docker-compose, allowing VulnWhisperer to recover all the files from the scanners and submit them again to ElasticSearch.
  - You follow up the official upgrade guide from ELK5 to ELK6 ***while using** ELK5 docker-compose*! Once done the whole migration, the next time you spin up the docker-compose, you should be able to do it with the ELK6 version without problems. 

**Notice we do not support ELK issues, as it is not directly related to VulnWhisperer.**

The volume used in both docker-compose is the same `esdata1`, which is why if you already had ELK5 data it will break. This volume is locally saved at `/var/lib/docker/volumes/vulnwhisperer_esdata1/`, and in order to get rid of it, you need to run the following command: ```docker volume prune -f```.