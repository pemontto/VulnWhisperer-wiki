Welcome to the VulnWhisperer wiki!


# FAQ

* **Is there a VulnWhisperer Dockerfile?**

The official VulnWhisperer Dockerfile is maintained at [HASecuritySolutions/docker_vulnwhisperer](https://github.com/HASecuritySolutions/docker_vulnwhisperer). This one though at the moment is outdated and the currently supported is still at [qmontal/docker_vulnwhisperer](https://github.com/qmontal/docker_vulnwhisperer)'s fork, to be merged.
* **Why are you using a fork of qualysapi library?**

We do us our own fork of the qualysapi library as it is with the one we have tested the project and we are sure it is working, we prefer doing that than running into issues because of code changes on the future dependency releases.
* **[Qualys VM] I have locked my account due to too many bad attempts?**

The account connection done in the Qualys Vulnerability Management module is managed by the qualysapi library dependency. We have had issues in the past in which the qualysapi was not correctly escaping symbols (e.g. $) or didn't work with the manager account but it did with a test read-only account. You might need to escape the symbols characters with a backslash.
* **[Kibana's dashboard] Unable to fetch mapping. Do you have indices matching the pattern?**
Troubleshooting this issue can be found [here](https://github.com/austin-taylor/VulnWhisperer/issues/83#issuecomment-406283999)