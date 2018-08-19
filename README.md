# elk-logmonitor

# Application logs monitoring with ELK stack

## Part 1 - Introduction

ELK is a versatile tool that allows you to collect logs from different sourses, 
parse them, and eventually, group and analyze.

In the simplest variant this stack consist of 3 elements:

### 1) Logstash - component that parses incoming data and passes it on to another tool.

Since logs can come in many different formats (mostly containing some jsons, timestamps, 
plain text messages of upredicatable length in random order)
and from variable sources (files, RabbitMQ queues and many others),
Logstash is the place where you can collect your logs, parse each type of it separately 
and extract meaningful data from it.
Logstash configuration consists of three main steps: 
- **input definition** (ie. file path), 
- **log processing** (extracting and naming fields like timestamp, error level, source and so on),
- **output definition** (you can define many at the same time - for instance, Elasticsearch and stdout)

### 2) Elasticsearch - kind of NoSQL database, that stores proceesed logs as JSONs and indexes them, 
### so you can aggregate end explore your data efficeiently.

For basic usage, almost no work needs to be done - Elasticsearch will do the magic 
withiout you even noticing.

The most important notions in Elasticsearch are:
**document** - in our case, one log, stored as JSON
**index** - collection of documents (they can be of many types)
**type** - additional field in a document, which allows you to group documents within an index;
documents of the same type should have more or less the same structure,
 but technically they don't have to.

 Elasticsearch provides you with a REST API, but, as said before, we will bearly use it.

 ### 3) Kibana - graphic interface for Elasticsearch, which allows you to visualize end explore the data

## Part 2 - Logstash configuration

Let's have a log example:
 
```
2018-08-14 22:52:37 > ERROR > Something went terribly wrong > {"client_id":"234893","uid":"Izm6fa63a857ada67d343a956a23dc44","referrer":"https://www.whatever.com/index.html"}
```

Our particular interest is how many errors from a given client do we have ATM, 
how it changes in time, and which kind of an error mostly occures.

First of all, we need to have ELK installed.
There are many ways to do this, widely described in the Internet, 
the most clean is to use a docker image:
https://elk-docker.readthedocs.io/#forwarding-logs-filebeat

For personal usage I have installed it directly using this tut (ommiting most of it):
https://www.rosehosting.com/blog/install-and-configure-the-elk-stack-on-ubuntu-16-04/

The addvantage of this approach is that I have ELK up and running whenever I open my laptop
(it can be a drawback for some people though).

Oh, and let me warn you - you're gonna need Oracle Java JDK to run ELK and it is also quite RAM consuming.

Having logstash installed, let's go to its directory and create a config file, let's call it simple.conf
The first one can be:
```
input {
  stdin {}
}
output {
  stdout {}
}
```

It tells Logstash to read a message from a command line, do the standard things
(add some default fields and wrap it all in a JSON format)
 and print it back to the cmd.

 You can try it out by running
```
 bin/logstash -f simple.conf
 ```
