---
layout: post
title: Some RDFa examples
slug: rdfa_examples
excerpt: "Some examples on how RDFa can be used to store metadata."
tags: [business intelligence, it4bi, metadata, rdfa, xml]
modified: 2013-12-23
comments: true
image:
    feature: old-days-of-technology.jpg
---


This post is part of my work for the course _**XML and Web Technologies**_ of the IT4BI (Information Technology for Business Intelligence) master programme. You can check out the presentation and the complete report on [my Slideshare ](http://www.slideshare.net/jlpino)profile.


## Using RDFa to improve websites


RDFa is not supported by schema.org, a shared markup vocabulary defined in collaboration by Google, Microsoft and Yahoo!, Google [2] has defined specific vocabulary for reviews, people, products, businesses, organizations, recipes, events and videos. For instance, in the following picture we can observe how they use the metadata stored in RDF attributes to improve the result of website reviews, they call those results "rich snippets".


[![iphone_reviews](http://jllopezpino.files.wordpress.com/2013/06/iphone_reviews.png?w=300)](http://jllopezpino.files.wordpress.com/2013/06/iphone_reviews.png)




This is an example from the W3C blog [1] that uses RDFa 1.0 to add metadata to a review, helping Google to index it:




[![rdfa_example](http://jllopezpino.files.wordpress.com/2013/06/rdfa_example.png?w=300)](http://jllopezpino.files.wordpress.com/2013/06/rdfa_example.png)




In 2009 the Central Office of Information had to face a big problem: organise the job vacancies and they needed to find the way of doing it without changing the websites of the different public agencies, because they use diverse web technologies. [3]




For this purpose they defined a **vocabulary** that could also be usable by others. With this vocabulary they are able to define the details of the job vacancy: the title, the type, the description, the requirements, language, etc. [4] After that they started to use this vocabulary implementing RDFa in different websites.




Another case of successful use of RDFa is GoodRelations [5], a vocabulary for e-commerce that helps to standarise the metadata of different vendors. It helps vertical searchs, for instance users that look for products in different websites or companies that need different suppliers. Multiple shop applications like Magent have already included it in their software solutions and it is possible to define it using RDFa, for example BestBuy use RDFa to define information about their stores like the opening hours, the location, the telephone number, etc. [6]





## Extracting the data embedded in RDFa


As we have already mentioned, Google defines a specific vocabulary for people.To add metadata in the RDF attributes of a XHTML document we can use any text or source code editor. However it is tricky to check a whole document for extracting only the metadata from it. In this case, we can use multiple tools to make this task easier, for instance we can install an extension for Chrome called _RDFa Triples Lister_ that extracts the metadata of the website we at visiting with this browser:


[![rdfa_chrome_extension](http://jllopezpino.files.wordpress.com/2013/06/rdfa_chrome_extension.png?w=300)](http://jllopezpino.files.wordpress.com/2013/06/rdfa_chrome_extension.png)




We can use RDF **parsing tools** that exact the RDFa embedded in a web page, for example with the _rdfquery _[7] tool we can read the RDFa information of BBC programmes and use it to create links to Spotify and stream the songs[8]. The following graph, created with RDFa play [9], shows the RDF information extracted from a programme of the BBC [10]:




Finally, the W3C has defined a mechanism to extract data compatible with the Resource Description Framework, including RDFa. For this purpose we have to define transformations that are instructions for extracting any embedded data properly [24]. For RDFa we can find a style sheet that defines the transformations that has to be done to a XHTML+RDFa document to extract the RDF data.[26]




[![bbc_rdfa_graph](http://jllopezpino.files.wordpress.com/2013/06/bbc_rdfa_graph.png?w=300)](http://jllopezpino.files.wordpress.com/2013/06/bbc_rdfa_graph.png)




For instance, the following image illustrates an interesting example: we can find in the web different calendars and probably the metadata is defined using different techniques (microformats, RDFa, etc.). The GRDDL transformations specify how to extract the RDF data from each document. Once we have extracted the RDF triples we can process them using for example SPARQL (query language for RDF).[25]




[![grddl](http://jllopezpino.files.wordpress.com/2013/06/grddl.png?w=300)](http://jllopezpino.files.wordpress.com/2013/06/grddl.png)





## Exercise


As we have already mentioned, Google defines a specific vocabulary for people. This vocabulary is very useful to make our social networking information accessible. The properties defined in the vocabulary are: [![exercise_vocabulary](http://jllopezpino.files.wordpress.com/2013/06/exercise_vocabulary.png?w=300)](http://jllopezpino.files.wordpress.com/2013/06/exercise_vocabulary.png) We need to use the vocabulary presented above, to modify this webpage adding metadata to it with RDFa: 

[![exercise_webpage](http://jllopezpino.files.wordpress.com/2013/06/exercise_webpage.png?w=300)](http://jllopezpino.files.wordpress.com/2013/06/exercise_webpage.png) 


A possible solution could be: 

[![exercise_solution](http://jllopezpino.files.wordpress.com/2013/06/exercise_solution.png?w=300)](http://jllopezpino.files.wordpress.com/2013/06/exercise_solution.png)


## REFERENCES


[1] [W3C. RDFa 1.1 with a rich snippet example](http://www.w3.org/QA/2011/05/rdfa_11_with_a_rich_snippet_ex.html)

[2] [Google. Rich snippets (microdata, microformats, RDFa, and Data Highlighter)](http://support.google.com/webmasters/bin/answer.py?hl=en&answer=99170)

[3] [Birbeck, M. More RDFa goodness from UK government web-sites](http://internet-apps.blogspot.fr/2009/04/more-rdfa-goodness-from-uk-government.html)

[4] [Birbeck Mark. (n.d.).Argot Vacancy](https://code.google.com/p/argot-hub/wiki/ArgotVacancy)

[5] [GoodRelations Wiki. (n.d.).The Web Vocabulary for E-Commerce](http://wiki.goodrelations-vocabulary.org/Quickstart)

[6] [Myers, J. CREATING LOCAL VISIBILITY TO OPEN BOX PRODUCTS WITH FRONT-END SEMANTIC WEB](http://jay.beweep.com/2010/03/30/creating-local-visibility-to-open-box-products-with-front-end-semantic-web/)

[7] [Google. rdfquery](https://code.google.com/p/rdfquery/)

[8] [Adding Spotify links to BBC Radio playlists, via RDFa, using Greasemonkey and rdfQuery](http://hublog.hubmed.org/archives/001913.html)

[9] [RDFa Group. RDFa Info](http://rdfa.info/play/)

[10] [Use of Semantic Web technologies on the BBC Web Sites](http://www.cmswire.com/cms/information-management/bbcs-adoption-of-semantic-web-technologies-an-interview-017981.php)

[24] [Gleaning Resource Descriptions from Dialects of Languages (GRDDL)](http://www.w3.org/TR/grddl/)

[25] [GRDDL Use Cases: Scenarios of extracting RDF data from XML documents](http://www.w3.org/TR/2007/NOTE-grddl-scenarios-20070406/)

[26] [RDFa2RDFXML style sheet](http://www.w3.org/TR/grddl-primer/RDFa2RDFXML.xsl)


