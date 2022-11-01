---
layout: post
title:  "Art of testing - Part 1"
date:   2022-10-31 22:50:23 +0100
categories: [tests, unit-tests, integration-testing]
author: dnf
image: assets/images/artoftest.jpg
featured: true
comments: true
---

Why we don't want to write tests?, Why testing is important?,  Are the tests enough for us?

# Introduction

In this post series, I will try to discuss the key issues related to software testing. The topic is very wide, but I hope that I will be able to convince a few test-skeptics and show why it is worth testing your code.

![WindowsTerminal](/assets/images/pill.png)

So if you don't want to test and want to continue living under the illusion that your software is good without testing, take the blue pill and close this article. However, if you want to wake up and see the real world where your software has bugs, take the red pill and read on.

Remember that theses presented in the article are my personal opinion and do not apply to every possible situation - there are always cases that do not fit the described one and you must use common sense. I am also not a trainer or test evangelist who wants to present to you the same things that are repeated everywhere about tests - I am presenting here only conclusions from my experience.

# Dictionary of terms

Sometimes some terms can be miss leading or have many meanings so I will explain some below

- **Business** - we will use the name "business" to name the client for whom we carry out the project, it can also be called the client or sponsor.
- **Integration test** - we refer to it in the sense of integration testing in ASP.NET Core so it is an in memory test without real http requests. I will cover this in depth in next sections.

# Why we don't want to write tests

![WindowsTerminal](/assets/images/tellingme.png)

Before we go into the details related to the tests, I would like to deepen the topic of why we don't write tests? We hear everywhere that we HAVE to do it... right? As usual, there is a lot of psychology in this topic and it's mostly about our motivations. Below are the main reasons I found and mistakes I also made:

- **Who really wants the tests to be written?** - You or your supervisor? If we feel forced to do something, **we usually don't do it or we do it poorly**. I think this is often the case with tests - you just don't always feel the urge to write tests because it does not come from within you. Whatever activities we are doing in our lives **we have to be convinces that we have something in return** - instead we just drop it because our brain is always going to simplify and to use less energy. So we have be sure that we really want and understand return from investment in testing - **I will try to convince You**!
- **"Business does not want us to write tests"** because we do not have time for it - they want fast releases. The fundamental problem is that we should not discuss this with business. Business should not decide whether to write tests or to write clean code 
  > When business tells you what SQL to write You have to rethink your business relationships :) 

  To understand the subject better, put yourself in the role of business - if, for example, a painter comes to you and you order him to paint a wall and he asks you if he can do tests to work out the right shade and you will have to pay for it, you probably say - "I need the wall to be painted and I will not pay extra for any tests - I need the wall to be well painted."
  To sum up, customer needs are important, but how you ensure quality is your decision.
  The solution to this problem is simple - you have to provide a good quality solution and **tests must be integral part of the solution**, and you decide how long a given feature will take during implementation, including the time to write the tests - business has nothing to say here. Remember also that assertiveness counts. If you provide a solution without tests only to "satisfy" the business, you will start bad precedence - the next time the business will expect the same or shorter time, so you will not write tests again. So now the clock is start ticking and after some time there will be errors and you will be patching them again quickly because the next job is waiting, **so the circle will close** and you will end up with the code that you will gladly give to someone for maintenance. Finally, I would like to remind you one more thing - sometimes business just wants to announce victory and open champagne - they ask for help and of course You will (because You are so nice person!!) not refuse and help to achieve it by writing code that somehow works **and then you stay with maintenance hell and the business will have new goals and forgot tradeoffs** 

- We **lack knowledge of how to write good tests** - here I do not mean only the technical aspects of writing a simple test, because everyone can do it. Unfortunately, there are many aspects described below that make it impossible for us **to write good tests**. There are many guidelines on how to write tests, but good patterns and examples are often lacking. I will try to explore the topic further.

