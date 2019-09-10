# Proof of concept for ILM rotation and setup in a distributed environment

## Problem Statement
Team A support the core stack components (ELK)

Team B,C,D... etc. Support their own projects and need centralised logging.

How can we configure the ELK stack appropriately so that when another team (Y) start
development of their product their instances can be quick enrolled without any input
from team A? 

# ELK Stack

## Elasticsearch
Standard elasticsearch 7.2.0 container configured with files in this repo. 

## Logstash
Standard logstash 7.2.0 container again configured with files as per this repo. 

Prior to starting logstash, it was required to load the systemtest_policy.json into elasticsearch:
`curl -XPUT -d @configs/elasticsearch/systemtest_policy.json -H 'Content-Type: application/json' localhost:9200/_ilm/policy/systemtest_policy` 

Once that policy was added, log**~~~~**
## Kibana
Standard kibana 7.2.0 container again configured with files as per this repo. 


# Beats
Below are details on configuration that was done to the beats running on 
the host system (not containerized, logstash outputs were pointed to `localhost:5044`)

## Metricbeat
Curled and installed using `dpkg -i`: -
https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.2.0-amd64.deb

Enriched metricbeat with the following fields, rest of metricbeat was stock barring
commenting out the elastic output and un-commenting logstash output to mimick business
setup.
```
fields:
  Environment: systemtest
  Component: westys-laptop
  Project: some-wildly-exciting-project
  Code: 0X34

```

## Journalbeat 

TODO

## Filebeat 

TODO