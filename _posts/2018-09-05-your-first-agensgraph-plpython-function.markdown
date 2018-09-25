---
layout: post
title:  "Let's write your first PL/Python function on AgensGraph!"
date:   2018-09-05 15:28:44 +0900
categories: jekyll update
---
As a first post of this blog, I will show you how to write a simple user-defined function using PL/Python on AgensGraph.

PL/Python module of AgensGraph is based on the PL/Python procedural language of PostgreSQL, and it allows you to create customized functions inside of AgensGraph. it only supports `Python2` at this point, so I encourage blog readers to install `python2.7 or above` if you only have python3 on your machine.

PL/python is only available as an "untursted" language, meaning it does not offer any way of restricting what users can do in it and is therefore name plpythonu. Hence the writer of a function in PL/Python must take care that the function cannot be used to do anything unwatned, since it will be able to do anything that could be done by a user logged in as the database administrator. Only superusers can create functions in untrusted languages such as plpythonu.

Before we go further, please make sure you enable the module by typing `CREATE LANGUAGE plpythonu`. 

{% highlight python %}
CREATE OR REPLACE FUNCTION get_ip_family(ip_str jsonb)
RETURNS int
AS $$
#Python source code starts
import ipaddress

ip_value = ip_str.replace('\"','')
return ipaddress.ip_address(unicode(ip_value)).version
#Python source code ends
$$ LANGUAGE plpythonu
#=> CREATE (a:ip {value:'192.168.0.28'});
#=> CREATE (a:ip {value:'2001:0db8:85a3:0000:0000:8a2e:0370:7334'});
#=> MATCH (a:ip) RETURN a.value,get_ip_family(a.value);
#=> prints '4' for IPv4Address '192.168.0.28'. 
#=> prints '6' for IPv6Address '2001:0db8:85a3:0000:0000:8a2e:0370:7334'.
{% endhighlight %}

- `CREATE OR REPLACE` indicates the system to create new user-defined function or replace exiting function with same name and input parameters.
- `get_ip_family(ip_str text)` indicates the system a name of the function and what types of parameters will be given by users.
- `RETURNS int` indicates the system what type the function will return. types can be one of PostgreSQL supported types.
- `AS $$ ... $$ LANGUAGE plpythonu` indicates the system what language this function will use. You can write your own Python codes in the block.

Check out the [agensgraph docs][agensgraph-docs] for more info on how to get the most out of AgensGraph. File all bugs/feature requests at [AgensGraph’s GitHub repo][agensgraph-github]. 

[agensgraph-docs]: https://bitnine.net/documentation
[agensgraph-github]:   https://github.com/bitnine-oss/agensgraph
