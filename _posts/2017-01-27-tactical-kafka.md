---
title: Tactical Command Line Kafka
author: kgorman
comments: true
layout: post
image: images/kafkacat_jq.jpg
categories:
- kafka
- streaming data
---

I wanted to share a quick piece on how to publish and subscribe JSON data with Apache Kafka using simple command line utilities. Here at [Eventador](http://www.eventador.io), we use this set of simple command line tools quite frequently. The command line is very useful for both testing and development, but also sampling your stream to understand the data inside it.

<!--more-->

The tools that we mostly use are:

- [kafkacat](https://github.com/edenhill/kafkacat): A command line tool for interacting with Apache Kafka
- [jq](https://stedolan.github.io/jq/): A command line JSON swiss-army knife

## Getting tools installed.

The first step is installing the tools needed. I am going to assume you use OSX for this..

```bash
$ brew install kafkacat jq
```

## Using City Bikes data
For this example, let's say we want to capture the current state of all of the public bikes in Austin, Texas using the [CityBikes](https://api.citybik.es/v2/) Project. Say we want to watch the number of free bikes per station as they get checked in and out.

## Publishing data

To publish data you will first need to get a connect string for your Kafka cluster. In this case, of course, I use [Eventador.io](http://www.eventador.io). Using the Eventador, [console](https://console.eventador.io/deployments) click on the deployment you want to publish to, then click the connections tab. Copy/Paste the connect string under 'Kafka Connections' into your code. You also want to ensure your ACL is open to your workstation, and you have a topic you want to publish to created. There is more info in the [getting started guide](https://eventador.github.io/documentation/getting_started_guide.html) on those things.

The CityBikes API returns JSON, so we simply pass that to kafkacat directly. The JSON has the following structure as returned from the API:

```javascript
{
  "network": {
    "company": [
      "BCycle, LLC"
    ],
    "gbfs_href": "https://gbfs.bcycle.com/bcycle_austin/gbfs.json",
    "href": "/v2/networks/austin",
    "id": "austin",
    "location": {
      "city": "Austin, TX",
      "country": "US",
      "latitude": 30.26408,
      "longitude": -97.74355
    },
    "name": "Austin B-cycle",
    "stations": [
      {
        "empty_slots": 2,
        "extra": {
          "address": "1199 S. Congress Ave.",
          "last_updated": 1485544463,
          "renting": 1,
          "returning": 1,
          "uid": "bcycle_austin_2570"
        },
        "free_bikes": 8,
        "id": "8d9dcd45a7b7d9843a239c0518264484",
        "latitude": 30.25222,
        "longitude": -97.74881,
        "name": "South Congress & Academy",
        "timestamp": "2017-01-27T19:14:43.576000Z"
      }...
    ]
  }
}
```

We gather the data via an API fetch, and stuff the JSON payload into Kafka using kafkacat. In this case we have a topic called 'BikeData' and we are publishing to an endpoint on the Eventador service.

```bash
BROKERS="cxxxxxx-kafka0.pub.va.eventador.io:9092"
TOPIC="BikeData"
curl http://api.citybik.es/v2/networks/austin | kafkacat -P -b $BROKERS -t $TOPIC
```

## Subscribing to the topic

Now that we've published a bit of data, let's subscribe to that topic.

```bash
kafkacat -C -b $BROKERS -t $TOPIC | jq '.network.stations'
```

No surprise, the output looks like below. To cut down on clutter, we are only returning the value of the network.stations array and pretty formatting the output using jq.

```javascript
[
  {
    "empty_slots": 2,
    "extra": {
      "address": "1199 S. Congress Ave.",
      "last_updated": 1485544463,
      "renting": 1,
      "returning": 1,
      "uid": "bcycle_austin_2570"
    },
    "free_bikes": 8,
    "id": "8d9dcd45a7b7d9843a239c0518264484",
    "latitude": 30.25222,
    "longitude": -97.74881,
    "name": "South Congress & Academy",
    "timestamp": "2017-01-27T19:14:43.576000Z"
  },
  {
    "empty_slots": 6,
    "extra": {
      "address": "310 E. 11th St.",
      "last_updated": 1485544463,
      "renting": 1,
      "returning": 1,
      "uid": "bcycle_austin_3291"
    },
    "free_bikes": 6,
    "id": "056491b86e9e64c788c90bd057344340",
    "latitude": 30.27193,
    "longitude": -97.73854,
    "name": "11th & San Jacinto",
    "timestamp": "2017-01-27T19:14:43.589000Z"
  }
  ...
]
```

This gives us a simple view of the data as it comes in. Kafkacat is smart enough to stay running consuming messages as they get published into the topic. Now, let's imagine we want the output to show the latitude/longitude, name, and free_bikes count sorted by the station with the most free_bikes, but only the top 3 results. Can we do all that with jq?

```bash
 kafkacat -C -b $BROKERS -t $TOPIC | jq '[.network | .stations|=sort_by(-.free_bikes) | .stations[] | {name, free_bikes, latitude, longitude}] | .[0:3]'
```

and the output is:

```javascript
[
  {
    "name": "City Hall / Lavaca & 2nd",
    "free_bikes": 16,
    "latitude": 30.26476,
    "longitude": -97.74678
  },
  {
    "name": "Long Center @ South 1st & Riverside",
    "free_bikes": 12,
    "latitude": 30.25941,
    "longitude": -97.74971
  },
  {
    "name": "5th & Bowie",
    "free_bikes": 11,
    "latitude": 30.2696,
    "longitude": -97.75332
  }
]
```

## A couple of final notes about the tools

Kafkacat is simply what it sounds like, a mechanism to `cat` information into Kafka. The beauty of the tool is it's ubiquity and ease of use. It's fantastically useful when you don't actually know the exact structure or values of the data in your Kafka topic.

jq is simply a JSON processor, so if you aren't putting JSON into your Kafka stream, well, you wasted your time reading this. Sorry. However, if you are using JSON then jq is an extremely powerful tool for processing that data and making sense of the structure and data traveling over your Kafka stream. It should also be noted that Kafka doesn't care about the JSON schema itself, it's just a string. The important part is the jq tool parses the string into usable JSON.

*** In a real example it would be much more powerful to have a continuous stream of data coming off the bike sharing equipment vs polling an API periodically, but hey, this is just sample stuff right?
