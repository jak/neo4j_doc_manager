![](https://travis-ci.org/neo4j-contrib/neo4j_doc_manager.svg)

# Overview

The [Neo4j](http://neo4j.com/) Doc Manager takes MongoDB documents and makes it easy to query them for relationships by making  them available in a Neo4j graph structure, following the format specified by [Mongo Connector](https://github.com/10gen-labs/mongo-connector).  It is intended for live one-way syncronization from MongoDB to Neo4j, where you have both databases running and take advantage of each databases' strength in your application (polyglot persistance).

![](https://raw.githubusercontent.com/neo4j-contrib/neo4j_doc_manager/master/docs/resources/images/mongo_connector_2.gif)

# Note

The software in this repository is provided AS IS, with no guarantees of any kind.

# Installing

You must have Python installed in order to use this project. Python 3 is recommended.

First, install neo4j_doc_manager with pip:

```
pip install neo4j-doc-manager
```

(You might need sudo privileges).

Refer to  [this document](https://github.com/neo4j-contrib/neo4j_doc_manager/blob/master/docs/neo4j_doc_manager_doc.adoc#21-setup) for more information if you experience any difficulties installing with pip. 

## Using Neo4j Doc Manager

Ensure that you have a Neo4j instance up and running. If you have authentication enabled (version 2.2+) for Neo4j, be sure to set the enviroment variables for **NEO4J_AUTH** containing your username and password as well as **NEO4J_URI** containing your host and port.

```
export NEO4J_AUTH=user:password
export NEO4J_URI=http://localhost:7474/
```

Ensure that mongo is running a *replica set*. To initiate a replica set start mongo with:

```
mongod --replSet myDevReplSet
```


Then open [**mongo-shell**](http://docs.mongodb.org/master/tutorial/getting-started-with-the-mongo-shell/) and run:

```
rs.initiate()
```

Please refer to [Mongo Connector FAQ](https://github.com/10gen-labs/mongo-connector/wiki/FAQ) for more information. 


Start the mongo-connector service with the following command:

```
mongo-connector -m localhost:27017 -t http://localhost:7474/db/data -d neo4j_doc_manager
```

**-m** provides Mongo endpoint
**-t** provides Neo4j endpoint. Be sure to specify the protocol (http).
**-d** specifies Neo4j Doc Manager.


# Data synchronization

With the `neo4j_doc_manager` service running, any documents inserted into mongo will be converted into a graph structure and immediately inserted into Neo4j as well. Neo4j Doc Manager will turn keys into graph nodes. Nested values on each key will become properties.

To see this in action, consider the following document:

~~~
{
  "session": {
    "title": "12 Years of Spring: An Open Source Journey",
    "abstract": "Spring emerged as a core open source project in early 2003 and evolved to a broad portfolio of open source projects up until 2015."
  },
  "topics":  ["keynote", "spring"],
  "room": "Auditorium",
  "timeslot": "Wed 29th, 09:30-10:30",
  "speaker": {
    "name": "Juergen Hoeller",
    "bio": "Juergen Hoeller is co-founder of the Spring Framework open source project.",
    "twitter": "https://twitter.com/springjuergen",
    "picture": "http://www.springio.net/wp-content/uploads/2014/11/juergen_hoeller-220x220.jpeg"
  }
}
~~~


Insert the following document into mongo using the [mongo-shell](http://docs.mongodb.org/master/tutorial/getting-started-with-the-mongo-shell/):

~~~
db.talks.insert(  { "session": { "title": "12 Years of Spring: An Open Source Journey", "abstract": "Spring emerged as a core open source project in early 2003 and evolved to a broad portfolio of open source projects up until 2015." }, "topics":  ["keynote", "spring"], "room": "Auditorium", "timeslot": "Wed 29th, 09:30-10:30", "speaker": { "name": "Juergen Hoeller", "bio": "Juergen Hoeller is co-founder of the Spring Framework open source project.", "twitter": "https://twitter.com/springjuergen", "picture": "http://www.springio.net/wp-content/uploads/2014/11/juergen_hoeller-220x220.jpeg" } } );
~~~

This document will be converted to a graph structure and immediately inserted into Neo4j:

![](https://raw.githubusercontent.com/neo4j-contrib/neo4j_doc_manager/master/docs/resources/images/graph1.png)

Refer to [this document](https://github.com/neo4j-contrib/neo4j_doc_manager/blob/master/docs/neo4j_doc_manager_doc.adoc) for more information and examples.

## Resources

* [Full Documentation](http://neo4j.com/developer/neo4j-doc-manager/)
* [Neo4j Doc Manager Getting Started](http://neo4j.com/developer/mongodb/)
* [Neo4j Doc Manager: Polyglot Persistence for MongoDB and Neo4j](http://neo4j.com/blog/neo4j-doc-manager-polyglot-persistence-mongodb/) (blog post)
