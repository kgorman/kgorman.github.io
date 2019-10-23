---
title: PostgreSQL + Ansible one liners
author: kgorman
comments: true
layout: post
featured_image: /images/matthew-smith-5935.jpg
categories:
- postgresql
- ansible
---
In 2015 I [gave a talk](https://github.com/kgorman/presentations/blob/master/2015/ansible_for_database_management/abstract.md) at PGConf Silicon Valley on the topic of using Ansible and PostgreSQL together. I had a great time. But I never posted anything from the talk. These tricks and patterns are still something I use today. So I thought I would share.

<!--more-->

So I figured I would post the one-liners section of the talk to capture some of these tricks here. But first, a quick overview for context.

## Some context

Ansible provides automation for infrastructure and services. It works nicely with modern cloud architecture, and gives you great power and control over vast arrays of resources with a simple scripted design pattern. Some points to understand:

- Ansible != Docker / Kubernetes
- Ansible != Terraform
- Ansible ~= Puppet / Chef

If you aren't already familiar with Ansible, here's a primer. This post assumes you already are setup to use Ansible to manage your database infrastructure.

## The one-liners

Ok, with that in mind, you can see how using Ansible to manage your database, specifically PostgreSQL database infrastucture could be awesome. Why type individual commands when you can script actions across your entire fleet of databases? Hint: Don't. Just use Ansible.

#### Are my machines up?

```bash
$ ansible us-east-prod -m ping -i hosts.ini -o -f 2
```

#### Is PostgreSQL up and responding?

Simply just check to make sure PG is responding as it should be.

```bash
$ ansible all -i hosts.ini \
-b --become-user=postgresql \
-a "psql -c 'select version;' postgres" -o
```

#### Are some of my key indexes there on all machines?

If my really important index isn't there, then build it in the background.

```bash
$ ansible all -i hosts.ini \
-b --become-user=postgresql \
-a "psql -c 'create index concurrently mybigindex on mybigtable(foo);' postgres" -o
```

#### Are the machines getting even amounts of load or is something broken?

Check the load on all my hosts to see if something is out of whack with one of them. This works great when you have sharded databases.

```bash
$ ansible all -i hosts.ini -a "uptime"
```

#### Ok, let's find the slow queries.

Let's check ensure we have the latest version of our DBA .sql scripts on each host, then use one of those scripts to see the slow queries.

First let's ensure we have the lastest version of the scripts locally.
```bash
$ ansible all -i hosts.ini -m git \
-a "repo=git://foo.example.org/dba_repo.git dest=/home/postgres/scripts/ version=HEAD"
```

The script we want to run looks like this:
```sql
-- top 10 slowest queries
SELECT username, waiting, query_start, query
FROM pg_stat_activity
ORDER BY 3 ASC
LIMIT 10;
```

Now let's run it.

```bash
$ ansible all -i hosts.ini \
-b --become-user=postgresql \
-a "psql postgres -f /home/postgres/scripts/top_10_slow.sql" -o
```
