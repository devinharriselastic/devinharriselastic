# ECE Fundamentals On-Demand 2.7.0
- Start 9/8/2021
- Created lab account through Google Sign-in `devin.harris@elastic.co`
- **Strigo access URL** `lymkglhewd8rdtkho-t2dd9wifgdpnqbwyj.labs.strigo.io`
  - **Lab Credentials**: `training`, `nonprodpwd` 
## 1. Elastic Cloud Enterprise Architecture
- ECE can be deployed anywhere including bare metal hardware, all services are containerized
  - Allows us to manage a large number of ElasticSearch Clusters
  - Maximizes HW utilization
### Architecture
![Architecture](ece-architecture.png)
- Service Oriented
  - Roles: 
    - Proxy: Handles user requests, keeps track of state & availability, helps with scaling
      - Load Balancer: Proxies are generally set up behind a Load balancer
    - Allocator: Run all the instances where ElasticSEarch and Kibana nodes. Allocators create new containers and start them when needed.
    - Coordinator: 
      - Constructor: Scheduler - deterimines what changes need to be made and writes to Zookeeper. Also assigns nodes to the proper Allocator
      - Cloud UI
      - API
    - Director: Manages the distributed data store
  - Runners: "supervisors" assigned one or more roles
- Containerized (Docker)
- Deployment state coordination using Zookeeper
- Easy Access through Cloud UI
### Lab 1 - Elastic Cloud Enterprise Architecture
- ECE requires Ubuntu, Red Hat, Centos or SUSE to run
- This lab walked through a highly technical setup of ECE including lots of Linux configuration changes
  - I'm not sure how relevant this is for me, so I'm asking in my [original issue](https://github.com/elastic/infosec/issues/7433#issuecomment-915308822)
  -
## Lesson 2: Elastic Cloud Enterprise Interfaces
- Installing: `bash <(curl -fsSL https://download.elastic.co/cloud/elastic-cloud-enterprise.sh) install`
  - Downloads and runs Elastic Install Script
  - Downloads and installs several Elastic Stack Packs by default
  - Displays output with details for administering the new install including:
    - username
    - password
    - tokens
    - etc...
  - We can tour the cloud UI from the output, there are two default users created
    1. `admin`
    2. `readonly`
### ECE Cloud UI
#### Deployments
- 3 by default
  - admin console
    - backs the cloud UI 
  - logging and metrics
    - collects logs and performance metrics 
  - security cluster 
    - user and role management for cloud UI and SSO for deployments are handled here
- From the deployments view we can see key information about the deployments
- Each deployment has an overview page that provides key information about that deployment including information about the ElasticSearch and Kibana deployments
### Platform
- Contains summary information
- Allocators information
- Runners information
- Proxies information
- Contructors information
- Elastic Stack information
  - By default ECE comes with two MAJOR version of elastic stack available 
- Templates information 
- Repositories information
- Settings information
- Security information
### Activity Feed
- This page shows recent activity as well as activity that has happened on clusters over past 24 hours
### Lab 2 - Elastic Cloud Enterprise Interfaces
- Got install finished up, placed output into a text file on machine `elastic-output.txt` Key values:
  - Cloud UI: `http://172.31.43.230:12400`, `https://172.31.43.230:12443`
  - Admin username: `admin`
  - Password: `uOGegrHOLilxhJ0dLfIDNyvuDQuVjlTjitw2OSlwTBI`
  - Read-only username: `readonly`
  - Password: `kKKxbutM8EK1gp0h1gf9GTmrYk9OnwMtvvtbLKJYIOT`
- Accessed web UI via `https://ec2-3-69-54-234.eu-central-1.compute.amazonaws.com:12443`
- Created `my_cluster` as described in the tutorial
  - username: `elastic`
  - password: `EKLbgimnv7U3TLbAcUJXvHli` 
  - ElasticSearch Endpoint: `https://8cc2dbed135d46da9c20a52b9d3d5441.172.31.43.230.ip.es.io:9243`
  - ElasticSearc CloudID: `my_cluster:MTcyLjMxLjQzLjIzMC5pcC5lcy5pbzo5MjQzJDhjYzJkYmVkMTM1ZDQ2ZGE5YzIwYTUyYjlkM2Q1NDQxJDc4NWNmNWI3OTdkZDRmMWU4NmZkYTdkNTlhMzJhZTA2`
  - Zone `ece-zone-1`
- Doing bulk blog load: `curl -X POST -k -u elastic -H 'Content-Type: application/x-ndjson' https://8cc2dbed135d46da9c20a52b9d3d5441.172.31.43.230.ip.es.io:9243/blogs-000001/_doc/_bulk --data-binary @/home/elastic/blogs_bulk.json`
- Launched Kibana as described in step 15 and found the same problem, updated IP address to AWS public IP
  - having issues with updating in the webUI I keep getting `Internal Server Error`
  - HOWEVER I did find I can open Kibana here `https://785cf5b797dd4f1e86fda7d59a32ae06.3.69.54.234.ip.es.io:9243/` 
- Successfully ran kibana query
  - Found out that each individual action has to be run i.e. when `POST ....` and `GET...` both in console, each has to have the run button pressed seperate from one another. 
## Lesson 3: Elastic Cloud Enterprise Features
- By default Elastic Cloud provisions 1GB of RAM for every 32 GB of storage
  - Decreasing the ratio for RAM:Storage with use of High Performance SSDs increases our performance for search intensive workloads
    - go from 1:32 to 1:16 or even 1:8
  - Increasing the ratio of RAM:Storage can be more efficient for workloads like logging
    - go from 1:32 to 1:48 or 1:96
- Upgrading can easily be done by the Web UI, it's easy to add new versions
- Snapshots - allow us to backup Elasticsearch indicies and can allow us to recover from failure.
  - A repository must be configured to support snapshots
  - After configuring a snapshot repository, a snapshot is taken every 30 minutes (or at user set intreval)
  - The following repositories are supported:
    - Amazon S3
    - Microsoft Azure
    - Google Cloud Storage
- By default, ECE collects monitoring data using Filebeat and Metricbeat and data is collected from every runner
  - We can also monitor deployments through Kibana using the Elastic Stack monitoring feature.
- ECE has built-in security features:
  - IP Filtering
  - Identity authorization services for SAML, LDAP, and Elasticsearch keystore
- ECE also supports features that are part of the Elastic Stack.
  - Encryption at rest is not configured "out of the box", hosts need to be configured for disk-level encryption using a tool like `dm-crypt`
    - **NOTE**: Snapshot repositories also need to be configured for encryption
### Lab 3: Elastic Cloud Enterprise Features
