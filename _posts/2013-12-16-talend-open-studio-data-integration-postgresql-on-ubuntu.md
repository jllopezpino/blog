---
comments: true
image:
    feature: old-days-of-technology.jpg
date: 2013-12-16 11:47:30+00:00
layout: post
slug: talend-open-studio-data-integration-postgresql-on-ubuntu
title: Getting started with Talend Open Studio Data Integration and PostgreSQL on
  Ubuntu
wordpress_id: 662
categories:
- Business Intelligence
tags:
- business intelligence
- etl
- postgres
- postgresql
- talend
- ubuntu
---

This is a very short guide that provides the essential steps to install PostgreSQL and develop a very simple job that loads some rows in a table.  TOS DI is an open source ETL tool like many others.


# Installing PostgreSQL





	
  * Install the server, the client and padmin3 (tool to configure the server easily).
  
  
{% highlight bash %}
sudo apt-get install postgresql postgresql-client pgadmin3
{% endhighlight %}

	
  * Connect to the server{% highlight bash %}sudo -u postgres psql{% endhighlight %}

	
  * Create our first database using the template by default. I called it 'movies'
{% highlight bash %}CREATE DATABASE movies TEMPLATE DEFAULT;{% endhighlight %}

	
  * Change the password of the user postgres:
{% highlight bash %}ALTER USER postgres WITH PASSWORD 'postgres';{% endhighlight %}

	
  * If you are a UI lover, you can continue using pgadmin3 from this point:

<a href="http://jllopezpino.files.wordpress.com/2013/03/pgadmin3-configuration.png">
    <img src="http://jllopezpino.files.wordpress.com/2013/03/pgadmin3-configuration.png?w=300" alt="pgadmin3 configuration" height="300" class="aligncenter size-medium wp-image-666" width="300" />
</a>



# Creating a job with Talend Open Studio Data Integration


We are create for instance a job that loads data from a file and insert it in a table of our database.


  * Download the [Talend Open Studio for Data Integration](http://www.talend.com/download/data-integration). It might take time so meanwhile you can take a look at the [Talend Help Center](https://help.talend.com/display/HOME/Talend+Open+Studio). Once it's downloaded, install it following the [installation guide](http://www.talendforge.org/wiki/doku.php?id=doc:installation_guide) and the [steps to install xulrunner](http://www.talendforge.org/wiki/doku.php?id=doc:installation_on_ubuntu).

	
  * Open TOS DI and create a new job.
  
  
[![newjob](http://jllopezpino.files.wordpress.com/2013/03/newjob.png?w=300)](http://jllopezpino.files.wordpress.com/2013/03/newjob.png)

	
  * From the right panel, drag and drop a tFileInputDelimited component.

	
  * Change the properties of the input component.

    * Select the file that is the source of your data and, in my case, I also changed the field separator.

    * Select the file that you are going to use as input.

    * Define the Schema of the input. In my case the text file contains only two fields.

    * In my case, I also changed the field separator to \t.
    
    
[![fileinputdelimited](http://jllopezpino.files.wordpress.com/2013/03/fileinputdelimited1.png?w=300)](http://jllopezpino.files.wordpress.com/2013/03/fileinputdelimited1.png)




	
  * From the right panel, drag and drop a tPostgresqlOutput component.

	
  * Connect the two of them: right click on the input component -> row -> main and click on the output component.

[![job](http://jllopezpino.files.wordpress.com/2013/03/job1.png?w=300)](http://jllopezpino.files.wordpress.com/2013/03/job1.png)

	
  * Change the properties of the output component:

	
    * Define the parameters to connect to the database.
	
    * Editing the Schema we will check that the changes that we did in the input component have been propagated to the schema of the output component.

    * In the advanced settings tab we can uncheck the 'Use batch size' option to get more accurate exception error messages. Otherwise the message might be "Call getNextException to see the cause"

[![tPostgresqlOutput](http://jllopezpino.files.wordpress.com/2013/03/tpostgresqloutput.png?w=300)](http://jllopezpino.files.wordpress.com/2013/03/tpostgresqloutput.png)

	
  * Run the job (F6) and if you didn't make any mistake, the rows will be in the created table. We can check that the table has been created and the information is there either with pgadmin3 or with the command-line client (psql):


{% highlight bash %}
\connect movies
select * from "ED_I_Actors";{% endhighlight %}
