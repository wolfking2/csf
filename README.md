# ArmourBird CSF - Container Security Framework [![Follow ArmourBird on Twitter](https://img.shields.io/twitter/follow/armourbird.svg?style=social&label=Follow%20%40armourbird)](https://twitter.com/intent/user?screen_name=armourbird "Follow ArmourBird on Twitter")

## Table of Contents
<!--ts-->
 1. [About](#about)
 2. [Architecture Diagram](#architecture-diagram)
 3. [APIs-CSF Server](#apis-csf-server)
 4. [Installation/Usage](#installationusage)
 5. [Building Docker Images](#building-docker-images)
 6. [Sneak Peak](#sneak-peak)
 7. [To Do](#to-do)
 8. [Website](#website)
 9. [Twitter](#twitter)
 10. [References](#references)
<!--te-->

## About

**ArmourBird CSF - Container Security Framework** is an extensible, modular, API-first framework build for regular security monitoring of docker installations and containers against CIS and other custom security checks. 

ArmourBird CSF has a client-server architecture and is thus divided into two components:

a) **CSF Client**  

-   This component is responsible for monitoring the docker installations, containers, and images on target machines
-   In the initial release, it will be checking against Docker CIS benchmark 
-   The checks in the CSF client will be configurable and thus will be expanded in future releases and updates
- It has been build on top of Docker bench for security

b) **CSF Server**  

-   This will be the receiver agent for the security logs generated by the various distributed CSF clients (installed on multiple physical/virtual machines)
-   This will also have a UI sub-component for unified management and dashboard-ing of the various vulnerabilities/issues logged by the CSF Clients
-   This server will also expose APIs that can be used for integrating with other systems

**Important Note**: The tool is currently in beta mode. Hence the debug flag of django (CSF Server) is enabled and the SQLite is used as DB in the same docker container. Hence, spinning up a new docker container will reset the database.

## Architecture Diagram
![ ArmourBird CSF Architecture Diagram](https://public.adobecc.com/files/1AANSG3RSNQAEE5BO2IENIJKAXFFFF)

## APIs CSF Server

**Issue APIs**

POST /issues 
-   For reporting issues from CSF clients

GET /issues/{issueId}
-   For listing specific issue with {id}

GET /issues
-   For listing all issues reported by all CSF clients

PUT /issues/{issueId}
-   For updating a specific issue (like for severity, comments, etc.)

DELETE /issues/{issueId}
-   For deleting specific issue

**Client APIs**

POST /clients
-   For adding a CSF client

GET /clients/{clientId}
-   For listing specific CSF client

GET /clients/
-   For listing all the CSF clients

PUT /clients/{clientId}
-   For updating the CSF client (for e.g. IP addr, etc.)

DELETE /clients/{clientId}
-   For deleting a CSF client from the network

**Client Group APIs**

POST /clientGroup
-   Adding client to a specific group (for e.g. product1, HRNetwork, product2, etc.)

GET /clientGroup/{groupID}
-   For listing client group details

GET /clientGroup/
-   For listing all client groups

PUT /clientGroup/{groupID}
-   For updating client group

DELETE /clientGroup/{groupId}
-   For deleting client group

## Installation/Usage

**CSF client** run as a docker container on the compute instances running docker installation.  It can be executed using the following command using the docker image hosted on hub.docker.com:

```
docker run -it --net host --pid host --userns host --cap-add audit_control \
-e DOCKER_CONTENT_TRUST=$DOCKER_CONTENT_TRUST \
-e CSF_CDN='<TO-UPDATE>' \
-v /etc:/etc \
-v /usr/bin/docker-containerd:/usr/bin/docker-containerd \
-v /usr/bin/docker-runc:/usr/bin/docker-runc \
-v /usr/lib/systemd:/usr/lib/systemd \
-v /var/lib:/var/lib \
-v /var/run/docker.sock:/var/run/docker.sock \
--label csf_client \
-d armourbird/csf_client
```
Make sure to update CSF_CDN environment variable in the above command with the CSF server URL. Once the container is executed, it will start sending issue logs to the CSF server on constant intervals.

**CSF server** can run as a docker container or natively on a web server on which various CSF clients will be sending data. You can run it on your server using the following command using the docker image hosted on hub.docker.com

```
docker run -p 80:8000 -d armourbird/csf_server
```

Browse the CSF server via the following links

 - Dashboard: http://< your-domain >/dashboard/
 - APIs: http://< your-domain >/api/



## Building Docker Images
Building docker image for CSF Client
```
git clone git@github.com:armourbird/csf.git
cd csf_client
docker build . -t csf_client
```
Building docker image for CSF Server
```
git clone git@github.com:armourbird/csf.git
cd csf_server
docker build . -t csf_server
```

## Sneak Peak
**Dashboard**
<img src="https://public.adobecc.com/files/1AE4TWJJN2VZWGHNMP21FDFYSXIFFF" width="100%">

**API View**
<img src="https://public.adobecc.com/files/1AAXTW0OBT05YB4DKYFG0ATW1ZKFFF" width="100%">

## To Do
- [ ] Integrate non-development web server and database
- [ ] Add API key support to CSF Server
- [ ] Add further checks in CSF Client
- [ ] Add create/update/delete functionality for Client Groups
- [ ] Add update/delete functionality for Clients
- [ ] Improve dashboard UI

## Website
https://www.armourbird.com/

## Twitter
[http://twitter.com/ArmourBird](http://twitter.com/ArmourBird)

## References
[https://www.cisecurity.org/cis-benchmarks](https://www.cisecurity.org/cis-benchmarks)
[https://github.com/docker/docker-bench-security](https://github.com/docker/docker-bench-security)
