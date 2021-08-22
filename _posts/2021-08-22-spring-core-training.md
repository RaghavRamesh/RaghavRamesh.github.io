---
title: "Spring Core Training by VMWare"
date: 2021-08-22
categories: [training]
tags: [spring, java]
---

Spring, the framework can be overwhelming for the uninitiated. Spring Boot, an additional layer, brings along even more magic with it through its auto-configuration and other features. Without a solid understanding of the fundamentals of Spring and Spring Boot, it can be hard to grok a standard mid-sized enterprise brownfield Spring Boot application. Until I took the time to learn the fundamentals of this framework, I found myself asking these questions: where are these magic beans being injected from? Why are there so many annotations on top of every class? This annotation is really just a wrapper on top of 3 other annotations - are you kidding me?!

Frameworks like Spring help by abstracting away boilerplate code so application developers can focus on business logic. But the sheer number of annotations and meta-annotations increase the number of classes and concepts to learn. And this combined with the pressure of product delivery timelines often means one doesn’t have sufficient time to learn each and every annotation in depth while maintaining a steady development pace.  In my case, MOOC courses helped with learning the framework but there wasn't sufficient hands-on training in the ones I tried. So when I came across the Spring Core Training offered by VMWare, I decided to spend the four days to attend it. I almost felt guilty for spending four days for this training, but I'm happy to say that in hindsight, this training was worth it. 

![Spring Core Training Certificate](/assets/img/posts/spring-core-training/VMWareSpringCore.png)

[The training](https://mylearn.vmware.com/mgrReg/courses.cfm?ui=www_edu&a=one&id_subject=94106) focused on the Spring framework for the first two days and Spring Boot for the next two. Throughout the training, there were labs after almost every theory section which helped in imbibing the concepts.

Before the training started, the trainer asked us to list what we wanted to get out of it. Like many others, my primary objective was to understand how and what Spring does for the application developer. Two parts of the training helped achieve this objective: first was the lesson that was aptly titled “Inside the Spring container”. It pulled the curtains back and revealed how a Spring Bean lifecycle works, how Spring proxies add additional behaviour at runtime (a.k.a. The magic), how Spring determines the bean creation order and some slightly advanced topics like how to use the `BeanFactoryPostProcessor` and `BeanPostProcessor`. I was told this was not covered in the unofficial courses and training.

The second part, which was also the one I found the most helpful, were a series of labs that were based on a practical example with a real-life use case. It was not just some contrived example as is usually present in poorly crafted tutorials. These labs taught dependency injection, the core principle behind Spring’s IOC container starting with POJO configuration (i.e., how one would instantiate classes without Spring), followed by Java configuration (i.e., how one would manually create Beans and inject them via constructor, setter and field injections), and finally, the same example using annotation configuration (i.e., how Spring automatically creates beans on our behalf and injects them for us). This step-by-step transition helped me to develop the much-needed mental model required to understand how Spring uses design principles like convention-over-configuration and separation of concerns to keep our application code lean and free from configuration code. 

I considered everything else I learned as bonus. And boy were there so many bonus items! I won’t list all of them, the training agenda does a good job of it:
- An excellent opportunity to read and understand how to use native Java patterns like reflection, proxies, sub-classes and annotations.
- The hands-on labs were particularly useful in gaining experience in aspects of backend programming I hadn’t worked on before such as JPA, JDBC and database transactions
- I jotted down some Java interview questions which are now quite handy as we are in an aggressive recruiting phase
- And a bunch of byte-sized concepts that I formed a rough understanding of while programming but didn’t spend the time to read the reference to understand completely. So there were many “Oooh so that’s how this works!”-moments. 
- Finally, attending this workshop and listening to lectures and advice from the Spring subject matter expert inspired me to consider becoming a trainer of some technology in the future.

As software engineers, we are expected to learn on-the-job. But harsh delivery timelines push us to adopt an approach of learning the bare minimum required to complete the user story at hand. From my experience though, when it comes to learning a new framework, it’s better to invest some time upfront to learn the fundamentals. In retrospect, I am glad I took the time off to attend this training. I have a deeper appreciation for Spring, Java and backend programming in general.
