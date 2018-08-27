---
layout: page
title: Parse Query Basics
permalink: /parse-query-basics/
tags: JS
---
In this lesson, we are going to look at queries in parse.

######WHAT'S A PARSE QUERY & WHAT IS THE NEED FOR A QUERY
Querying parse is like a asking the parse server to filter data based on certain rules.
parse queries are sent to the server and the parse server returns datasets matching the ruleset(much like filtering data)
A parse query is used to select a subset of the data stored on the server based on the certain rules


``` javascript
use initialize //demo code
initialize url
``` 
Intialize and specify URL for query

######DATA TO BE USED 

soccer teams
players

screenshots of data to be used


we are going to use these datasets during the course of this lecture
and  our queries is going to be based on the PLAYERS & TEAMS datasets


first 2 lines defines classes for TEAM & PLAYER
``` javascript
var q = new Parse.Query("Team");
q.equalto("code", "MCFD");
```

Here we create a instance of the query object and specify the data, on which the query is to be based
and specify the query criteria(filtering rules)

At this point the  query is just defined here and not yet executed(not yet sent to the server for processing)

``` javascript
q.find();
```
In the above line we send the request to the Parse server and the server performs the data selection based on filtering criteria eg finding all the TEAMS where TEAM CODE is equal to MCFD

After the processsing by the Parse server is complete, the results are sent back
and the results have to be captured using a promise

``` javascript
q.find().then(function(results){
	console.log(results);
})
``` 


