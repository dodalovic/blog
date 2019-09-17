---
title: Using regular expressions for querying Mongodb documents
date: 2017-04-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - mongodb
  - software development
---
 
Ladies and gents, here’s one fairly short hint for anyone wondering how to query documents in `Mongodb` 
using regular expressions. Let’s get straight to the point:

Let’s start by inserting couple of documents (using `Mongodb` shell) which we’ll use for querying 
afterwards:

{{<highlight javascript>}}
db.developers.insertMany([
    { "name" : "John", "languages" : ["java", "php", "javascript"] },
    { "name" : "Johnny", "languages" : ["java", "c", "c++"] },
    { "name" : "Jim", "languages" : ["node", "java"] }
]);
{{</highlight>}}

Now, when we run the following command in shell:

{{<highlight javascript>}}
db.developers.find()
{{</highlight>}}

Command output confirms all test documents are successfully stored:

{{<highlight json>}}
{
    "_id" : ObjectId("587e6ec738cbd11c2dc46932"),
    "name" : "John",
    "languages" : [
        "java",
        "php",
        "javascript"
    ]
}
{
    "_id" : ObjectId("587e6ec738cbd11c2dc46933"),
    "name" : "Johnny",
    "languages" : [
        "java",
        "c",
        "c++"
    ]
}
{
    "_id" : ObjectId("587e6ec738cbd11c2dc46934"),
    "name" : "Jim",
    "languages" : [
        "node",
        "java"
    ]
}
{{</highlight>}}

Now, say you want to find all documents that have field name starting with some particular value, 
let’s say "Joh". The way to do is pretty straightforward:

{{<highlight javascript>}}
db.developers.find({ name : { $regex : /^Joh.*/ } }).pretty()
{{</highlight>}}

Command output confirms we matched correct document(s):

{{<highlight json>}}
{
    "_id" : ObjectId("587e6ec738cbd11c2dc46932"),
    "name" : "John",
    "languages" : [
        "java",
        "php",
        "javascript"
    ]
}
{
    "_id" : ObjectId("587e6ec738cbd11c2dc46933"),
    "name" : "Johnny",
    "languages" : [
        "java",
        "c",
        "c++"
    ]
}
{{</highlight>}}

We could also try matching documents having some field ending with particular value, let’s say 
"im", just by doing something like:

{{<highlight javascript>}}
db.developers.find({ name : { $regex : /^.*im$/ } }).pretty()
{{</highlight>}}

which would match "Jim" in our case:

{{<highlight json>}}
{
  "_id": ObjectId("587e6ec738cbd11c2dc46934"),
  "name": "Jim",
  "languages": ["node","java"]
}
{{</highlight>}}

Regular expressions are sometimes the only way to go for particular problem sets, so I hope this 
helps understanding mongodb `API`s dealing with regular expressions.

Until next time!