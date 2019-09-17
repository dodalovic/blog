---
title: Querying Mongodb documents for array properties
date: 2017-04-30T13:35:00+01:00
author: Dusan Odalovic
draft: false
toc: false
images:
tags:
  - mongodb
  - software development
  - featured
---

Ladies and gents, I’m just posting one short reminder to myself and anyone keen to find out how do
 we query array type fields in `Mongodb`.

Let’s start by inserting couple of documents (using `Mongodb` shell) which we’ll use for querying 
afterwards:

{{<highlight javascript>}}
db.developers.insertMany([
    { "name" : "John", "languages" : ["java", "php", "javascript"] },
    { "name" : "Jack", "languages" : ["java", "c", "c++"] },
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
    "name" : "Jack",
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

Now, say you want to find all documents that have “java” as the first entry in languages array 
field, we do by using following syntax:

{{<highlight javascript>}}
db.developers.find({"languages.0" : "java"})
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
    "name" : "Jack",
    "languages" : [
        "java",
        "c",
        "c++"
    ]
}
{{</highlight>}}

Array position is indexed as expected starting by zero, so in case we want all docs having “java” 
at second position, we do it like this:

{{<highlight javascript>}}
db.developers.find({"languages.1" : "java"})
{{</highlight>}}

Next, if we want to list all documents that contain “java” at any position inside languages array 
field, we do it like this:

{{<highlight javascript>}}
db.developers.find({"languages" : "java"})
{{</highlight>}}

Command output lists all initially inserted documents:

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
    "name" : "Jack",
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

Finally, if we want to find all documents that have languages field exact to let’s say:

{{<highlight json>}}
["java", "php", "javascript"]
{{</highlight>}}

we do it like this:

{{<highlight javascript>}}
db.developers.find({"languages" : ["java", "php", "javascript"]})
{{</highlight>}}

Command output prints something like:

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
{{</highlight>}}

I’m primary keeping this as a quick reminder to myself, but will be very happy if someone else 
finds it useful!

Until next time!