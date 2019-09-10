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
Standard logstash 7.2.0 container again configured with files in this repo. 

Prior to starting logstash, it was required to load the systemtest_policy.json into elasticsearch:

`curl -XPUT -d @configs/elasticsearch/systemtest_policy.json -H 'Content-Type: application/json' localhost:9200/_ilm/policy/systemtest_policy` 

Once that policy was added, logstash started normally.

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

### dashboards

When installing the dashboards I had to override a few settings to point them in the right direction of the data
(now sitting in index pattern `systemtest-*`)

```
setup.dashboards.enabled: true
setup.dashboards.index: systemtest-*
```

Adding these as well as the necessary kibana configuration metricbeat was able to load the necessary dashboards into 
kibana and they were then viewable. 

#### Manual loading of the dashboards
In certain situations, it may be preferred to not have every single beat loading dashboard configuration at startup as
this slows down the beats startup, and also necessitates the beats having elastic and kibana credentials stored in 
some form.  

TODO - Proving of manual dashboard loading

## Journalbeat 

TODO

## Filebeat 

TODO

# Findings
The ILM rollovers etc. are handled the simplest through logstash (as it is what is creating the index).  
But need to find a way to load project specific ILM policies ahead of time so that logstash has the policy
ready to apply when creating.  Still needs some thought. 