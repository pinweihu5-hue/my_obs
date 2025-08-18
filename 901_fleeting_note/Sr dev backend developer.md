---  
alias:  
creation_date: 2024-07-07 17:50  
tags: 
related:
[[777_career_idx]]

---  
ideas abt what senior Nodejs/backend dev does
- [ ] [nodeJS senior - Reddit Search!](https://www.reddit.com/r/node/search/?q=senior&type=link&cId=f1fd8454-e50b-4600-8347-26a65d82a4d5&iId=1f9b140d-5c8f-4576-9fe7-023b87c40083)
- [ ] [BE senior - Reddit Search!](https://www.reddit.com/r/Backend/search/?q=senior&type=link&cId=dc40c76c-e9f0-44bb-a8e0-6e5d77005572&iId=56ea7214-b563-4aee-87b0-d1ece01522d3)


```

you gonna touch devops stuffs, docker, k8s, cloud, cicd, vpc network stuffs, linux system, dbs, tunning stuffs, design system, ...


Testing. CI/CD. Spec gathering and customer babysitting. Application and system observability.

The more senior you get, the more you need to learn soft skills, but also high level architecture and the “boring” stuff that makes for a stable system.



Making a stable system can be the most interesting. I've recently had to re-engineer a system designed for 100k events/day to handle 100m. So many intricate changes, like having to move from batch processing to stream processing almost everything to avoid blowing up the memory usage.


Testing Vitest.

CI/CD Github Actions

Spec gathering and Customer Babysitting ( Hit the gym, hire a personal trainer to talk about this things in plain terms, if he/she (whatever gender-bender it identify) get it, everyone can get it, this is in fact what I do).

Application and system observability (If you have resource to share, gladly to take a look at it)



Observability usually comes from the platform it's running on. AWS custom metrics are worth understanding if you need specific things they don't offer by default.

Error logging and aggregation using something like Sentry is useful to know too. A big problem is alerting too much, meaning people ignore the big things when they happen.



One thing I'm surprised a lot of folks don't know, even senior ones, is how to deploy an app on your own in it's entirety. Like, not just the code, but the infrastructure.

Then the next skill you'll need, is how to build something people will use. But that's a whole new skillset.




If you're not much into automated testing, that's probably a big win.

Getting "good" with observability and evolving robustness (I.e. making use of what you observe) was what took me from mid to senior. This did involve having to learn a bunch of Cloud (AWS in my case) stuff too that supports this.

I'm talking good logging that actually helps you solve problems, usful alarms, not just crap that makes noise and panic, error tracking, automatic retries, dead letter queues etc, etc.

I think another often overlooked element of improving (as we often look at how we the individual can improve) is being the team player in your squad. Do you know stuff that others don't? Teach them, then not only are you upskilling others, but you've got a helping hand for next time that thing crops up. Enable others and be helpful and considerate, just cos you could knock out a feature in an afternoon, doesn't mean Billie with half your experience can.



===

I generally see it as a combination of hard and soft skills. The engineering skills follow a rough path:

Language syntax

Common patterns (Mvc, dependency injection)

Standard libs (fs, child_process,...)

Testing (unit to e2e)

UI , CLI, Server work

Databases

Multiple languages

Build & deployment

Infrastructure & Distributed systems

Basically you start narrowly focused on using a single language, mastering it, using a bunch of different patterns, learning all matter of testing, and using it in different contexts. Then taking time to learn other languages to appreciate the pros/cons of language designs, and finally moving to really understand the underlying dependencies of shipping software. With this you have formed an opinion of all these areas, but acknowledge people specialize and will be more knowledgeable on a single subject. Learn from them.

Soft skills:

Continuous learning (everything above)

Teaching / mentoring (everything above)

Interviewing / Hiring

Understanding the implications of engineering decisions on the business

Focusing on ‘people problems’ not software problems (picking tech and patterns with people in mind first)

Empathy for engineers, non engineers, and users

Ability to foster a supportive collaborative environment

You know all the tech well enough to ship something, but you’ve realized you can’t do it alone. So you focus on learning how to develop a group of people to get where you’ve gotten. You are trying to multiply yourself and focusing on others more than yourself.





As someone trying to hire a senior node dev, it's a combination of hard and soft skills. Ideally an "expert" is someone you hire not only to write code, but to teach others and elevate the quality of your whole team.

Hard Skills:

Promises/asynchronous patterns should be no issue

Experience will a well adopted frameworks, like Express

Knowing how to build and publish npm packages (my company does a lot of opensource work)

How to set up unit/integration testing

Connecting to and efficiently querying databases

Able to quickly evaluate and test new tools to integrate into current systems

And, most importantly, good JS debugging skills. Definitely the most important.

Soft Skills:

Able to mentor other devs

Able to work with product teams at your company to determine priorities/write tickets

Able to estimate the time it'll take to complete a task accurately (this one is so hard)

And if your company does open source work, able to be a good representative of a project in the OSS community.

It's honestly a lot to ask for, and could be TOTALLY different if you're going to work at a big corporation where you don't need to really interact with anyone outside of your dev team. In the end, the biggest thing I look for when hiring is the debugging skills and then the excitement to keep learning/trying new tools.

```



