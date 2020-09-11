---
layout: post
title:      "Got some styling in my CSS, I'm cache(in)' out"
date:       2020-09-10 16:57:24 -0400
permalink:  got_some_styling_in_my_css_im_cache_in_out
---


At some point, you have probably spent hours figuring out why your application is not loading correctly or why a change is not showing up in the browser. You may have been instructed to 
“clear your cache.” But what exactly is a cache, why is it important, and what does clearing it do?

A cache (pronounced like “cash”) is a mechanism used by browsers to optimize user experience. When you visit a site for the first time, a cache temporarily stores HTML, CSS, and JavaScript (and more) locally. When you visit the site again, the browser will load information from the cache instead of going all the way to the server and waiting for a response. Data is simply read from in-memory storage, boosting speed and performance. 

A cache can be thought of as a bedroom dresser. A dresser stores clothes, with the most frequently worn garments on the top. In the summer, shorts and tank tops are easily accessible, while jackets and jeans are located at the bottom. Just as a cache responds to a request with the most current information, a dresser allows you to quickly grab recently worn clothing (without having to dig through the entire drawer). 

So, what happens when you clear your cache? Let’s use Amazon as an example. Your cache might hold information such as the hat you searched for last week or the puzzles you viewed during quarantine. Clearing your cache will delete the previously mentioned browsing history from your local hard drive. However, it will not remove account information such as a saved username and password. 

During the tailend of this project, I came across this issue. My RESTful routes and CRUD actions were working, but I was not able to see any styling changes in the browser. I spent a few hours tinkering with my CSS and ERB files, trying different selectors and tags. I tried starting and stopping shotgun, with no luck. As a last-ditch effort, I tried viewing my application in chrome using the “Incognito” window. To my excitement, I saw all of the color and font changes! I then realized my browser had been loading previously saved files from my cache. Because the Incognito window does not use a cache, I was able to finally see my styling changes. 

If you come across this issue, you can easily fix it by changing your settings in DevTools:

1.	Right click anywhere on the screen and select “inspect”
2.	Go to the network tab
3.	Check the box labeled “disable cache”

![devtools](https://imgur.com/PW72Lhl)

While the cache can save you time, it can also waste it. Make sure you are cache(in)’ out periodically, whether that is clearing the cache, disabling it, or viewing your application using the Incognito window. 
 

