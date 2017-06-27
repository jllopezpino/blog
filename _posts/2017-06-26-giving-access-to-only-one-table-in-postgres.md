---
layout: post
title: Giving access to only one table in Postgres
excerpt: "How to give access to only one table in Postgres"
tags: [database, sql, postgres, permissions]
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
CREATE ROLE availability_user LOGIN PASSWORD 'super_secret_password_000' VALID UNTIL 'infinity';
GRANT CONNECT ON DATABASE marketingdb TO availability_user;
{% endhighlight %}


### 2- Create a new schema to move all the existing tables

{% highlight sql %}
CREATE SCHEMA private_schema;
REVOKE ALL ON SCHEMA private_schema FROM public;

ALTER TABLE ag_events SET SCHEMA private_schema;
{% endhighlight %}

If you want to move all existing tables in the public schema to the new schema, try this [PL/SQL script](https://stackoverflow.com/a/10224326/337746).

### 3- Gran permissions to the already existing users to that schema

{% highlight sql %}
GRANT ALL PRIVILEGES ON                  SCHEMA private_schema TO "QDGItEsCY2J3vspb";
GRANT ALL PRIVILEGES ON ALL TABLES    IN SCHEMA private_schema TO "QDGItEsCY2J3vspb";
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA private_schema TO "QDGItEsCY2J3vspb";
GRANT ALL PRIVILEGES ON                  SCHEMA private_schema TO rdsadmin;
GRANT ALL PRIVILEGES ON ALL TABLES    IN SCHEMA private_schema TO rdsadmin;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA private_schema TO rdsadmin;
{% endhighlight %}


### 4- Make sure that new users can't access the private schea unless permission is granted


{% highlight sql %}
REVOKE ALL ON SCHEMA private_schema FROM public;
REVOKE ALL PRIVILEGES ON SCHEMA private_schema FROM availability_user;
ALTER DEFAULT PRIVILEGES IN SCHEMA private_schema REVOKE ALL ON TABLES FROM PUBLIC;
{% endhighlight %}

### 4- Make sure users can still find the tableS in the new schema


{% highlight sql %}
ALTER DATABASE marketingdb SET search_path TO private_schema, postgres, public;
{% endhighlight %}



### 4- Create the new table in the public schema

{% highlight sql %}
CREATE TABLE public.films (
    code        char(5) CONSTRAINT firstkey PRIMARY KEY,
    title       varchar(40) NOT NULL,
    did         integer NOT NULL,
    date_prod   date,
    kind        varchar(10),
    len         interval hour to minute
);
{% endhighlight %}
