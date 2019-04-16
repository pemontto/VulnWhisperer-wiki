
## Local development

VulnWhisperer now has a test class to intercept and mock API calls from the Nessus, Tenable and Qualys Vuln frameworks. It includes example scan data taken from Nessus, Tenable and Qualys Vuln scans of vulnweb.com.

It adds two cli flags `--mock` and `--mock_dir` to enable mocking API endpoints, with all test data currently stored in /test.

The best way to run VulnWhisperer in development by installing it in develop mode
```shell
python setup.py develop
```

The tests require you to have downloaded the test files from `VulnWhisperer-test` repository, which is mapped as a submodule into the `VulnWhisperer` repo under `test` folder. In order to initialize the submodule, you need to run:
`git submodule init && git submodule update`

From your vulnwhisperer repo directory you can just run `vuln_whisperer -c configs/test.ini --mock` otherwise you can specify a directory with your example data with `vuln_whisperer -c configs/test.ini --mock --mock_dir /path_to_vulnwhisperer/test`; the `configs/test.ini` contains paths to `/tmp` folder.

Two tests are also integrated with Travis CI.

## Mock tests with docker compose
This currently requires you build you own local docker image tagged as `vulnwhisperer-local`. If you wish to use the docker hub version, in `docker-compose-test.yml` just replace the line  
`image: vulnwhisperer-local`
with
`image: hasecuritysolutions/vulnwhisperer:latest`.

To run the mock test suite with docker compose follow these steps:
```shell
git clone https://github.com/HASecuritySolutions/VulnWhisperer
cd VulnWhisperer
git submodule init && git submodule update
# Build the vulnwhisperer-local image
docker build -t vulnwhisperer-local .
# Ensure other environments are down
docker-compose -f docker-compose.v6.yml down
docker-compose -f docker-compose-test.yml down
# Run the mock test docker compose
docker-compose -f docker-compose-test.yml up
```

