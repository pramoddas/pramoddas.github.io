---
layout: page
title: Parse Query Basics
permalink: /parse-query-basics/
tags: JS
---
In this lesson, we are going to look at queries in parse.

WHAT IS A PARSE QUERY & WHAT IS THE NEED FOR A QUERY
Querying parse is like a asking the parse server to filter data based on certain rules.
parse queries are sent to the server and the parse server returns datasets matching the ruleset(much like filtering data)
A parse query is used to select a subset of the data stored on the server based on the certain rules


HOW TO
use initialize
initialize url

DATA TO BE USED

soccer teams
players

520 players
22 teams
we are going to use these datasets during the course of this lecture
so our queries is going to be based on PLAYERS & TEAMS

CODE
first 2 lines defines classes for TEAM & PLAYER
{% highlight javascript %}
var q = new Parse.Query("Team");
{% endhighlight %}


a query instance we can perform queries on the database

q.eaulto("code", "MCFD")
defining the query parameters, where we idetify the query ruleset...keep in mind that the query is defined here and not yet executed

q.find();
sends the request to the server and the server performs the data selection based on filtering criteria
which is .......finding all the TEAMS where TEAM CODE is equal to MCFD
q.find() returns results after querying on the server
the results can be captured using a promise 
q.find().then(function(results))



