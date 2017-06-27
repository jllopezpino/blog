---
layout: post
title: Giving access to only one table (or a few) in Postgres
excerpt: "How to use schemas to give limited access to new users to our Postgres database"
tags: [database, sql, postgres, permissions, schemas]
modified: 2017-06-26
comments: true
image:
    feature: old-days-of-technology.jpg
---

Companies are reluctant to share their data and that's a good thing.
But in many cases we forget that we can grant limited access to our database to our partners and stakeholders.
Managing users privileges is often cumbersome, but it can save you a lot of time.
How many companies do you know still sending CSV files around?


Here are there are a few simple SQL statements to be able to give access to (and permission to modify) only a few tables.
Or better said, what we are doing with these statements it to hide the content tables from new users.


### 1- Create the new user

{% highlight sql %}
CREATE ROLE new_user LOGIN PASSWORD 'here_goes_the_password' VALID UNTIL 'infinity';
GRANT CONNECT ON DATABASE our_db TO new_user;
{% endhighlight %}


### 2- Create a new schema to move all the existing tables

{% highlight sql %}
CREATE SCHEMA private_schema;
REVOKE ALL ON SCHEMA private_schema FROM public;

ALTER TABLE already_existing_table SET SCHEMA private_schema;
{% endhighlight %}

If you want to move all existing tables in the public schema to the new schema, try this [PL/SQL script](https://stackoverflow.com/a/10224326/337746).

### 3- Gran permissions to the already existing users to that schema

{% highlight sql %}
GRANT ALL PRIVILEGES ON                  SCHEMA private_schema TO "User1";
GRANT ALL PRIVILEGES ON ALL TABLES    IN SCHEMA private_schema TO "User1";
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA private_schema TO "User1";
GRANT ALL PRIVILEGES ON                  SCHEMA private_schema TO "User2";
GRANT ALL PRIVILEGES ON ALL TABLES    IN SCHEMA private_schema TO "User2";
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA private_schema TO "User2";
{% endhighlight %}


### 4- Make sure that new users can't access the private schea unless permission is granted


{% highlight sql %}
REVOKE ALL ON SCHEMA private_schema FROM public;
REVOKE ALL PRIVILEGES ON SCHEMA private_schema FROM new_user;
ALTER DEFAULT PRIVILEGES IN SCHEMA private_schema REVOKE ALL ON TABLES FROM PUBLIC;
{% endhighlight %}

### 4- Make sure users can still find the tables in the new schema


{% highlight sql %}
ALTER DATABASE our_db SET search_path TO private_schema, postgres, public;
{% endhighlight %}



### 4- Creating new tables

From now, you will only have to be careful when creating new tables.
I strongly recommend to use only schema-qualified table names when creating new tables.

For example:

{% highlight sql %}
CREATE TABLE private_schema.films (
    film_id        char(5) CONSTRAINT pk_film_id PRIMARY KEY,
    title       varchar(40) NOT NULL
);
{% endhighlight %}
