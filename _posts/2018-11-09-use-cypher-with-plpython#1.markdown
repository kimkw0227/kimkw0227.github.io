---
layout: post
title:  "Use cypher with your plpython#1"
date:   2018-11-09 11:33:00 +0900
categories: jekyll update
---
In this post, we will use plpython function to execute a cypher query.

Like you used in PostgreSQL, you can simply use cypher syntaxes with `plpy.execute`.

{% highlight python %}
CREATE OR REPLACE FUNCTION cypher_test()
RETURNS void
AS $$
#Python source code starts
#Assume that you have a vertex with label test and property text
records = plpy.execute("MATCH (a:test) RETURN a.text AS sample")

plpy.info(records[0]['sample'])
#Python source code ends
$$ LANGUAGE plpythonu
#=> prints 'hello' as a result 
{% endhighlight %}

- `plpy.execute` executes given queries and returns the result of query.
- `plpy.info` can print anything that happens in your plpython script.

Check out the [agensgraph docs][agensgraph-docs] for more info on how to get the most out of AgensGraph. File all bugs/feature requests at [AgensGraph’s GitHub repo][agensgraph-github]. 

[agensgraph-docs]: https://bitnine.net/documentation
[agensgraph-github]:   https://github.com/bitnine-oss/agensgraph
