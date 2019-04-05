VulnWhisperer now has a test class to intercept and mock API calls from the Nessus, Tenable and Qualys Vuln frameworks. It includes example scan data taken from Nessus, Tenable and Qualys Vuln scans of vulnweb.com.

It adds two cli flags `--mock` and `--mock_dir` to enable mocking API endpoints, with all test data currently stored in /test.

The tests require you to have downloaded the test files from `VulnWhisperer-test` repository, which is mapped as a submodule into the `VulnWhisperer` repo under `test` folder. In order to initialize the submodule, you need to run:
`git submodule init && git submodule update`

From your vulnwhisperer repo directory you can just run `vuln_whisperer -c configs/test.ini --mock` otherwise you can specify a directory with your example data with `vuln_whisperer -c configs/test.ini --mock --mock_dir /path_to_vulnwhisperer/test`; the `configs/test.ini` contains paths to `/tmp` folder.

Two tests are also integrated with Travis CI.