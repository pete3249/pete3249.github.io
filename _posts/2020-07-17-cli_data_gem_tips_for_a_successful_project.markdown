---
layout: post
title:      "CLI Data Gem: Tips for a Successful Project"
date:       2020-07-17 11:55:38 +0000
permalink:  cli_data_gem_tips_for_a_successful_project
---


I completed the CLI Data Gem project this morning and it is hard to believe that just over a week ago I had no idea what I was going to create and where to begin. I learned a great deal in a short time and feel proud of the finished product. Listed below are a few tips that helped me through this new experience that may be useful to others who are tackling similar projects. 

**Tip #1: Brainstorm ideas with your classmates **

After reading the project requirements, I started searching for an API based on advice from my instructor and the longevity compared to scraping. I spent a few days considering multiple options before decision paralysis fully kicked in. 

Lucky for me, a few classmates asked if I wanted to talk through ideas with them. It was without a doubt the most productive 30-minutes of my entire project. My classmates asked questions about the benefits of using one API over another, the documentation available, and the feasibility of each project idea. Hearing the perspectives of my peers helped me better evaluate the options I was considering and discover new ideas that I hadn’t thought of previously. I was able to make a decision that night (a CLI that allows users to find tennis centers within a specified location using a Yelp API), all thanks to the shared knowledge and encouragement from my classmates. 

If you’re not sure where to start, try asking yourself these simple questions:

-	What is a subject that interests me?
-	What is a hobby that I can gear the project toward?
-	What resource could provide me the information I am searching for?

Takeaways: 
•	Collaboration is powerful. Welcome ideas and lean on your team for support. 
•	Embrace a challenge. Even if you get stuck during a project, chances are you will gain valuable skills that you can apply to future projects and your career. 

**Tip #2: Learn about each folder in your skeleton **

What is a “skeleton” anyway? A skeleton is the “bare bones” that include the files needed for your program to run (i.e. Gemfile, Rakefile, CODE_OF_CONDUCT.md, LICENSE.txt, .gitignore, gemspec, gem_name.gemspec, lib/gem_name, lib/gem_name.rb, lib/gem_name/version.rb, etc.). I used Bundler to create my gem. Bundler conveniently provides a skeleton after installation, which was SO EASY. A few lines of code and voilà, you are ready to start coding!

Seeing my TENNIS_CENTER_GEM with all the files was really exciting, but I still had no idea what each of the files were for (I paid little attention to anything outside of the ‘lib’ directory prior). Before moving on, I reviewed the Bundler website and previous lessons to increase my overall understanding of each file. This minor step helped me decipher where to place requirements (and why) before developing my gem.

Takeaways: 
•	Use the tools available, such as Bundler, to save time and give you a head start. 
•	Take the time to return to previous lessons to help you retain knowledge. 
 
**Tip #3: Utilize pry**

Pry is an interactive shell that “freezes” or stops your program so you can see what is happening in your code. I used Pry consistently to figure out return values when pulling data from the API. I got so comfortable using it throughout the project, that I am wondering how I ever survived without it.

To use Pry, simply add it to your Gemspec with: spec.add_dependency “pry”

And, put require ‘pry’ at the top of any files you plan on using it. 

Takeaway: 
•	Stop and look at the code to help you pinpoint what is happening. 

Remember, the most important effort when undertaking a project is continuing to move forward. Whether you are picking an initial idea, learning what a gemspec is all about, or digging deep into your code with pry, each step gets you closer to the end goal. I am excited to keep learning and embrace the process, and I hope this advice helps you do the same. 

