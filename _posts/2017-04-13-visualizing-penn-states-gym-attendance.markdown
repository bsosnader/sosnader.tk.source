---
layout: post
category: technology
title: "Visualizing Penn State's Daily Gym Attendance"
---

![white building ]({{ baseurl }}/img/posts/gym.jpg)

#### Notice: As of Summer 2018, Penn State has taken down the website I used to scrape data from, so the graph doesn't work anymore. :( Also, turns out I don't have any full-size screenshots saved anymore, so enjoy this blurry thumbnail of the final product

![fitness thumbnail]({{ baseurl }}/img/posts/fitness.png)

If you're a Penn State student who likes to exercise, you know that the three on-campus gyms can get *really* crowded. It sometimes seems like you have to get there immediately at opening time if you want to get a peaceful, uninterrupted workout in. It's to be expected--although three gyms is a lot, 46,000 students is even more. The University recognizes that this can be a problem, and takes a count of students entering and exiting each gym, uploading it to a [really neat webapp](https://studentaffairs.psu.edu/CurrentFitnessAttendance/) where you can see how crowded each gym is. But that doesn't really help you plan--you can only see what's happening in the moment. Sure, eventually if you check it enough you get used to knowing when the crowds come and go, but it'd be a lot more useful (and interesting) to see attendance charted over time. So I decided to do just that.
<!--more-->

# Breaking it down

I expected this to be a fairly large project, so I had to separate it into manageable chunks. I decided to work on actually obtaining the data first, and left the front-end visualization for later. I knew I'd need to store it in some kind of database as well, but I had to figure out how to actually *get* the data I wanted.

# The Scraper

The first thing I tried was looking in the source code for any clues as to how the page was getting its data. I quickly found the [API link](https://studentaffairs.psu.edu/CurrentFitnessAttendance/api/CounterAPI), but, unfortunately, it wouldn't let me access it. Next, I tried to use the python module [beautifulsoup](https://www.crummy.com/software/BeautifulSoup/) to scrape and parse the html. That didn't work either, for an obvious reason--the page was rendered using javascript, and beautifulsoup alone can't get the javascript-rendered html--it can only get the basic html which didn't have the values I wanted.

Instead, I had to set up a headless browser using selenium and phantomJS in my python browser. What the headless browser does is basically go to the webpage and pretend to be a regular browser, like Chrome, so that the page will render its javascript. Then you can pass the rendered html over to beautifulsoup and commence the parsing. The code is super simple, too:

```python
url = 'http://example.com'
driver = webdriver.PhantomJS()
driver.get(url)
soup = BeautifulSoup(driver.page_source, "html.parser")
```
Then you can use the soup object to extract whatever data you need, and you're set! You just need to make sure you have phantomJS installed.

```bash
sudo apt-get install phantomjs
```
Also make sure to include the proper imports at the top of the python file:

```python
from selenium import webdriver
from bs4 import BeautifulSoup
```
You'll probably want regex as well:

```python
import re
```

So now that I had a reliable way of scraping data from the website, I needed to figure out how to store it.

# The Database

The selection of a databse for this project was actually something that took me a bit of time to consider. I couldn't really use a regular relational database because it didn't make much sense. It's not like I'm storing lists of users, where each row is a distinct user with distinct fields. I really only have three distinct 'things'--the three gyms. Each gym has attendance values and a timestamp associated with that value. For that kind of setup, a time series database should be used. Time series databases are built for this exact scenario and make it easy to organize the data.

I chose to use [InfluxDB](https://www.influxdata.com/) because it was free, had good documentation, and seemed to have all the features I was looking for without being unnecessarily complex or bloated. It has extensive libraries for many languages, and it was really easy to link to python. All I had to do was convert my html from earlier to a JSON format dict and use it in the following code:

```python
def main(host='localhost', port=8086):
    user = 'user'
    password = 'password'
    dbname = 'name'
    json_body = format_url() # this is my function from the scraper class

    client = InfluxDBClient(host, port, user, password, dbname)

    client.create_database(dbname)

    client.create_retention_policy('policy_name', '7d', 1, default=True)

    client.write_points(json_body)

def parse_args():
    parser = argparse.ArgumentParser(
        description='example code to play with InfluxDB')
    parser.add_argument('--host', type=str, required=False, default='localhost',
                        help='hostname of InfluxDB http API')
    parser.add_argument('--port', type=int, required=False, default=8086,
                        help='port of InfluxDB http API')
    return parser.parse_args()

if __name__ == '__main__':
    args = parse_args()
    main(host=args.host, port=args.port)
```

I got all of that working on my laptop, and now had a python script I could run to consistently add data to the database. The next step was getting data in regular intervals. I needed to set up a server.

# The Server

As a cheap college student, I never want to spend money if I don't absolutely have to. For instance, this website is hosted by [github pages](https://pages.github.com/) entirely for free, as is my [Grade Calculator](http://sosnader.com/grade-calc/) project. But, github pages is just a simple static host for frontend content. I needed a real machine to run my database on.

Before making the jump to buying a VPS though, I first pulled out my dusty old Raspberry Pi Model A. After a *lot* of tweaking and fiddling, I got the database set up on a headless installation of raspbian. There's not too much to talk about here. After installing InfluxDB, Python 3, and all their respective dependencies, I added a simple cron job add data every ten minutes.

```bash
*/10 * * * * bash -l -c '/home/brenden/psu-fitness/database.py' >> /home/brenden/logs/cron.log 2>&1
```

Once I verified that my setup was working correctly, I grabbed a server from [Vultr](https://www.vultr.com/) and replicated my setup. Originally, I planned on only hosting the database in the VPS, and putting the front end on github pages. However, I figured since I already had the server I might as well use it as the web server as well. I've never configured my own web server from scratch before anyway, so I viewed it as a new challenge.

Configuration was actually super easy. I installed nginx and set up a configuration file. The config file had two key parts. The first part told the server where to look to find the html files to serve to port 80. THe second part told the server where to look if someone queried the database, which, for InfluxDB, takes the form of:

```
http://servername:8086/query?your-query-here
```
I didn't want to have that silly 8086 there so I set up a reverse proxy to point any requests to

```
http://servername/query
```

to port 8086 and to the database. InfluxDB has some basic authentication built in, and I'm not too worried about anyone hacking into my database. They wouldn't find anything anyway, and I took enough basic steps I feel to stop any bot or casual hacker from doing something they shouldn't.

Speaking of security, I also decided to add SSL support to the site. I was expecting this to be a huge hassle, but luckily [Let's Encrypt](https://letsencrypt.org) offers a super painless way to set up SSL support on a web server. I just had to change my nginx configuration file a little bit and run the tool that Let's Encrypt supplies and I was good to go.

# The Front End

The final part of this challenge was the front end. I wanted to go barebones this time--I didn't think I needed any sort of Angular framework or the like. I just wanted a simple page to display the graph and some info and nothing else. [ChartJS](http://chartjs.org) is a great, simple JavaScript charting library that I was easily able to adapt to my purposes. After whipping a few styles together along with the [Skeleton CSS Framework](http://getskeleton.com) (a new favorite of mine), the site was finally ready to deploy! You can view it at [https://fitnessdb.tk](https://fitnessdb.tk).

# What I Learned

This project took a few weeks of my time, but the efforts were more than worth it. Not only did I create something that's sorta cool, I also learned a ton about a bunch of different technologies in the process. I got a *lot* more comfortable with databases than I have been in the past. I really had never used or configured a database up until this point, and this project was a great way to learn in a hands on approach.

I also got better with using external Python and JavaScript libraries. Documentation can often be sparse and unreadable, and I definitely learned how to adapt and twist things to do what I wanted them to do.

Funnily enough, I think what I learned the *most* about through this project was the linux shell. I had to use it extensively to configure the servers, run commands, and test things. I got far more comfortable with it than I had been before, and I think those skills will really help my productivity in the future.

Until next time!
