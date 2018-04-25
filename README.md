# WORK IN PROGRESS

# Apache Atlas v2 Rest API

Data governance is an important paradigm especially for the department which handles data. A data governance helps answer questions like:
* Which sources are feeding data ?
* What is the data schema of these sources ?
* Which process reads the data and how is it transformed ?
* When was the data last updated ?
* Can we classify the data as privacy, public etc ?
* Based on classification can we know which users and processes are accessing private data ?
...

Data governance is thus knowing such metadata of data and then taking appropriate actions. Actions could be simple as answering the above questionnaires or complex like defining access policy on confidential data set, so that only specific users/groups can view it.

You could say ,yeah we are doing this already by via wiki, excel and word docs. That’s a start but such doc fails to give a complete end to end pic. Such medium if not updated and continuously contributed, would be out of synch of what is happening with your data in your environment.

## Apache Atlas

Enter Apache Atlas. it is a data governance tool ,which facilitates in gathering metadata information of data and processes and maintaining it. Unlike excel and wiki docs, it has functioning components which can monitor your process , data -store, files and updates its metadata repository. 

It provides functionality such as notifying systems if there is a entities get updated with new columns or dropped. In combination with Apache ranger, it can be used to define access policies for users and processes.

Apache Atlas is not just for hadoop environment, it could be integrated in other environments too. However certain functionalities could be limited.

## Overview
In this write up, my plan is not so much to explain the architecture,solution etc of Apache atlas. For this I would rather that you visit the Apache atlas documentation at Apache Atlas 0.8 doc

What I would like to do in this write up is to walk you thru the process of defining metadata in Apache atlas using the REST API v2.

I am doing this, as there is currently very minimal documentation/example on the format of the request json. For this write up, I am using atlas v0.8.

## Hypothetical scenario

The following is a very crude simple scenario of a data ingestion process.

A source system (news site scraper) uploads a csv data file to your landing zone. You have a python process that read the file , formats it to a json format and publishes the record to Kafka topic. A streaming process consumes the message from the topic , enriches with some data and stores into a data (Hbase).

![Image of Flow](https://github.com/venkatra/atlas-v2/blob/master/image_1.jpg)

In the example, it’s assumed that atlas hook for storm, Hbase is not configured. I am not going to show actual code of these artifacts (python script, storm code etc..); I am demonstrating what you would need to do define these into Atlas.

## Basics
For the purpose of this write up I am using the rest v2 endpoints.The documentation for rest API endpoint is at: Apache REST API v2

From the apache doc: A ‘Type’ in Atlas is a definition of how a particular type of metadata objects are stored and accessed. A type represents one or a collection of attributes that define the properties for the metadata object. 

From a developers view; a type is analogous to a class definition. Example

Type : Kafka_topic |
------------------ |
Attributes: |
Broker |
Topic name |
Topic configuration |
Key schema |
Value schema |

Type : Data_File |
------------------ |
Attributes: |
File name pattern |
Directory |
Server |
Format |
Data schema |

An entity is an instance of a Type, analogy is object from the oops world.

Type : Kafka_topic : new_topic

Attributes | |
----------- | |
Broker | analytics_topics_broker |
Topic name | news_topic |
Topic configuration |
Key schema | Id , String |
Value schema | Id ,String |
Url | String |
Headline | String |

Refer to the above concepts ![TypeSystem doc]https://atlas.apache.org/0.8.0-incubating/TypeSystem.html

## Interacting with Atlas
For the solutions that we are defining in atlas; we are going to be defining the following:
Type definition 
- Node:
  * File
  * Python script
  * Kafka topic with schema definition
- Entities:
  * DataFile
  * Kafka topic
  * Hbase table
- Process:
  * Python script
  * Storm topology


