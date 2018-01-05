# Network Operator

Network Operator is an orchestrator based on micrservice architecture. Featuring complete CI/CD, API, revision controlled configurations, and zero touch deployments. 

## Getting Started

Quickstart
Download network-operator from the release section and save to your path.

### Prerequisites

```
Docker CE
Client:
 Version:      17.10.0-ce
 API version:  1.32 (downgraded from 1.33)
 Go version:   go1.8.3
 Git commit:   f4ffd25
 Built:        Tue Oct 17 19:04:16 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.09.0-ce
 API version:  1.32 (minimum version 1.12)
 Go version:   go1.8.3
 Git commit:   afdb6d4
 Built:        Tue Sep 26 22:45:38 2017
 OS/Arch:      linux/amd64
 Experimental: true
```
Once docker is installed, you can configure the host with the following

```
vrctl install
vrctl run
```


### Advanced configuration

Advanced configurations can be done with a YAML file saved to /.shared/vrctl.yaml

```
# Required and must be unique across the system for the auto updater to work the way its set up currently
NodeName: vrouter
# Leave blank to get IP from NSOT, or set static but no checking currently in place to prevent duplicate IPs
PrivateIP: 
Ui: true
DEBUG: false
DebugViz: false
DebugWatch: false
LOGFILE: /.shared/logs/vrctl.log
# All the ETCd variables are configured to use the ITC3 ETCd cluster, only override them if you have another cluster to connect to
EtcdApiVersion: 3
EtcdKey: /.shared/certs/etcd.key
EtcdEndpoint: https://etcd.itc3.io:2379
EtcdCert: /.shared/certs/etcd.pem
EtcdCaCert: /.shared/certs/ca.pem
# The following enable, or disable the service container, and configured for the defaults currenly
Gobgp: true
# Define bgp peer type - accepted core; edge 
# Used to select the ASN
Type: core
NodeExporter: false 
Nat: false
Cloud: false
Tinc: true
Evpn: false  
Core: true
Auth: false
Seed: false
Netmap: false
Prtg: false
Netdata: false
Benchsec: false
Artifacts: false
Cadvisor: false
Teleport: true
TeleportRoles: proxy,node
Snmp: true

########################################################
# Keycloak proxy configuration
# 
########################################################
Keyauth: false
KeyauthUpstream: http://localhost:19999
KeyauthProxyURL: http://localhost:19999
KeyauthRealm: itc3
KeyauthRealmPublicKey: 
KeyauthProxyDiscoveryUrl: https://key.itc3.io/auth/realms/itc3
KeyauthProxyClientID: itc3-auth-broker
KeyauthProxyClientSecret: 
KeyauthProxyEncryptionKey: 
KeyauthProxyListen: 0.0.0.0:443

########################################################
# VizServer Configurations
# 
########################################################
# Enables Vizceral metrics collection and shipping to ETCd
VizCollector: true
# customer, gce-useast-4, aws-useast-1, aws-useast-2
Region: customer
# Define VizServer status, which when enabled host the vrctlviz web app.
VizServer: false
Oauth: false
OauthUpstreamUrl: "http://localhost:19998"
OauthListenPort: "19990"
OauthRedirectUrl: "https://viz.itc3.io:19990/oauth2/callback"



########################################################
# Logging configurations
# 
########################################################
Logging: true
LogAgg: true
# Es set to current ITC3 cluster
ElasticsearchHost: es.itc3.io
Fluentd: true
FluentdSecret: 12349876!@
FluentdLogAggServer: logagg.itc3.io

########################################################
# Docker host configurations
# 
########################################################
# DockerMemory: 3000M
DockerRestart: "always"
DockerPrivileged: true
DockerLogMaxSize: "10m"
DockerMaxFiles: "3"
DockerVolumesShared: "/.shared"
DockerVolumesTeleport: "/var/lib/teleport"
DockerNet: "host"

########################################################
# Container images
# 
########################################################
DockerImage: gcr.io/itc3-production/network-operator
DockerBranch: master
DockerGc: 
ImageNetdata: netdata:master
ImageNetmap: netmap:master
ImageTinc: tinc:master
ImageGobgp: gobgpd:master
ImageFluentd: fluentd:master
ImageSnmp: snmp:master
ImageCadvisor: cadvisor:master
ImageVizServer: vrctlviz:master
ImageCollector: vrctl-viz:master
ImageOauth: keycloak:master-oauth2-proxy
ImageUi: vrctl-ui:master

########################################################
# BGP daemon Configuration
# Uncomment to apply configurations
########################################################

bgpConfig:
  originate:
    ipv4-10.255.0.0/16:
      - cidr 10.255.0.0/16
      - local-pref 200
      - med 200
      - community 65520:10
    ipv4-10.255.1.0/16:
      - cidr 10.255.1.0/16
      - local-pref 100
      - med 100
      - community 65510:10
   ## Information on flowspec https://github.com/osrg/gobgp/blob/master/docs/sources/flowspec.md
  flowspec:
      
########################################################
# AWS Configuration
# S3 bucket access, KMS encryption, Backups, etc
########################################################
AWS:
  KmsRegion: us-east-2
  # KmsKey: arn:aws:kms:us-east-2:47253456348022:key/gw4r5tg-017f-4a6e-fgsdfr-sdfgxzdfdggsfg
  # KmsAccessKey: AKIAIAKIAIAKIAI
  # KmsSecretKey: nzUjkcDu5nzUjkcDu5nzUjkcDu5nzUjkcDu5nzUjkcDu5
  KmsS3Bucket: itc3-vrctl
  LoggingS3Bucket: itc3-vrctl-logging
  LoggingS3Region: us-east-2
  # LoggingAccessKey: AKIAIAKIAIAKIAI
  # LoggingSecretKey: nzUjkcDu5nzUjkcDu5nzUjkcDu5nzUjkcDu5nzUjkcDu5
  
########################################################
# Legacy Vars for backwards compatability
# Phasing these out, but too much to update at once
########################################################
AUTH: false
ETCDCTL_API: 3
ETCDCTL_ENDPOINTS: https://etcd.itc3.io:2379
ETCDCTL_CERT: /.shared/certs/etcd.pem
ETCDCTL_CACERT: /.shared/certs/ca.pem
ETCDCTL_KEY: /.shared/certs/etcd.key
CORE: true



```

Verify tinc connected

```
vrctl tinc
vrctl tinc nodes
vrctl tinc edges
vrctl tinc subnets
```

Verify BGP

```
vrctl neighbors
vrctl global rib
```
Log files
Docker logs files can be viewed with the following
```
vrctl logs
```

Network Operator log files can be tailed at 
```
tail -f /.shared/logs/vrctl.log
```

## Running the tests

Still need to add testing

### Break down into end to end tests

Explain what these tests test and why

```
Give an example
```

## Deployment

Need to document multiple device config management with ETCD and github

## Built With

* [Go](http://golang.org/) - Go 1.0

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/your/project/tags). 

## Authors

* **Jeremy Pogue** - *Initial work*

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## Acknowledgments

* etcd
* tinc


