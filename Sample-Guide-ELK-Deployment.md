Install Requirements-ELK Node **\*SAMPLE\***
--------------------
The following instructions should be utilized as a **Sample Guide** in the absence of an existing ELK Cluster/Node. This will cover a Debian example install guide of a stand-alone node of Elasticsearch & Kibana.

While Logstash is included in this install guide, it it recommended that a seperate host pulling the VulnWhisperer data is utilized with Logstash to ship the data to the Elasticsearch node.

*Please note there is a docker-compose.yml available as well.*

**Debian:** *(https://www.elastic.co/guide/en/elasticsearch/reference/5.6/deb.html)*
```shell
sudo apt-get install -y default-jre
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
sudo apt-get install apt-transport-https
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
sudo apt-get update && sudo apt-get install elasticsearch kibana logstash
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
sudo /bin/systemctl enable kibana.service
sudo /bin/systemctl enable logstash.service
```

**Elasticsearch & Kibana Sample Config Notes** 

Utilizing your favorite text editor:
*   Grab your host IP and change the IP of your /etc/elasticsearch/elasticsearch.yml file. (This defaults to 'localhost')
*   Validate Elasticsearch is set to run on port 9200 (Default)
*   Grab your host IP and change the IP of your /etc/kibana/kibana.yml file. (This defaults to 'localhost') *Validate that Kibana is pointing to the correct Elasticsearch IP (This was set in the previous step)*
*   Validate Kibana is set to run on port 5601 (Default)

*Start elasticsearch and validate they are running/communicating with one another:* 
```shell
sudo service elasticsearch start
sudo service kibana start
```
OR

```shell
sudo systemctl start elasticsearch.service
sudo systemctl start kibana.service
```

**Logstash Sample Config Notes**

*   Copy/Move the Logstash .conf files from */VulnWhisperer/logstash/* to */etc/logstash/conf.d/*
*   Validate the Logstash.conf files *input* contains the correct location of VulnWhisper Scans in the *input.file.path* directory identified below:
```
input {
  file {
    path => "/opt/vulnwhisperer/nessus/**/*"
    start_position => "beginning"
    tags => "nessus"
    type => "nessus"
  }
}
```
*   Validate the Logstash.conf files *output* contains the correct Elasticsearch IP set during the previous step above: (This will default to localhost)
```
output {
  if "nessus" in [tags] or [type] == "nessus" {
    #stdout { codec => rubydebug }
    elasticsearch {
      hosts => [ "localhost:9200" ]
      index => "logstash-vulnwhisperer-%{+YYYY.MM}"
    }
  }
```
*   Validate logstash has the correct file permissions to read the location of the VulnWhisperer Scans

Once configured run Logstash: (Running Logstash as a service will pick up all the files in */etc/logstash/conf.d/* If you would like to run only one logstash file please reference the command below):

Logstash as a service:
```shell
sudo service logstash start
```
*OR*
```shell
sudo systemctl start logstash.service
```
Single Logstash file:
```shell
sudo /usr/share/logstash/bin/logstash --path.settings /etc/logstash/ -f /etc/logstash/conf.d/1000_nessus_process_file.conf
```
