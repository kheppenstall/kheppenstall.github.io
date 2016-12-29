---
layout: post
title:  "MongoDB and Rails"
date:   2016-12-29 11:16:29 -0500
categories: mongo activerecord rails ruby
---

### Understanding The Parts

When starting with rails it is customary to use ActiveRecord with a SQL database like PostgreSQL or MySQL. When I decided to create a new rails project using the document-based database MongoDB, I found there were surprisingly few resources to help me transition. I made a project using MongoDB and documente d what I learned.

If you're wondering what a non-relational database is, checkout this [blog post](https://www.pluralsight.com/blog/software-development/relational-non-relational-databases). One key difference is that relational databases let you link data between tables using keys. Non-relational databases store data with no inherent structure to make connections or pass around keys.

First off, let's I made a chart to show some terminology comparing the database terms most rails developers are used to and their MongoDB equivalent.

Relational Database | Non-relational Database
--- | ----
SQL | NoSQL
PostgreSQL | MongoDB
Object Relational Mapper | Object Document Mapper
ActiveRecord | Mongoid


### Advantages and Disadvantages

At first I had trouble understanding when a document-based database like MongoDB would be preferable to a relational database. After some reading I found a few reasons to choose a database like MongoDB. The first is flexibility. There are no migrations and no schema. And although that seems scary, you avoid the headache of worrying about creating and running migrations or rolling back when something did not work. Moreover, if you need to make a significant change to the structure of your database, it is easy with Mongo since there is nothing to rollback. Just go into the models and make the edits you need. Another advantage of Mongo is scalability. It is just JSON documents so simple retrievals and additions are quick and easy. 

With the advantages there also are plenty of disadvantages. Complex relationships are tough to model just using JSON documents. So if there are lots of potential has_many or belongs_to relationships in your dataset, it could be challenging to figure out how to use Mongo without repeating data in the database or adding foreign keys (and adding a relational component to your database). Furthermore, there is no way to run a joins. As a result complex queries and analysis on the data take more time to figure out and more code for the same task.


### Getting Started with Mongoid in a New Rails Project

Mongoid is an excellent Ruby gem that maps your Ruby objects to documents in a MongoDB database. [This post](http://kerrizor.com/blog/2014/04/02/quick-intro-to-mongodb-in-rails) is the best resource I found for starting a new project with rails. The only difference I found is that it was better to just use `gem mongoid` for rails 5, not `gem "mongoid", git: 'git://github.com/mongoid/mongoid.git'`.

Once you're setup the [Mongoid documentation](https://docs.mongodb.com/ruby-driver/master/mongoid/) is excellent when creating new models, embedding documents, and querying the database. 


