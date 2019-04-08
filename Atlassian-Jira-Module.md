This is the wiki page for the VulnWhisperer Jira Module.

## Scanner Support
The module works with the following scanners:
* Nessus
* Qualys Vulnerability Management

## Module functionalities
* create new tickets with new vulnerabilities
* update existing tickets' assets
* comment each asset change on the ticket
* allow tracking of "risk_accepted" assets
* group all assets by team/jira project
* close tickets that have been solved
* reopen closed tickets still unresolved
* close obsolete tickets to get rid of old system's duplicates

## Jira Workflow

![Jira Workflow](https://github.com/HASecuritySolutions/VulnWhisperer/blob/beta-1.8/docs/source/jira_workflow.png)

## Module command
`vuln_whisperer -c configs/frameworks_example.ini -s jira --source qualys_vuln -n "scan test"`

## Dependencies
The module is able to do the sync by using:
* Jira ticket title (Vulnerability name)
* Jira ticket labels, which are:
    * scanner
    * scan_name
    * vulnerability (referring to a confirmed vulnerability coming from scanner)
    * vulnerability_management (referring to ticket being opened by the vulnerability management system)

## Module Workflow
The workflow takes place over two VulnWhisperer files:
### vulnwhisp.py
- checks the local database for scanner.scan_name combinations
- checks that provided config file has configuration for each scan
    - if not, creates configuration for missing scan (in this stage, VulnWhisperer exits as it needs config file to be filled)
- processes the data from the last results file downloaded from the scanner.scan_name
    - in this step, it discards by criticality the vulnerability that are lower than the minimum criticality specified on the configuration file 

### jira_api.py
Main function is sync(), which takes the vulnerability and Jira data for finish the processing.

* tickets that are older than 6 months with the label **vulnerability_management** are closed for hygiene and control
* download all the Jira tickets with the tags {source}, {scan_name}, vulnerability and vulnerability management
* for each vulnerability:
    * check if the ticket exists and if assets are the same
        * if ticket exists and assets are the same, its considered a duplicated and makes sure the detected ticket is open
        * if ticket exists but assets are not the same, it marks the vulnerability to update
    * if needs update, checks if assets are new vulnerable assets or assets in ticket no longer vulnerable, and updates the ticket accordingly with the new set of vulnerable assets and comments each change of deleted and added assets
    * if ticket does not exist, it creates a new ticket with the vulnerability and assets vulnerable to the specified queue
    * cross checks all the tickets with the vulnerabilities to be reported, and those tickets which vulnerability no longer appears in the scan are closed, as it means the vulnerability was solved. Accordingly comments on the ticket.

## Exceptions
The module ignores tickets that have been closed that contain the labels either **risk_accepted** or **server_decomission**. This is so that teams can close tickets if they are not going to fix it and stop being bothered, but also allow the tracking of those issue for doing **Risk Assessment**.

## Jira Sync structure
The jira sync function requires the vulnerabilities to be sent as a list and have assets grouped by vulnerability. Each vulnerability needs the following fields:
* source -> scanner which data is being parsed
* scan_name -> specific scan_name being parsed
* title -> vulnerability name
* diagnosis -> vulnerability description
* consequence -> threat that the vulnerability involves
* solution -> solution to the specific vulnerability
* ips -> assets affected
* risk -> low, medium, high, critical
* references -> any other references for the users to check

All of these fields will come from the scanner results themselves, it is just needed to have them sent following this structure, so that data is already parsed when it arrives to the module, and the module strictly takes care for the Jira related tasks.

## Extras
By matching with the scan_name, we can track down all the tickets that were reported from said scan even though they might have moved from Jira queue.

Automatically updated tickets will also have added a "update" label to the ticket.

Tickets are being opened as type "Bug", which is [one of the default issue types from Jira](https://confluence.atlassian.com/adminjiracloud/issue-types-844500742.html).

If vulnerable assets are too many, instead of posting all of them in the ticket description, a file is attached with all of the assets.

*dns_resolv* is a variable on the Jira configuration, that allows you to enable the host resolution of the vulnerable assets using the VulnWhisperer host DNS for those assets without detected hostname.