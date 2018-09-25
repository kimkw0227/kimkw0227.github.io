---
layout: post
title:  "Let's make your function a bit more secure!"
date:   2018-09-22 11:28:44 +0900
categories: jekyll update
---
In the previous post, I posted how to make a simple function using PL/Python. Like mentioned, PL/python is only available as an "untursted" language, meaning it does not offer any way of restricting what users can do in it and is therefore name plpythonu.

It is unlikely that we let unwanted users to access and use functions, because they are written in `plpythonu`. Therefore, I would like to share some ways to prevent unwanted users from using functions that they are not allowed to. 

The magic keywords are `revoke execute on` and `grant execute on`. You can revoke execution on certain function from all users, then you can grant usage on the funtion to certain user or certain groups. The following example revokes permission from general users and grant only user test2 to use the function `get_ip_family`.

{% highlight sql %}
#=> CREATE USER test1;GRANT ALL ON DATABASE superdb TO test1;
#=> CREATE USER test2;GRANT ALL ON DATABASE superdb TO test2;

REVOKE EXECUTE ON FUNCTION get_ip_family(ip_str jsonb) FROM public;

#=> Access to AgensGraph using user test1
#=> Test Query.1:MATCH (a:ip) RETURN a.value,get_ip_family(a.value);
#=> prints 'ERROR: permission denied for function get_ip_family'

GRANT EXECUTE ON FUNCTION get_ip_family(ip_str jsonb) TO test2;

#=> Access to AgensGraph using user test2
#=> Test Query.2:MATCH (a:ip) RETURN a.value,get_ip_family(a.value);
#=> prints '4'
{% endhighlight %}

Check out the [agensgraph docs][agensgraph-docs] for more info on how to get the most out of AgensGraph. File all bugs/feature requests at [AgensGraph’s GitHub repo][agensgraph-github]. 

[agensgraph-docs]: https://bitnine.net/documentation
[agensgraph-github]:   https://github.com/bitnine-oss/agensgraph