- **Bad environment** - sometimes it is also the fault of the people we work with. If we hear around that we don't need tests and everyone say bad things about testing, we justify ourselves not to write tests and join so called **"local anti test sect"** :) Event if we are urge to be test evangelists after some time we finally gave up because it is hard to be only one that is doing this. Have you ever found yourselves stop doing some thing like house cleaning because you share it with people making mess or stop obey rules on the road when you see other not doing this - maybe on exotic trip when you see that rules are not same as in your country and you simply go with the crowd? But we can use this psychological rule and mirror it - when we will create good environment and test hobbits it would be just hard for someone to brake our rules and he will obey the rules and go with the crowd!

- **"Tests cause problems"** - we don't want to write tests because we had problems with them before - "They kept crashing... with changes in the code", "half of the tests stopped working" - so there is assumption that they hurt us more than they help. That's what happens when we write tests poorly - in next sections I will try to explain how to do it better.
  
- **"Tests have never helped us so why waste time on them"** - this is what happens if we write wrong tests - we can call them trivial tests, that is tests that do not really test anything. For example if 2 = 2 which is always true. Of course, your tests have a slightly more complicated assert but after simplification they turns into such a condition.
  
- **You don't see long term results** - sometimes people calculates only short period of time and this results in wrong assumptions. If we are calculating ROI from test only in first development process we could drop it as unnecessary cost and in Excel it will look like optimization. But have a look at this chart below

  ![WindowsTerminal](/assets/images/belive.png)
  As se can see when we measure whole product lifetime we can see that test have real benefits. We just have to look on whole picture.

- **Laziness** - Is our last "sin". Sometimes we simply just do not want to write tests.. period. We don't have any quality gates to stop us from doing that. We say that we'll write the tests later, but... we'll never do that. Unfortunately, with unwritten tests it is like with this temporary solutions that we do at home - you put something under furniture and think "I will do it better tomorrow" and this "construction" can live for a few years ;)

  ![WindowsTerminal](/assets/images/test_production.png)

  Sometimes it is just to overcome the first barrier - but what it is? It is just some glueing we have to make in each project - create new project, prepare the test "scene" some mocks, helper code and so on. 
  > When we have some good test adding next one is simple

# Why testing is important

In the previous section, we mentioned the motivation to write tests. I would like to present a few reasons why you should write tests and what will you gain from them?

- **Peaceful sleep** - we often forget that we do not write the code that we throw out the door after the release, but we will have to keep it and maintain. In a week, month or year we will have to change something in the code we wrote and it will suddenly turn out that the code that was obvious to us while writing now is unreadable. And many times we were proud of many interesting "tricks" or "optimization" we made but after some time it is simply incomprehensible to us and **we are a bit afraid to change it**. However, if the code is delivered together with the tests, then by making the change, we immediately know if we broke something, and if the tests work, we can be sure that our change did not break anything. Of course, such certainty must be mature as well as the fact that we need tests, but it is worth considering!
- **Refactoring** - sometimes we need to change something in a critical place of our system, sometimes it happens that we have to do it not in our but someone else's code that we inherited. How many times have you met that no one wants to make changes to the code because everyone is afraid to do it - is it some old, very important piece of code that was written by someone who... doesn't work here anymore? And of course there are not tests to guard your changes. Your work looks like making a change in the engine running with a million screws - we remove one screw and pray that nothing explodes. **Tests provide stability and isolation** - thanks to well-written tests, we can introduce our change, run the tests and leave the prayer for another moment ;)
When we talk about refactoring, the term Red, Green, Refactor is often used. In short when we write a test to check the new functionality it must have a false result at the beginning, then we improve the implementation by writing the minimum code so that the test starts returning true, and then through refactor we improve the implementations while keeping the result true .

  ![WindowsTerminal](/assets/images/refactor.png)

