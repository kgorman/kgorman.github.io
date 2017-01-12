---
title: Python and cx_oracle
author: kgorman
layout: post
permalink: python-and-cx-oracle
fave-post_views:
  - 31
categories:
  - Python
tags:
  - Oracle
  - Python
---
I decided to start futzing with Python and Oracle. I generally use perl for most everything, and sometimes php for web based things, but I wanted to try Python at the urging of colleagues. I downloaded the source from [here](http://www.python.org/download/) and added in cx_Oracle from [here](http://sourceforge.net/projects/cx-oracle/). The installation on linux was about as smooth as possible. I installed the source @ ~oracle/python so I could keep a user space version of Python. This is similar to how I generally install perl. So I created my first application using Python, ```select * from dual;```. No big deal, but wow, Python is a pretty straightforward language. Here are some resources for Oracle based code:  
- [IOUG Paper](http://www.ioug.org/python.pdf)
- [The Oracle Docs](http://www.python.net/crew/atuining/cx_Oracle/html/cx_Oracle.html)

The docs and code examples are pretty slim for Oracle, but I supposed that to be expected as the language and it's use with Oracle expands over time. Here is an example of the hello world app: 

```python
#!/home/kgorman/python/bin/python
import cx_Oracle
handle = cx_Oracle.connect('scott/tiger@test')
curs = handle.cursor()
sql = """ select * from dual """
curs.execute(sql)
row = curs.fetchone()
print row[0]
curs.close()
```
