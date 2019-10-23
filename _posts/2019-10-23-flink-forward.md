---
title: Flink Forward Berlin Recap
author: kgorman
comments: true
layout: post
featured_image: /images/kg_flink_forward_berlin.png
featured_image_credit_line: photo by Ververica GmBH
categories:
- flink
- kafka
- flinkforward
---

I got back from Berlin for the 2019 Flink Forward conference, and thought I would share some thoughts. It was a great conference, and it's amazing to see the growth in the Flink community.

<!--more-->

The big announcement from Stephan Ewen at Ververica was [Stateful Functions](https://statefun.io/) - which is a framework for building coordinated functions that maintain state in a Function-as-a-Service model. It will be interesting to see how this development gets integrated back into Flink going forward, there is already talk on the email lists as to such.

A theme that was omni-present at the conference is how many companies are building or have built self service platforms using Flink. The force driving this trend is the need to democratize data around an organization with a useful framework for productivity. Yelp, Pinterest, Salesforce, Netflix have built internal platforms. I was excited to speak on the panel with some of these companies talking about the differences and lessons learned moderated by Robert Metzger and Konstantin Knauf.

Here is the video from the panel:

<iframe width="560" height="315" src="https://www.youtube.com/embed/-rGAGedttEQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

I was also excited to see so much traction for the Flink SQL API and general excitement around using SQL to process streams of data. Many of the self-service platforms described above use SQL as a first class citizen. This makes sense as the entire point of these platforms is to help more people in an organization make use of streaming data. Writing jobs in Java/Scala that utilize the Flink API are powerful and rich, but clearly take more time and care than hacking out some SQL. Obviously, with SQLStreamBuilder we believe quite a bit in this philosophy and it was great validation to see so many folks talking about using SQL and Flink. Here is my talk on the motivations and experiences building SQLStreamBuilder:

<iframe width="560" height="315" src="https://www.youtube.com/embed/wAfCxGxPUxU" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

All in all it was a great conference. We got to chat streaming data for two solid days, check out the vendor area, listen to talks from a set of amazing speakers and soak in just a little bit of the German culture. In particular I thought these sessions where particularly good:

- [One SQL to Rule Them All - Fabian Hueske](https://youtu.be/uJtqGkIxGhc)
- [Building a Self-Service Streaming Platform at Pinterest - Bairos Novak](https://youtu.be/3-X6FJ5JS4E)
- [Intelligent Log Analysis and Real-time Anomaly Detection @ Salesforce - Andrew Torson](https://youtu.be/D4kk1JM8Kcg)
- [Airbus makes more of the sky with Flink - Jesse Anderson & Hassene Ben Salem](https://youtu.be/sYlbD_OoHhs)
- [State Unlocked - Seth Wiesman & Tzu-Li (Gordon) Tai](https://youtu.be/-Fq6io_HIDE)

Fixated on not leaving without a sample of local food and drink - Erik and I were able to check out some local beers.

![erik_beer](/images/erik_beer.png)


You can check out some [pictures from the event](https://www.flickr.com/photos/dataartisans/albums/72157711379485738) on Flickr. See you at Flink Forward in San Francisco in the spring!