- **Changing mindset** - when writing a production code, **our mind is often focused on the so-called happy path** - we think on how to go from point A to point B in simplest way. When writing tests, we switch to thinking "what if", thanks to which we start to look if there are any unusual situations - do we validate what the user enters? are all input value combinations supported? Thanks to writing tests, we are able to change our point of view and write code by looking at it not only through a happy path
- **Documentation** - if we have fully tested code for some functionality, it makes great documentation! If you do not like writing text on a wiki, this documentation may be enough for someone to understand how the code works (including you in a few months ;))
- **Better Architecture** - An often overlooked fact is that the code that contains the tests is also better written. Why is it like that? Because without testing our code is very easy to break and turn into a so-called **Big ball of mud** - we have many nested classes, generics, reflections but in the end we run and "it works" so we will "git push" and go to next task. Tests introduce something very important to our code - **simplicity**. We have to isolate the code we test, look at dependencies - in other words, **realize what they are**! In the end, our code is just clearer and testable.
- **They speed up testing** - but wait a minute, how does writing tests speed up testing? It is about a different type of tests - the so-called development tests, i.e. tests that we perform while writing a given functionality, including debugging. This can be done in many ways and I have done all of them in my career. Sometimes I created a separate project with buttons that fired a piece of library code that I used in the application, and sometimes I launched the main application and played a given workflow. Unfortunately, these approaches are expensive because the application may usually consist of more than one website, may require a database, authorization and other dependencies such as certificates, permissions, etc. Even if we prepare all dependencies, we must remember that every other developer will have to do it. Of course, we can somehow automate it, but there is one more issue - how long will it take to run such a test. For example - to run a website, we have to download a token using a certificate using some tool like postman and then using this token, we have to query the website ( which also starts a few seconds). The whole process can take couple minutes. What if we use integration tests? Someone may say that the preparation of the tests will take too long, but if we look at the matter in the long term - let's assume that writing the test will take us 120 minutes, for the sake of simplicity we will average one classic test flow to 5 minutes. So after 24 runs (24 * 5 = 120) we have the first results - of course this is a simplification because the integration test lasts a few seconds, so let's say 30. Why are integration tests so fast? Because the authorization layer (e.g. the token is turned off - we do not want to test it at this point - the tokens themselves have been tested by Microsoft and the authorization test can be done separately). 
  
Summarizing these points, I hope that you are slowly starting to notice that it is not others who should ask you to write tests, only you should need them

# Are the tests enough for us?
Tests are a double-edged sword - I wrote earlier about the fact that they provide us with the certainty that our code is of good quality, but this certainty can be deceptive, as I wrote in the [Pull Request article](https://dominikjeske.github.io/pull-requests/). We can ask ourselves - what is the metric that we have good tests? I will probably hear something about code coverage which unfortunately is not entirely true. Why is this so? Below is an example of a method that divides two numbers to which we added the test - we have full coverage, all green so should we be happy?

![WindowsTerminal](/assets/images/test_coverage.png)

Unfortunately, if we look closely, we can find situations that our test did not cover. These can be two kinds of problems

- **Technical** - i.e. those resulting from the language in which we write, such as NullReferenceException or those given below
  - DivideByZeroException when the second parameter will be zero
  - Loss of precision - the test did not show that, we lose the precision of the result by replacing double-> float-> double

- **Business domain** - what results from the specification we received from the business and there may already be many rules that should be checked. For example:
In our business, we only operate on positive numbers
The result should be rounded to two decimal places

So you can wonder if we need such metrics at all? They can be useful but only in one way, so as **Vladimir Khorikov** mentioned in his book "Coverage metrics are a good negative indicator but a bad positive one". 

>**Code coverage can show us that we have too few tests, but they will not show if we have enough of them.**

One could also ask whether we need these tests at all, since they do not give us certainty? - as always, the true is always in the middle. The lack of tests only means that we will live in ignorance because we do not check anything, so we should not cheat ourselves that we can get rid of them for our convenience. However, you cannot fall under the false belief that just by having "any tests" they will provide us with good code. But the more tests we will write, the more we will learn and even if we miss something, we will remember about it next time and include such cases in our tests - **we have to develop the test code just like application code**.
We also need to remember that we have different types of tests that are suitable for different situations - we will describe them in the next sections.

# To be continued

In next post I will cover different kind of tests and how to avoid some mistakes... 