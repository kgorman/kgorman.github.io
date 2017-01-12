---
title: Open source databases; scaling the application stack.
author: kgorman
layout: post
permalink: open-source-databases-scaling-the-application-stack
fave-post_views:
  - 234
categories:
  - PostgreSQL
tags:
  - architecture
  - open source
  - Oracle
  - postgresql
  - scalability
---
After reading Allan Packers excellent blog entry ['are proprietary databases doomed'](https://blogs.oracle.com/allanp/entry/are_proprietary_databases_doomed) , a couple of additional thoughts came to mind. He mentions that open source databases (OSDB's) are becoming "good enough", and explains about how most users don't need or use the vast amount of the features available in the proprietary databases, and thus, OSDB's are becoming more popular. Coming from a proprietary background myself, scaling Oracle at Paypal and then Ebay, I know that side of the business pretty well. In the last year, I have made the professional leap to OSDB's in a big way at Hi5, where we do quite a bit of workload on an OSDB  - [PostgreSQL](http://www.postgresql.org). So Allan's points hit home for me.

<!--more-->

However, I think there is additional context to why the OSDB market is growing. The stack is changing around OSDB's to enable them to make up market segment faster than they normally would. Take any new large scale internet architecture, they are almost [universally](http://highscalability.com/) using OSDB's. Is this trend because OSDB's are getting so good, and copying the feature set of the propriety databases? No, not really. It's because the OSDB software has become \*good enough\*, good enough when we, as software engineers code around many of the features the OSDB's lack (and propriety vendors market as differentiators). In order to scale your company on the internet, you must scale the entire application stack, not just part of your application, hence the adoption of architectures incorporating [splitting](http://www.addsimplicity.com/downloads/eBaySDForum2006-11-29.pdf) and [sharding](http://highscalability.com/unorthodox-approach-database-design-coming-shard>).

So where an OSDB may fall short on feature set or performance, we engineer the shortcoming out of the equation. This approach renders many of the new features of the proprietary databases, well, not so attractive. Take for instance, partitioning; stable and robust in Oracle, and well, not so good in PostgreSQL. Architectures like ours here at Hi5 are designed such that we don't need the database to handle some of these more complex configurations like partitioning. The application is partitioned natively, and all the database engine sees are regular logically partitioned tables. OK, fine, so what about result caching? Nope, we use [memcached](http://en.wikipedia.org/wiki/Memcached) for that. How about  [stored outlines and plan stability](http://www.oracle-base.com/articles/misc/Outlines.php>)? Not needed, we partition our databases so small and simple that plans rarely deviate. Parse storms? Sure we see that problem, but heck the tables are so small (in comparison), the impact is fairly small. What about [online redefinition](http://www.oracle.com/technology/deploy/availability/pdf/HA\_10gR2\_online\_reorg\_twp.pdf)? Forget it, we can "mark down" databases, features, or tables in our application so that the customer does not see much impact. And what is great is, the open source model lends itself to this architecture, since it's free in terms of license costs, we can split and make more databases as needed, and the near zero entry cost allows for rapid innovation and growth. Sure my point is more directed at internet based database usage, and not a more traditional usage, but thats the place where I play.

I think when you look at why OSDB's have gained popularity, one has to additionally consider how they are incorporated into a scalable, robust application stack. I think the idea of engineering your whole stack will continue to grow (at least in the internet space), and perhaps an even larger market share will come with it. This is one of the reasons I made the leap to the open source community, and I am glad I did.
