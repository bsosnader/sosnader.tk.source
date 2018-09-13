---
layout: post
category: technology
title: "Nearby: Crowdsourced Fun"
---

This past spring, I took Penn State's CMPSC 431W - Database Management Systems class. The class is meant to be a senior year, capstone class, so a major part of the grading process is a semester-long group project focused on full-stack development. 

My group's idea was a crowd-sourced web application to for nearby events, meetups, or giveaways. The intention would be for users to post events that they walked by, or knew were happening somewhere close by. Then, other users would vote on the events so people could see the popular things going on around them. It would all be based on the user's location. We knew we were being kind of ambitious for the amount of time we had, but we thought it was a really cool idea so we went with it!

<!--more-->

We first had to go through the typical steps that any software development project should go through at the beginning--writing functional specifications and sketching out the structure and layout of the application--before starting any coding. For me, this was great experience for working in a real development environment. Often, personal projects will just get slapped together a little bit too quickly, when some initial planning could have made things much easier. 

Through the planning process, we settled on a system framework. 

![framework]({{ baseurl  }}/img/posts/sys_framework.png)

I primarily did the work on the frontend. I had a fair amount of experience with Angular from my previous internship, so it made sense to use it again on this project. I thought it would be a breeze, but I actually ran into a few interesting issues to tackle and learn about throughout the implementation.

# Location

One of the biggest features of the application was that it was location-based. Everything revolved around seeing *nearby* events--you wouldn't really care about something going on 50 miles away. So one of the most important features of the project was to be able to actually get that information from the user. 

There's supposed to be a built-in API in the browser for doing it, and the code is simple enough: 

```javascript
navigator.geolocation.getCurrentPosition(function(position) {
  do_something(position.coords.latitude, position.coords.longitude);
});
```

However, what I found was that often during testing, and less frequently in a live environment, the API just wouldn't work. There wasn't really a clear explanation why it would happen, and it persisted even after fiddling with several different settings.

I decided that a efficient and easy fallback would be to simply ask for the user's zipcode if the location request didn't go through. That way, a user could still use the app even if the API failed or if they didn't want to share their exact location.

# User Authentication

This project was the first time I had actually had to implement something more than just a simple authentication window in an application. Fortunately, using JSON Web Tokens made it extremely easy to authenticate users and store their tokens for persistent login. 

One thing that caused a wrinkle here was that our app's components had different functionalities and looks depending on if a user was logged in or not. It was initially challenging to find a way to ensure that every component that needed to would update when a user logged in or out.

To fix this I made use of Angular's [shared service](https://angular.io/guide/component-interaction#parent-and-children-communicate-via-a-service) concept. Basically, every time a login or logout event was emitted, it would broadcast that through the service to all other components subscribed to that service. That way, I could basically have a communication path between components that would allow for them to listen for those events. 

# Takeaways

From this project I got a lot more experience with Angular and with working in a group. Collaborating with the backend group members, building up features and interactions, and linking everything together was exciting and educational. 

The app is no longer online because we didn't want to pay for hosting, but you'll find some screenshots below!

<div class="slider">
  <div><a href="{{ baseurl }}/img/projects/nearby/1.png"><img src="{{ baseurl }}/img/projects/nearby/1.png" alt="nearby screenshot" /></a></div>
  <div><a href="{{ baseurl }}/img/projects/nearby/2.png"><img src="{{ baseurl }}/img/projects/nearby/2.png" alt="nearby screenshot" /></a></div>
  <div><a href="{{ baseurl }}/img/projects/nearby/3.png"><img src="{{ baseurl }}/img/projects/nearby/3.png" alt="nearby screenshot" /></a></div>
  <div><a href="{{ baseurl }}/img/projects/nearby/4.png"><img src="{{ baseurl }}/img/projects/nearby/4.png" alt="nearby screenshot" /></a></div>
</div>

