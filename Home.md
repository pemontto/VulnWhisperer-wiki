Welcome to the VulnWhisperer wiki!


# FAQ
VulnWhisperer is composed by several pieces of software, which means that in order to solve any possible issues, we need to pinpoint where exactly the process is failing.

## VulnWhisperer
* **[FAIL] Could not login to Nessus/Qualys?**

If your password includes special characters, it might be possible that you need to escape that character within the password itself so that the scanner processes it and connects properly. Only do this if you are getting this issue, as each scanner has its own way of dealing with the connection and has its own dependencies.

* **[Qualys] Stuck at "QualysGuard Username: "?**

This could be because the wrong qualysapi dependency it is installed; VulnWhisperer works with qualysapi version 3.5.0, fork created by Austin Taylor and copied into the VulnWhisperer in order to avoid extra external dependencies. 

* **[OLDER VERSIONS] Why are you using a fork of qualysapi library?**

We do use our own fork of the qualysapi library as it is with the one working, the latest release from qualysapi has some changes that break authentication with Qualys and requires manual interaction.

The dependency working is the version 3.5.0, forked by Austin Taylor and copied into the VulnWhisperer project in order to avoid more external dependencies. 

Ignore the qualysapi=4.1.0 in requirements.txt, as this is because 4.1.0 is the only existent official release from qualysapi, already breaking VulnWhisperer if depending only on that one.
* **[Qualys VM] I have locked my account due to too many bad attempts?**

The account connection done in the Qualys Vulnerability Management module is managed by the qualysapi library dependency. We have had issues in the past in which the qualysapi was not correctly escaping symbols (e.g. $) or didn't work with the manager account but it did with a test read-only account. You might need to escape the symbols characters with a backslash.
* **[Qualys VM] I'm trying to run Qualys VM module but I get `[FAIL] ERROR: no such child: count`?**

You might be having enabled in your config file the [qualys] module (Qualys Web Scanner) instead of the [qualys_vuln] (Qualys Vulnerability Management).
## ELK Stack
* **[Kibana's Dashboard] Unable to fetch mapping. Do you have indices matching the pattern?**

Troubleshooting this issue can be found [here](https://github.com/austin-taylor/VulnWhisperer/issues/83#issuecomment-406283999).
* **[Kibana's Dashboard] Dashboard 'No results found'?**

Kibana has its own `time picker` on top right, which by default has selected the last 15 minutes; this usually brings confusion making it seem that data failed to be feeded to ElasticSearch/Kibana. Change the time window (e.g to last 30 days) to check if it is a time window issue.
* **[Kibana] Failing to find indexes (e.g. "risk_score") but reports ingested fine and visible at Kibana?**

Make sure you are using the correct version of ELK, as 6 is not supported yet. This issue might be due to a configuration issue on the logstash config file, you might need to enable/uncomment certain lines.
## Docker/docker-compose
* **Is there a VulnWhisperer Dockerfile?**

The official VulnWhisperer Dockerfile is maintained at [HASecuritySolutions/docker_vulnwhisperer](https://github.com/HASecuritySolutions/docker_vulnwhisperer). This one though at the moment is outdated and the currently supported is still at [qmontal/docker_vulnwhisperer](https://github.com/qmontal/docker_vulnwhisperer)'s fork, to be merged.
* **[docker-compose][vulnwhisperer downloaded data] I followed all the steps to deploy the docker compose, but I am getting in Kibana a “Index Patterns: Please specify a default index pattern” error?**

If you have a different paths for the scans data, you must be aware that the paths from the config files are pointing inside of the docker container, not your local host. This might be an issue when trying to point data from another folder.