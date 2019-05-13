---
layout: post
title:  "clauset-moore-newman example with AgensGraph"
date:   2019-05-13 18:21:00 +0900
categories: jekyll update
---
In this post, we will use plpython function to write clauset-newman-moore clustering algorithm.(https://bit.ly/2YmS6Dp)

{% highlight python %}
CREATE OR REPLACE FUNCTION clauset_newman_moore_script()
RETURNS void as $$
import time
import networkx as nx
import psycopg2 as ag
from networkx.algorithms.community import greedy_modularity_communities
import community

def check_table_existence():
   res = plpy.execute("SELECT relname FROM pg_stat_all_tables WHERE relname='clauset_newman_moore_temp'").nrows()
   if (res == 0):
      plpy.execute("CREATE TABLE public.clauset_newman_moore_temp (cluster_id integer, nodes text[], primary key (cluster_id))")
   else:
      plpy.execute("TRUNCATE TABLE public.clauset_newman_moore_temp")

def doQuery(qry):
   try:
      conn = ag.connect("host=127.0.0.1 port=5555 dbname=test user=test password=test")
      cur = conn.cursor()
      cur.execute("SET GRAPH_PATH TO test")

      cur.execute(qry)
      records = cur.fetchall()

      G.add_edges_from([tuple(r) for r in records])
   except Exception, e:
      plpy.error(e)
   finally:
      conn.commit()
      cur.close()
      conn.close()

check_table_existence()

start = time.time()

G = nx.Graph()

query = " MATCH (a)-[r]->(b) RETURN a.value AS node, b.value AS neighbor"

doQuery(query)

partition = greedy_modularity_communities(G)

plan = plpy.prepare("INSERT INTO public.clauset_newman_moore_temp VALUES ($1,$2)",["int","text[]"])


cluster = 0

for node_list in tuple(sorted(c) for c in partition):
    cluster = cluster+1
    plpy.execute(plan,[cluster,node_list])
    #plpy.info(cluster,node_list)


done = time.time()
elapsed = done - start

plpy.info("Time Elapsed: "+str(elapsed)
$$ language plpythonu;
{% endhighlight %}

- `plpy.prepare` prepares the execution plan for a given query.
- `plpy.execute` executes given queries and returns the result of query.
- `plpy.info` prints out given parameters to the console.

Check out the [agensgraph docs][agensgraph-docs] for more info on how to get the most out of AgensGraph. File all bugs/feature requests at [AgensGraph’s GitHub repo][agensgraph-github]. 

[agensgraph-docs]: https://bitnine.net/documentation
[agensgraph-github]:   https://github.com/bitnine-oss/agensgraph
