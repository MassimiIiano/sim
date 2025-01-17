# SIM - Security in Mind
SIM is a software to detect vulnerable devices in the local network.

# How to set up a vulnerable environment

We recommend using [VulnHub](https://github.com/vulhub/vulhub) to setup a vulnerable environment. 

VulnHub is a project that collects several `Dockerfiles` that expose vulnerable services, especially:
* Outdated and vulnerable versions of web servers or databases
* Misconfigured systems that expose vulnerabilities

## Multiple machines vs single machines

The user that wants to test and assess *SIM*, could think what is better between using a single machine or many machines:
* If the purpose of testing is checking if a vulnerability is catched, then, running multiple docker containers from VulnHub is totally acceptable, because under the hood vulscan will detect all the vulnerable software
* If the purpose of testing is chekcing if a vulnerability is cathed on muliple machines, the only way is to having many machines running these docker images and connected to the same network of the machine running SIM

## Example

If a vulnerable version of *nginx* is to be tested, a user could use [this](https://github.com/vulhub/vulhub/tree/master/nginx/CVE-2013-4547) to execute a docker machine running it.

So:
1. Clone the git repo `git clone https://github.com/vulhub/vulhub`
2. Move to the right folder `cd nginx/CVE-2013-4547`
3. Execute docker compose `docker compose up`
