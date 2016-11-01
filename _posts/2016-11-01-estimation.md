---
layout: post
title: "Estimation"
date: 2016-11-01
---

All developers are asked some variation of this frightening question - *How long will you take to finish this?* or *By when can you finish this?* or something else. It's frightening because it's tough to consistently deliver within the estimate.

Why?
There is a lot of uncertainty when a [user story](https://en.wikipedia.org/wiki/User_story) is defined. There are multiple variables involved in the estimation process. Yet, you, the developer, are expected to make a commitment. If you haven't already realised that you're not alone, it's worse because you would be blaming yourself and thinking you’re incompetent when you don't finish it by the given estimate.

But estimates are guesses, and sometimes they can go wrong.

But if you don’t stick to your estimate, you probably need to give an excuse during daily scrum, then your team member will say - *Looks like I'm blocked, let me see what else I can work on.* Gah! That's not the best thing to hear especially if your daily scrum is at the start of the day.

Not just that you are haunted by your previous experiences of giving an optimisitic estimate and ending up taking around twice as much time to complete the story. Why? Because in our eagerness to impress our supervisors, we tend to over promise. I’ve done that in my first internship only to be humiliated in front of the entire team including two new junior interns.

But face it, estimates are not going away. If you’re lucky your product manager will empathise. But that’s it. There are business requirements and your job as a developer is to make your product manager’s job easy. But it's not all bad; it only gets easier with experience, so hang in there.

You will naturally get better at evaluating the time taken for tasks as you ship more stories. But it's not absolutely necessary to have your share of failures of exceeding estimate values in order to get the hang of estimation. I recently read a book the [Clean Coder](https://www.amazon.com/Clean-Coder-Conduct-Professional-Programmers/dp/0137081073) by Uncle Bob, which had an entire chapter dedicated to estimation.

{: .center}
![Probability Distribution](../../../../images/distribution.png)

{: .attribution}
*Source: https://goo.gl/vxKpnj*

Uncle Bob says that estimation is actually best explained using a probability distribution graph.

 * If everything goes as planned, you could probably complete the story in 2 days.
 * The highest probability would be a nominal estimate, 3 days.
 * If multiple things don't go as planned, there is even a chance of the story taking 10 days to be completed - pessimistic.

Woah, hold on there Ragi, this graph looks all nice and dandy, but I can't get back to my product manager with a graph everytime he asks me how long I will take to finish something, right?

Well, first of all, don't call me Ragi. Secondly, yes you can't get back with a graph. But there is a practical solution that can be interpreted from the graph. If you observe the above graph keenly, you can infer that there is a 50% chance that you can complete the story in 3 days, and a 90% chance that you can complete the task in 5 days. This is called the 50-90 approach. You can do yourself a favor by not sticking to a hard deadline due to the uncertainty involved and get back with two reasonable values.

One another solution is to have a [Planning Poker](https://en.wikipedia.org/wiki/Planning_poker) session at the start of a sprint to estimate tasks together as a team. When multiple developers are involved in estimating tasks, you will feel more confident in evaluating the estimates of stories and it also gives you an opportunity to explain the reasoning behind your estimate.

An objective approach on things to consider while estimating a story or a task is given in [this link](http://softwareengineering.stackexchange.com/questions/648/how-to-respond-when-you-are-asked-for-an-estimate).

Estimation is hard because it's hard to predict the variables that may affect your work in the future. But you can definitely take steps from your end to become better at evaluation.

How do you deal with estimation? Do you have any useful tips? If so do share them with me by commenting below!
