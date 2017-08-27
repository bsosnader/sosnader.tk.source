---
layout: post
category: technology
title: "My Summer Internship"
---

This summer, I had the pleasure of working at HighPoint Solutions in their Outsourcing department. The entire summer I was working on a project they assigned to one other intern and me.

The project was a pretty cool and unique one. Since HighPoint is consulting company, they have to fill out a lot of Request For Proposal (RFP) documents to send to prospective clients. RFPs are basically a list of questions a company will send to other companies who want to help them outsource something. The other companies fill out their answers to these questions and send them back, and then there's a bidding process which ultimately ends in one particular company being selected for the outsourcing (in our case, hopefully HighPoint!).

As you might imagine, a large amount of these questions end up being very similar from company to company. HighPoint wanted a way to easily search for questions from past RFP documents that contained similar keywords to questions they wanted to answer. Previously, their only way of doing this would be to open each individual RFP document and search for the questions/keywords on a document by document basis. What the envisioned was a search bar where they could copy and paste a question, and get a list of previous questions and answers similar to that question, along with some metadata tags, like what original document it came from, or the date it was submitted.
<!--more-->

Our managers gave us pretty much free reign to build whatever we wanted, as long as it had the functional specs they were looking for. The first thing we did on our own was separate the project into the three distinct parts that seemed clear to us. First, we knew we needed to make some sort of parser that could extract the questions and answers from RFP documents. Second, we needed a way to store those entries in a way that was easily searchable. Finally, we needed a UI that wrapped everything together and allowed for searching and uploading of documents.

# The parser

The parser was written in Java, mainly because we wanted to use [Apache POI](https://poi.apache.org/), a really good API for parsing Microsoft documents. We figured the simplest way to extract the data was to have users highlight questions, and color answers red. This way, the parser could easily pair them together. We put the parser on a Java servlet which accepted http POST requests containing metadata tags and the document.

# The database

SQL Databases are great for a lot of things, but fast full-text search isn't really one of them. For that, we needed a search engine, something that could index the text really well and allow for fast and complex queries to be made to our text. [Elasticsearch](elastic.co) fit the bill while also being open source and well-documented (big relief). Elasticsearch is a search engine built on top of Apache Lucene that is truly fantastic-- it had all of the features I needed to easily filter and manipulate the data and was fast and easy to learn.

# The front-end

When thinking about what to use for the web front-end, I thought a lot about what we needed to be able to do and display. I knew that there would be a lot of API calls and HTTP GET / POST requests, as well as a need for dynamic results, options, and forms. [Angular](http://angular.io/), which I've worked with a little bit before, seemed to be well-suited to the task. And it really, really was. From the pipes allowing me to easily separate and display what data Elasticsearch gave me, to the modularity of the components and the sensibility of using services, Angular really let us do everything we wanted to.

# Putting it all together

Once we had these components all working, it was time to put them together and deploy them for production use. We put the Java servlet and the web front-end on an Apache Tomcat web server (it amazes me how many different libraries and products Apache has), and the ES instance on its own platform. There were a few tricky things here, like linking up Tomcat with the company's domain controller to authenticate users in Active Directory, but otherwise this part went smoothly. We were working pretty much up until the last few days, which were then spent making sure everything was well documented and ready to be passed on.

# Final Thoughts

Overall, the internship was a ton of fun and exposed me to the real world of software development. We were given functional specs for a project, and had to accomplish our goals while being involved in regular meetings and updates on our progress. We had to adhere to company restrictions and policies for building software, and had to make sure our project was what the company really needed. At the end of the summer, the Senior Vice President of our department said it was the best intern project he'd ever seen, which was incredible to hear.

Below is a screenshot of a search result from the app. It looks simple, but the work that went into making it happen was much more than simple.

![app image]({{ baseurl  }}/img/posts/blurry.png)
