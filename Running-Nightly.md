Running Nightly
---------------
If you're running linux, be sure to setup a cronjob to remove old files that get stored in the database. Be sure to change .csv if you're using json.

Setup crontab -e with the following config (modify to your environment) - this will run vulnwhisperer each night at 0130:

`00 1 * * * /usr/bin/find /opt/vulnwhisp/ -type f -name '*.csv' -ctime +3 -exec rm {} \;` 

`30 1 * * * /usr/local/bin/vuln_whisperer -c /opt/vulnwhisp/configs/example.ini`

Another option is to tell logstash to delete files after they have been processed.

_For windows, you may need to type the full path of the binary in vulnWhisperer located in the bin directory._
