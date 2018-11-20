---
layout: post
title:  "Use cypher with your plpython#2"
date:   2018-11-20 18:21:00 +0900
categories: jekyll update
---
In this post, we will use plpython function to prepare a statement and execute the plan.

You can use `plpy.prepare` to make a plan and `plpy.execute` to execute the plan with given data.

{% highlight python %}
CREATE OR REPLACE FUNCTION cypher_test2()
RETURNS text
AS $$
#Python source code starts
#Assume that you have a vertex with label, called "test" and property, called text
#=> CREATE (:test {id:1,text:'hello'}) CREATE (:test {id:2,text:'test'}) CREATE (:test {id:3,text:'world'})
plan = plpy.prepare("MATCH (a:test) WHERE a.id=$1 RETURN a.text AS sample",['int'])
records = plpy.execute(plan,[2])

return records[0]['sample']
#Python source code ends
$$ LANGUAGE plpythonu
#=> prints 'test' as a result 
{% endhighlight %}

- `plpy.prepare` prepares the execution plan for a given query.
- `plpy.execute` executes given queries and returns the result of query.
- `return` returns the value that you indicate.

Check out the [agensgraph docs][agensgraph-docs] for more info on how to get the most out of AgensGraph. File all bugs/feature requests at [AgensGraph’s GitHub repo][agensgraph-github]. 

[agensgraph-docs]: https://bitnine.net/documentation
[agensgraph-github]:   https://github.com/bitnine-oss/agensgraph