Scan the log messages for any errors and after a while if you should be able to browse to the [VulnWhisperer - Reporting dashboard](http://localhost:5601/app/kibana#/dashboard/72051530-448e-11e7-a818-f5f80dfc3590?_g=(refreshInterval%3A(pause%3A!t%2Cvalue%3A0)%2Ctime%3A(from%3A'2019-03-01T01%3A00%3A14.380Z'%2Cmode%3Aabsolute%2Cto%3A'2019-04-15T22%3A00%3A00.000Z'))&_a=(description%3A''%2Cfilters%3A!()%2CfullScreenMode%3A!f%2Coptions%3A(darkTheme%3A!f%2CuseMargins%3A!f)%2Cpanels%3A!((embeddableConfig%3A(vis%3A(legendOpen%3A!f))%2CgridData%3A(h%3A20%2Ci%3A'5'%2Cw%3A24%2Cx%3A0%2Cy%3A56)%2Cid%3A'2f979030-44b9-11e7-a818-f5f80dfc3590'%2CpanelIndex%3A'5'%2Ctype%3Avisualization%2Cversion%3A'6.4.3')%2C(gridData%3A(h%3A20%2Ci%3A'12'%2Cw%3A24%2Cx%3A0%2Cy%3A36)%2Cid%3A'8d9592d0-44ec-11e7-a05f-d9719b331a27'%2CpanelIndex%3A'12'%2Ctype%3Avisualization%2Cversion%3A'6.4.3')%2C(gridData%3A(h%3A20%2Ci%3A'14'%2Cw%3A24%2Cx%3A24%2Cy%3A16)%2Cid%3A'67d432e0-44ec-11e7-a05f-d9719b331a27'%2CpanelIndex%3A'14'%2Ctype%3Avisualization%2Cversion%3A'6.4.3')%2C(embeddableConfig%3A(vis%3A(params%3A(sort%3A(columnIndex%3A!n%2Cdirection%3A!n))))%2CgridData%3A(h%3A20%2Ci%3A'15'%2Cw%3A12%2Cx%3A36%2Cy%3A36)%2Cid%3A'297df800-3f7e-11e7-bd24-6903e3283192'%2CpanelIndex%3A'15'%2Ctype%3Avisualization%2Cversion%3A'6.4.3')%2C(embeddableConfig%3A(vis%3A(params%3A(sort%3A(columnIndex%3A!n%2Cdirection%3A!n))))%2CgridData%3A(h%3A20%2Ci%3A'20'%2Cw%3A12%2Cx%3A24%2Cy%3A36)%2Cid%3A'471a3580-3f6b-11e7-88e7-df1abe6547fb'%2CpanelIndex%3A'20'%2Ctype%3Avisualization%2Cversion%3A'6.4.3')%2C(embeddableConfig%3A(vis%3A(params%3A(sort%3A(columnIndex%3A!n%2Cdirection%3A!n))))%2CgridData%3A(h%3A15%2Ci%3A'22'%2Cw%3A8%2Cx%3A40%2Cy%3A0)%2Cid%3A'995e2280-3df3-11e7-a44e-c79ca8efb780'%2CpanelIndex%3A'22'%2Ctype%3Avisualization%2Cversion%3A'6.4.3')%2C(gridData%3A(h%3A20%2Ci%3A'29'%2Cw%3A24%2Cx%3A0%2Cy%3A16)%2Cid%3A'479deab0-8a39-11e7-a58a-9bfcb3761a3d'%2CpanelIndex%3A'29'%2Ctype%3Avisualization%2Cversion%3A'6.4.3')%2C(embeddableConfig%3A(vis%3A(defaultColors%3A('0%2520-%252050'%3A'rgb(247%2C252%2C245)'%2C'50%2520-%2520100'%3A'rgb(0%2C68%2C27)')%2ClegendOpen%3A!f))%2CgridData%3A(h%3A16%2Ci%3A'30'%2Cw%3A10%2Cx%3A30%2Cy%3A0)%2Cid%3Ae6b5b920-f77a-11e8-8f42-af2e41422cf8%2CpanelIndex%3A'30'%2Ctype%3Avisualization%2Cversion%3A'6.4.3')%2C(embeddableConfig%3A(vis%3A(colors%3A('0%2520-%252010'%3A%2523EAB839)%2CdefaultColors%3A('0%2520-%252010'%3A'rgb(8%2C48%2C107)')%2ClegendOpen%3A!f))%2CgridData%3A(h%3A16%2Ci%3A'31'%2Cw%3A9%2Cx%3A21%2Cy%3A0)%2Cid%3A'61b43c00-f77b-11e8-8f42-af2e41422cf8'%2CpanelIndex%3A'31'%2Ctype%3Avisualization%2Cversion%3A'6.4.3')%2C(embeddableConfig%3A(vis%3A(colors%3A('10%2520-%252020'%3A%2523890F02)%2CdefaultColors%3A('0%2520-%252010'%3A'rgb(255%2C245%2C240)'%2C'10%2520-%252020'%3A'rgb(103%2C0%2C13)')%2ClegendOpen%3A!f))%2CgridData%3A(h%3A16%2Ci%3A'32'%2Cw%3A11%2Cx%3A0%2Cy%3A0)%2Cid%3A'8c9c9430-f77b-11e8-8f42-af2e41422cf8'%2CpanelIndex%3A'32'%2Ctype%3Avisualization%2Cversion%3A'6.4.3')%2C(embeddableConfig%3A()%2CgridData%3A(h%3A15%2Ci%3A'33'%2Cw%3A10%2Cx%3A11%2Cy%3A0)%2Cid%3Ac533c120-fe8c-11e8-8f42-af2e41422cf8%2CpanelIndex%3A'33'%2Ctype%3Avisualization%2Cversion%3A'6.4.3'))%2Cquery%3A(language%3Alucene%2Cquery%3A(match_all%3A()))%2CtimeRestore%3A!t%2Ctitle%3A'VulnWhisperer%2520-%2520Reporting'%2CviewMode%3Aview))