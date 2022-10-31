---
layout: post
title:  "Art of unit tests"
date:   2023-10-30 17:41:23 +0100
categories: [tests, unit-tests, integration-testing]
author: dnf
image: assets/images/artofwar.jpg
featured: true
comments: true
---

In this post, I will try to discuss the key issues related to software testing. The topic is very wide, but I hope that I will be able to convince a few test-skeptics and show why it is worth testing your code.

# Introduction

In this post, I will try to discuss the key issues related to software testing. The topic is very wide, but I hope that I will be able to convince a few test-skeptics and show why it is worth testing your code.

![WindowsTerminal](/assets/images/pill.png)

So if you don't want to test and want to continue living under the illusion that your software is good without testing, take the blue pill and close this article. However, if you want to wake up and see the real world where your software has bugs, take the red pill and read on.

Remember that theses presented in the article are my personal opinion and do not apply to every possible situation - there are always cases that do not fit the described one and you must use common sense. I am also not a trainer or test evangelist who wants to present to you the same things that are repeated everywhere about tests - I am presenting here only conclusions from my experience.

# Dictionary of terms

Sometimes some terms can be miss leading or have many meanings so I will explain some below

- **Business** - we will use the name "business" to name the client for whom we carry out the project, it can also be called the client or sponsor.
- **Integration test** - we refer to it in the sense of integration testing in ASP.NET Core so it is an in memory test without real http requests. I will cover this in depth in next sections.

# Why we don't want to write tests

![WindowsTerminal](/assets/images/tellingme.png)

Before we go into the details related to the tests, I would like to deepen the topic of why we don't write tests? We hear everywhere that we HAVE to do it... right? As usual, there is a lot of psychology in it because it's mostly about motivations. Below are the main reasons:

- **Who really wants the tests to be written?**You or your supervisor? If we feel compelled to do something, we usually don't do it or we do it poorly. I think this is often the case with tests - you just don't always feel the urge to write tests because it does not come from within you. I will try to change it in the next part.
- **"Business does not want us to write tests"** because we do not have time for it - the want fast release. The fundamental problem is that we should not discuss this with business. Business should not decide whether to write tests or to write clean code 
  > When business tells you what SQL to write You have to rethink your business relationships :) 

  To understand the subject better, put yourself in the role of business - if, for example, a painter comes to you and you order him to paint a wall and he asks you if he can do tests to work out the right shade and you will have to pay for it, you probably say - "I need the wall to be painted and I will not pay extra for any tests - I need the wall to be well painted."
  To sum up, customer needs are important, but how you ensure quality is your decision.
  The solution to this problem is simple - you have to provide a good quality solution, tests must be part of the solution, and you decide how long a given feature will take during implementation, including the time to write the tests - business has nothing to say here. Remember also that assertiveness counts. If you provide a solution without tests only to "satisfy" the business, you will start bad precedence - the next time the business will expect the same or shorter time, so you will not write tests again. So now the clock is start ticking and after some time there will be errors and you will be patching them again quickly because the next job is waiting, **so the circle will close** and you will end up with the code that you will gladly give to someone for maintenance. Finally, I would like to remind you one more thing - sometimes business just wants to announce victory and open champagne - they ask for help and of course You will not refuse and help to achieve it by writing code that somehow works **and then you stay with it with maintenance hell and the business has new goals** (business shoots the programmer wears balls (smile))

- We **lack knowledge of how to write good tests** - here I do not mean the technical aspects of writing a simple test, because everyone can do it. Unfortunately, there are many aspects described below that make it impossible for us **to write good tests**. There are many guidelines on how to write tests, but good patterns and examples are often lacking. I will try to explore the topic further.

- **Bad environment** - sometimes it is also the fault of the people we work with. If we hear around that we don't need tests and they are hated, we justify ourselves not to write tests

- **"Tests cause problems"** - we don't want to write tests because we had problems with them before. "They kept crashing... with changes in the code half of the tests stopped working" - so there is assumption that they hurt us more than they help. That's what happens when we write tests wrong and in sections I will try to explain how to do it better.
  
- **"Tests have never helped us so why waste time on them"** - this is what happens if we write wrong tests - we can call them trivial tests, that is tests that do not really test anything. For example if 2 = 2 which is always true. Of course, your tests have a slightly more complicated check but, after simplification, sometimes turns into such a condition.
  
- **You don't believe in tests** - sometimes we are "hurt" by a bad experience with tests or we just don't see anything in them that will help us. It all depends on how "good" tests we will have in our application - below is a graph of what it looks like from the book "Unit Testing Principles, Practices, and Patterns"

  ![WindowsTerminal](/assets/images/belive.png)

- **Laziness** - Is our last "sin". Sometimes we simply just do not want to write tests.. period. We don't have any quality gates to stop us from doing that. We say that we'll write the tests later, but... we'll never do that. Unfortunately, with unwritten tests it is like with this temporary solutions that we do at home - you put something under furniture and think "I will do it better tomorrow" and this "construction" can live for a few years ;)

  ![WindowsTerminal](/assets/images/test_production.png)

# Why testing is important

In the previous section, we mentioned the motivation to write tests. I would like to present a few reasons why you should write tests and what will you gain from them?

- **Peaceful sleep** - we often forget that we do not write the code that we throw out the door after the release, but we will have to keep it and maintain. In a week, month or year we will have to change something in the code we wrote and it will suddenly turn out that the code that was obvious to us while writing now is unreadable. And many times we were proud of many interesting "tricks" or "optimization" we made but after some time we is simply incomprehensible to us and **we are a bit afraid to change it**. However, if the code is delivered together with the tests, then by making the change, we immediately know if we broke something, and if the tests work, we can be sure that our change did not break anything. Of course, such certainty must be mature as well as the fact that we need tests, but it is worth considering!
- **Refactoring** - sometimes we need to change something in a critical place of our system, sometimes it happens that we have to do it not in our but someone else's code that we inherited. How many times have you met that no one wants to make changes to the code because everyone is afraid to do it - is it some old, very important piece of code that was written by someone who... doesn't work here anymore? And of course there are not tests to guard your changes. Your work looks like making a change in the engine running with a million screws - we remove one screw and pray that nothing explodes. **Tests provide stability and isolation** - thanks to well-written tests, we can introduce our change, run the tests and leave the prayer for another moment ;)
When we talk about refactoring, the term Red, Green, Refactor is often used. In short when we write a test to check the new functionality it must have a false result at the beginning, then we improve the implementation by writing the minimum code so that the test starts returning true, and then through refactor we improve the implementations while keeping the result true .

  ![WindowsTerminal](/assets/images/refactor.png)

- **Boundary conditions** - when writing a production code, **our mind is often focused on the so-called happy path** - we think on how to go from point A to point B in simplest way. When writing tests, we switch to thinking "what if", thanks to which we start to look if there are any unusual situations - do we validate what the user enters? are all input value combinations supported? Thanks to writing tests, we are able to change our point of view and write code by looking at it not only through a happy path
- **Documentation** - if we have fully tested code for some functionality, it makes great documentation! If you do not like writing text on a wiki, this documentation may be enough for someone to understand how the code works (including you in a few months ;))
- **Better Architecture** - An often overlooked fact is that the code that contains the tests is also better written. Why is it like that? Because without testing our code is very easy to break and turn into a so-called **Big ball of mud** - we have many nested classes,generics, reflections but in the end we run and "it works" so we will "git push" and go to next task. Tests introduce something very important to our code - **simplicity**. We have to isolate the code we test, look at dependencies - in other words, **realize what they are**! In the end, our code is just clearer and testable.
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

# Types of tests
We can test in various ways and it good practice to have naming conventions to distinct them. The naming conventions are important because they introduce a certain constraints.
> You can find more about it in this podcast https://www.dotnetrocks.com/?show=1542

Below we highlight different kind of tests with suggested name suffix that you can change in your team. It is worth mentioning that you don't always want to run all of the test in your Continuos Integration pipeline because they take to long to run. This is why I made a suggestion about this in brackets.

- **Unit Tests** [.Tests] - are tests that allow you to test a single functionality in isolation from other program elements that do not require any external resources to be executed, such as connection to the database or file system. (Run in CI - true)

- **Integration tests** [.IntegrationTests] - this is how we can test in ASP.NET Core.  We mainly test API controllers here. Those test are executed in memory and request to controllers from http clients are replaced by in memory execution - all external dependencies are blinded (connection to the base, redis, etc.) (Run in CI - true)

- **System tests** [.SystemTests] - Some components are mocked and we test a specific scenario in systems A and B. We only test the integration between A and B in a given specific scenario. (Run in CI - true, only when we can prepare clean isolated environment for tests)

- **End to End tests** [.End2EndTests] - Tests involving sending real HTTP queries to controllers - nothing is mocked (Run in CI - false)

- **Performance tests** [.Benchmarks] - The so-called microbenchmarks, i.e. testing the code with BenchmarkDotNet libraries (Run in CI - false)

- **End to End performance tests** [.PerfTests] - Load tests our website with a large number of inquiries (Run in CI - false)

- **Support libraries** [.Testing] - Shared testing code reusable between test projects (Run in CI - false)

We can also talk about UI test but this is outside the scope of this article. We also can extend our test suite with contract tests or other kind of test suitable for our solution. 

The tests differ from each other in what part of the system we test - the smaller it is (UnitTests), the faster it will be performed and we can write more such tests. However, the problem with such tests is that they usually have some dependencies that are usually replaced with mocks, stubs, and so on. The more such elements we introduce, the greater the probability that our dummy will be the source of the problem in itself, because they do not reflect how the element works, but only in the specific one we are now considering, which may lead to a situation in which we overlook something.

We must remember not to run all tests in a CI loop - tests that last a few minutes are better placed in a separate process (for example, run once a day or before release) so that they do not slow down our work. Therefore, it is important to name the tests appropriately, thanks to which we will be better able to automatically run a certain class of tests.

It is worth mentioning the test pyramid, which looks like this

![WindowsTerminal](/assets/images/piramide.png)

However, one thing needs to be mentioned - generally we should have the most unit tests, then integration, system and UI tests. For example having 100 different test cases, it is better to test the logic in isolation (Unit tests) and test the controllers using integration tests in happy path - we test that Dependency injection works, everything starts (integrates) and the controller responds - instead of 100 times "shooting" the controller because it will take time. But... remember that our code is not always written by the book and we can have different kinds of our applications. If, for example, our controller is a simple bypass which accepts a reqest and calls another service (without any logic) or is a typical CRUD, what can we test in our code? We have only mapping and data validation because there is no business logic there. We can also write infrastructure code (such as logging) - if we have such code, we may care more about integration or E2E tests and our triangle can be set up side down.

In the following sections, we will mainly focus on unit and integration testing.

# Testing basic concepts
When writing tests, you should know some basic concepts that are used in testing.

- **AAA (Arrange, Act, Assert)** - we usually divide the test into a part in which we prepare the tested service (often called SUT - System Under Test), then perform the tested operation and finally check whether the result fill requirements. Such a layout is not always required but is a common practice and as we wrote in the case of naming test projects, standards are very helpful. Let's also try not to write comments like below how we divide our test - usually divisions with new lines are enough for this - comments are helpful in examples like this to show it to new people. Usually, the Arrange phase is the most complicated and we will talk about it later, the execution and assertions are not that complicated

  ![WindowsTerminal](/assets/images/aaa.png)

- **Stubs / mocks / dummies**
The subject of broadly understood mocks is described in many places, so I will not elaborate on them here - I will only talk about the most important matters. It is very rare that the class we are testing has no dependencies - if this is the case, it is always worth asking yourself whether we have divided our code properly. It is worth verifying whether our class meets the Single Responsibility rule.The principle of Single Responsibility tells us that the code has one responsibility and therefore deals with one problem - if it is not, we should divide it and separate dependencies. For example, logging should be implemented outside our class and we should use the ILogger interface. In addition, if our class is simply too big in terms of the number of lines, then you also have to think about whether it is doing too much?

  >Making a partial class and breaking it down into many files is a scam, not a division

  An example class looks like this

  ![WindowsTerminal](/assets/images/dependencies.png)

  we can pass all the dependencies in the same way as in the production code or prepare a mock/stub - we will discuss when to do it later and now we will focus on the mechanism itself.

  >Interfaces are not the only way to change the implementation - we can also depend on a class with virtual methods that we overload in the test code

  Without crushing the difference between the different types of our dummies, let's remember mainly this - if we want to return the test vale, we have a Stub, and if we also want information about whether or how many times a given method has been performed, we are dealing with a mock. The nomenclature is important because if we call something a mock and someone expects a real mock, you may be surprised, so let's pay attention to it. To create our mocks, we can use a ready-made Moq implementation, but before we reach for it, let's think if we really need a "cannon to shoot a fly". Perhaps it will be easier to make such a simple stub yourself as below. The advantage of this solution is that it is reusable.

  ![WindowsTerminal](/assets/images/stub.png)

- **Test Data Builder Pattern** - In tests, the so-called Test Data Builder which, using the builder pattern, allows you to prepare an object of a given type. Pattern is very simple - we prepare methods by convention starting with **With** the name of which reflects the data that we want to replace in the test. Finally, we call the Build method and get an object of the given type. Often in such a builder we create instances of the stubs shown above or use Moq to configure the mock.

  ![WindowsTerminal](/assets/images/builder.png)

  an example of a builder using moq can be found here

  TODO

# What a good test should be
Well-written tests will bring us a lot of benefits, but for that to be the case, we must avoid mistakes while writing them

## Stable
A stable test is a test that for the same test conditions always get the same result. Unstable tests cause a lot of problems - or we ignore them and repeat them again in the hope that "now maybe it will work". After some time, we finally turn off the test to "later" correct it, which often does not happen.

  >Turning off individual tests or entire projects is something we should avoid, but it happens when tests get out of control

There are many reasons why we may destabilize our tests - I will try to list the main ones

- **Our tests are not isolated** - what does this mean? This means that the test method depends on something globally shared. For example, if we use static / property fields anywhere in the test, we ask for problems. We must remember that more and more often tests are performed in parallel, so if we do not have control over the order, sometimes we will be lucky and the state we set is as we expect, but it may be that some other test will set our state to another. Each test should have "its own world" and the tests should not affect each other (including the rule that they should not depend on the order of execution) - we ensure this by avoiding splitting the state through static variables or other ways like files written to disk. Below we can see the use of the shared variable, which in unfavorable conditions can be set in the second test before the first one has time to use it, so Test1 sets it to 10 but before the Calculate Test2 method is executed it will set it to 20 and then our test will return an error.

  ![WindowsTerminal](/assets/images/isolation.png)

  Another reason may be, for example, access to the database - if we have a test that operates on a database, we must ensure that each test either works on a clean database or the previous one is cleaned up after itself (we can also work in a transaction) - the topic is quite big and I will not be here discussed extensively. If anywhere in the test we refer outside of the test - that is, to a static variable, file or any other place that the tests can share, you may have a stability problem.

- **External dependencies** - If we use an external dependency in our test - for example a database, cache or WebApi service that we have no control over when running the test, then one day our tests may stop working suddenly because someone is restarting or updating. We cannot rely on such dependencies in our tests
  
- **Testing internals** - A common mistake while performing tests is testing things that should not be tested. Below we see a service that calculates a certain algorithm - we have the main method Calculate which is a contract that is called by someone using our class, but the Step1 and Step2 methods are an implementation detail that may change in the future even if the main method returns the same result. Perhaps we will want to get rid of these steps and write the algorithm differently, but then all our tests will have to be improved. Never test the implementation details as they will block you during future changes. Even if the methods are private, sometimes we still test them using reflection - **if you start to think about it, think twice!**

  >When designing your classes, **always hide private methods for the class using the private or internal keyword**. Look at your class like car parts - you wouldn't let the "fuel level" variable be set from the driver's seat?

  ![WindowsTerminal](/assets/images/internals.png)

  >**Tests should cover all external contracts** - remember that a contract is not only about input and output parameters. These are also exceptions and what you write to disk (unless they are an implementation detail that may change).

- **Unstable dependencies** - Another problem is dependencies that are not completely stable. We expect different values ​​from variables such as GUID, but there are elements such as, for example, DateTime.Now time dependencies. They cause a lot of "fun" and introduce a lot of nondeterministic cases. For example, if we use DateTime.Now to get a date and then do something with it, we may be surprised because at certain times or dates it suddenly turns out that our test stops working. Maybe, for example, we have a leap year and February suddenly has a different number of days or we get the date twice (before and after midnight) in same test - there can be a lot of various strange cases, but it's best to just avoid such a dependence and use an interface everywhere like: TODO
so we will be able to set the date and time we want and our test will always work the same. An example of use can be found here. TODO

- **No "Single Responsibility"** - We've mentioned this principle before and it also affects the stability of our tests. In an ideal world, when you make a code change that changes the contract guarded by our tests, one test should stop working. Of course, even with a well-divided code, we will not always have such ideal situation, but it is often worse and a single change causes our application to break in many unexpected places. Half of the tests stop working and we are waisting whole day correcting the problem. The reason for this may be that we have created the so-called big ball of mud, so the code in which the so-called super classes (or "god class") for everything, so they contain all the logic of the application, infrastructure, logging and whatever we have in our application - at first it seems okay because we have everything in one place, but such code causes only problems. The main reason is that we do not have separate responsibilities and they permeate all elements of our application - so a change in one place causes a change in 100 others. An interesting experiment will be if we start to separate parts of the application responsible for specific applications - it turns out that what previously influenced this separated element has nothing to do with it. Below is an example of a class that uses the configuration parameter - what happened here?

  ![WindowsTerminal](/assets/images/bibball.png)

  Someone in the MakeCoffe method uses the same parameter as in the Calculate method - what will happen if we change this parameter - delete it or rename it? Suddenly changing the calculation of some algorithm we can't make coffee - sounds funny? Now we can laugh, but if we make more places in the code that will not be so obvious, and then for a few months our code will "overgrow with a mold", because this is what happens with this type of code - you add, add previously shiny new code is changing to ugly one, then we are afraid to change something because we don't know what a given parameter does, so it's better to leave it. Often the changes may not show a compilation error (such errors are great because they are easy to catch) worse when something changes in the runtime. How to prevent problems? Separating classes with their own responsibilities and naming everything better - so without generic names like algorithm (algorithm for what exactly?), Parameter called "a" and meaningless method names.

  ![WindowsTerminal](/assets/images/circle.png)

  Such code will be better for us to test immediately and changes in Circle will not affect changes in Caffe, so we get a stable code

- **Mixing business code with infrastructure code** - A common problem also related to singe responsibility is creating a business code (so-called business domain) mixed with infrastructure code, so somewhere in our domain our code has to do with http headers, ASP controllers, WCF code, etc. What are the consequences? Unfortunately, there are many of them - starting with the fact that our code is so related to technology that it is very difficult to upgrade later (if someone was porting code from WCF, he knows what I'm talking about). In addition, some changes in the infrastructure code affect our business domain, which causes the instability we write about. One of the ways to isolate from the infrastructure in ASP.NET Core controllers is to use a mediator, thanks to which in one WebApi project we have code related to the ASP infrastructure, and in the other we have pure business code. There should not be any specific HTTP related codes in the app project - due to the fact that it is a separate project, we can not reference ASP at all, so it will be easier for us to ensure that the infrastructure does not leak into the business code.
  > **It is a good idea to use a compiler to guard our design**. If it shows an error, it will block us - so if we do not have, for example, a reference to ASP, no one will use it even by accident in the future. This is important because bugs do not appear immediately, but are the result of long-term maintenance, sometimes by subsequent developers. The best way is to prevent such situations so that we do not have to remember.

  ![WindowsTerminal](/assets/images/mixbusiness.png)

  The general principle derived from onion/clean architecture is that in our business domain we communicate with the outside world through adapters, i.e. interfaces in the case of c#. Our business code needs to see the outside world only through abstractions. The same applies to how the outside world refers to our business code - If the infrastructure code calls the domain code, it should not pass any objects from "its world" there, only the POCO class (plain old CLR object).

  A common problem with passing something to a method is that instead of passing in a parameter what the method needs, we pass a huge class there that is not further used outside of one field.

  If we isolate these two worlds well, our code will be easier to maintain.

  ![WindowsTerminal](/assets/images/hexagonal.png)

- **Too many dependencies** - previously we wrote about the separation of dependencies, but there is one problem with that. If we have the code as above which contained the pronunciationsElements mixed with each other that should not be put into one class at all is the separation of dependencies and using them in this class will not change much. We can end up with a class like this below

  ![WindowsTerminal](/assets/images/manydependencies.png)

  Then we often catch our heads - how can I test it - plug everything? And if everything locks in place, what will I actually test? The answer is simple - most likely this class should be split - if something has too much dependencies it probably does too much!
  For those interested in the topic of a good division of responsibilities, I suggest you read the Bounded Context topic from DDD

- **You test too much in one test** - sometimes we put many arrange, act, assert sections in one test, which is not entirely correct. The test should check one scenario and if we have too much in it, we blur what is really being tested and if the test stops working, it is also not clear what is the reason. A small change can "crash many tests" if they are too related and we test "too much" in all of them.

- **Side effects and functional code** - the simplest code to test and maintain is the functional code, i.e. the code that takes input parameters and returns the result without the so-called side effects. What are they? It can be writing to a file or throwing an exception which complicates our code. When it comes to throwing exceptions, this is a broad topic, but the basic rule is **not to use exceptions to control the flow of your application**. Many times, for the sake of convenience, we simply throw an exception to ourselves, which unfortunately complicates the handling of our code later because an uncaught exception may even cause our application to close. Everyone needs to know what exception can fly out of our code (which, of course, we often do not document), so our contract, which is a set of output and input parameters, becomes very complicated. Below we can see two methods - the first instead of always returning a result throws an exception, the second returns the Result type (from excellent library https://www.nuget.org/packages/CSharpFunctionalExtensions/) - in the second case we have full behavior in the contract and the calling person expects that he must check result, but a wrong result will not cause our entire application to stop working.

  ![WindowsTerminal](/assets/images/sideeffect.png)

  And what about other side effects - it is not always possible to eliminate them, but sometimes you can delegate this responsibility to another place - for example, if our code would only send an event that something happened and another code will have to read it and save to a file, then responsibility of our code is to send the event. We can also leave the operation to the caller of our code - **maybe it's not our responsibility to write something to the file**?

## Quick
Tests should run quickly - what does that mean? We can have hundreds of them in a project, so if each one would execute a minute, we would have an hour for 60 tests. In most cases, a unit test should take less than a second - it is difficult to give exact values, but if the test takes more than a second then you should look at it.
> During the warm-up, the first tests are slower, so it is not always the fault of the test itself

What mistakes are we making here?

- **Time dependencies** - time flows normally during production, so sometimes we have to wait seconds or minutes for an event, but during testing we do not / should not wait that long. The best solution is "time compression" - we are interested in the events that occur in some order, but what will happen between them does not matter to us, so instead of waiting, just manually trigger these events in the right order one after the other.
If someone used Reactive Extensions, this method of testing is the default option there - we define a virtual axis of events and say which event is to be triggered, but the test itself lasts quickly, even though the events are even a few hours apart

Typical time dependencies:

  - **Timers** - Sometimes we have a timer in the code and we use it in a test in the same way as in production, so we set, for example, a time of 10 seconds and wait for what happens for ... 10 seconds; (All external dependencies should be extended beyond our service and timer is such a dependency - it depends on the system clock (i.e. there are many kinds of timers, but we simplifying). In this case, we should have such a timer as a dependency TODO
  so we can set it up TODO

  As you can see, we created a stub of our timer and in the following states in which our sut is located, we start our timer. We can say that we have flattened the time, so instead of waiting for it to last, we allocate time points and activate our trigger.

  - **Waiting for Task** - sometimes we wait for some Task's result, but there is a way to do it differently using TaskCompletionSource - thanks this is what we are able to return a value (mark Task as finished) similar to what we showed in the case of timers: TODO

  We should also remember that sometimes we abuse Tasks or even "hide" them in logic so that they are not visible. It is best to leave the creation of Task to the executor of our code - then our code becomes simpler and easier to test and we "shift" the multithreading to another place. If the code is to be executed in parallel, someone else will decide. Below are the first two examples of bad use of Tasks that can be replaced as shown at the end

  ![WindowsTerminal](/assets/images/taskwait.png)

- **Referring external resources** - if instead of mocking some external resources, such as a database, each time we refer to it, our test takes longer. We have to consider whether we really need to refer to this resource instead of mocking it for the purpose of testing our class

## Easy to maintain
We often focus on writing the test at a given moment, but we don't think about maintaining it. This is related to another problem - we do not treat the test as an integral part of the solution. Tests should not be an unwanted addition that we add - it should be an integral part that we care for, as well as the production code. All other techniques like clean code should apply here. In order for the test to hold, it must be written with the same care (we will mention this in the Readable section)

- **Overuse of moq** - sometimes it happens that having seemingly convenient tools in hand, we start to abuse them or use them incorrectly.

  ![WindowsTerminal](/assets/images/moq_over.png)

  This is the case with moq - it allows you to create a mock in a "simple" way, so every time we need to mount something, we just add a definition in the code like here TODO

  What's the problem with this piece of code? The biggest one is that if we have a website to which we create a mock and it is used in each test and we have 100 such tests, we put such a "monster" in each one by copy and paste. If such a "monster has 10 parameters, generics or other exotic parameters appear, then the construction is very complex. Now let's imagine that someone is doing a refactor of the method we did mock and then... all our tests stop working. So, taking a shortcut by copy / paste will make it difficult for you to maintain afterwards.
  It is said that the DRY (Dont Repeat Yourself) principle does not have to apply in testing - sometimes it is so and we can make some concessions, but it does not mean that we copy everything that goes without thinking
  Does that mean you shouldn't use such libraries? Of course not - they were created to help us, but it's enough to do it better - just separate it into a separate place - depending on how we want to separate it, it can be a factory method, builder or our stub. For example here: TODO
  we have a builder that uses mock internally and if anything changes, it's in this one place.
  Why did I write the "straight" way in quotation marks? I'll cover this in the readability section.

## Readable
Our tests should be legible for several reasons. One is obvious - where the application code should be read from, and as we have already mentioned, the test is an integral part of the application. In addition, tests constitute documentation of our code, so when reading the test code, we should see exactly what we are testing.

- **Mess in the Arrange section** - very often in this section there is a code for which Apple created the icon ![WindowsTerminal](/assets/images/kupa.png). Often the arrange section looks like chaotic house of cards that can collapse when we touch it which but it "somehow" works - often after a week the author does not know what is happening in such a test because it is simply hard to read. A well-written test does not need to be commented on (as it is with the production code) because it is so readable. The test is to clearly say what scenario we are testing. We should see in it these essential elements for the test, and the technicalities should not obscure the essence. Below, we used the Test Builder Pattern described earlier, thanks to which we have hidden the technical details and the builder methods clearly tell us what data is used in the test
It is also good practice to use comments in the most important places of the test - for example, if, for example, we test similar elements many times and change, for example, one element, it is worth commenting on this element (as you can see in WithDep2)

  ![WindowsTerminal](/assets/images/arrange.png)

  Of course, we don't have to use the builder every time - sometimes we just need to transfer something to the factory method. What matters is that the test is legible

  For comparison, we can see how one of our Shark teams moved from the previous solution to using builders to make their code more readable

- **Well-Named Tests **- the most difficult in programming is naming and invalidating the cache, and so is the naming of tests. There are many schools of how to do it well - I used the method of MethodName_TestCase_Result for some time and it was quite good pattern, but it did not always fit and sometimes I had to force something into this template. Currently I prefer an approach in which the method has generic name like Testx and the name displayed in the test comes from the attribute in which we can describe the scenario in which we test

  ![WindowsTerminal](/assets/images/good_names.png)

  The most important thing is that the name of the test should be clear and say what we are checking in a given test. Names should say something to everyone - not just programmers.

# When to mock

![WindowsTerminal](/assets/images/when_mock.png)

This is a very controversial topic as there are many schools of thought about how to approach it. I will present my opinion below. For a long time, a popularly used method (until today) is the so-called London school. In this approach, the test consisted of one class test and all its dependencies are isolated with mocks. This has its advantages because thanks to the insulation we have full control and we know that if something does not work it is due to a bug in this class because other components are replaced with a mock. Using this method, I had the impression that something was wrong (the so-called code smell) - that is, the feeling that something is wrong, but I could not put a proper name on it. The big problem with this method is that we spend most of our testing time writing mocks. Moreover, it forces us to create artificial interfaces - so the interfaces that we use as dependencies in the class only so that this dependency can be replaced. Interfaces were created to give the possibility to create abstractions for various implementations - in theory we can say that such a test implementation fits this definition, but it is a slight abuse. We end up with the code in which we have to define an interface for virtually every class. It is clearly a code smell (at least for me).

This is not just my opinion - such voices appear both in the cited book and in other programmers. Jeremy D. Miller, author of such libraries as StructureMap, Marten DB, Jasper and Lamar, in one of his blog entries, quoted the following sentence:

"Truth be told, many of the mock-heavy unit tests I wrote back then didn't provide a lot of value compared to the effort I put into authoring them, and I learned the hard way in longer lived codebases like StructureMap that I was better off in many cases breaking the “one class” rule of unit tests and writing far more coarser grained tests that were focused on usage scenarios because the fine-grained unit tests actually made it much harder to evolve the internal structure of the code. my later efforts, I switched to mostly testing through the public APIs all the way down the stack and got much better results. "

So is there a better method? There is a different approach, but as always, it also has its drawbacks. In this approach, we do not test the class but the use case, so we do not blind all dependencies. We test the class along with the dependencies it uses (except for the exceptions that we'll talk about later). This has its advantages and disadvantages - the main disadvantage is that we lose a bit on insulation, so if something breaks down, we can not tell immediately which class the problem is in, but it is the price we have to pay for the advantages of this solution. Due to the fact that we use the same classes, we do not have to write mocks and, above all, we do not have to maintain them, so we can spend more time on the merits. We also don't have so many interfaces that we need to maintain and it makes our code simpler.

So how should we mock well - there is never a golden rule, but we can follow the one that says not to mock the so-called private classes, i.e. those used internally that do not refer anywhere outside our system. However, we should mock the so-called Shared Dependencies, i.e. databases, file system and other potentially shared resources or resources over which we have no control.

In times of dockerization, more and more such shared resources can also be made available "locally" for the test

![WindowsTerminal](/assets/images/mock_external.png)

At this point, it is also worth mentioning about integration testing. Testing # Integration testing - thanks to the ease of ASP.NET Core and what we said about mocking, the difference between unit and integration tests (in the sense of those with ASP.NET Core) starts to blur and this it's up to you to decide how much granular tests you will write.

When it comes to the granulation of the test, it is also an element that must be felt (similar to the size of the microservice). The author of the book gives us a hint, but you will have to decide in the end

"A test should tell a story about the problem your code helps to solve, and this story should be cohesive and meaningful to a non-programmer." Vladimir Khorikov

Of course, the point is not to create unit tests, but only to integrate - the principle of the pyramid is still valid, however, the differences between these tests are not as big as it might seem.

One more thing is worth mentioning - sometimes we make a mistake in defining whether something is a dependence of our class. For example, we have a class that takes as a dependency a service that retrieves the current date - it needs it for its calculations. If we look closely, we really need a year - what if we didn't take this dependency in the constructor and take it as a parameter of the enumerating method? I am not saying that you always have to do this, but it is worth considering what is your real dependence and what is the parameter

![WindowsTerminal](/assets/images/time_dep.png)

# Good practices
Below are some good practices

- After finding a bug in our software, write a test that covers the discovered case
- In the Assert section, avoid using production code - test verification must be entirely on the test side, if we use production code we risk that by changing the production code we will change the test assertions in a non-intentional way. Perhaps the change will eventually be in the test, but it will be intentional
- Similarly, when it comes to algorithms - do not put a piece of the algorithm in the assertion, but only the expected result (if we test the division, substitute the resulting number instead of x / y in the assertion). By doing this you are stiffening the link to the production code that may change.
- Avoid leakage of the test code into the production code - we should not change the production code so that something works in the test but won't be used in production - this is a potential hole that someone may exploit unknowingly. Do not put the test classes in the production code because it creates a trash.
- Avoid trivial tests - we often write tests just to "get something" and this test does not help us much, but is for the satisfaction of the requirements. Try to make the test really helpful in catching a potential problem. Tests should focus on your domain model - where the main logic is or as they also say "where the money is" (smile)
- Avoid conditional sections (if'olog) in tests - the test should be clear and specific and its execution should not depend on parameters (we are not talking about tests with variations of InlineData input data)
- Set the so-called Quality Gate for the new code that you add to the project so as to ensure a minimum number of tests
- If we create a class that cannot be created badly (https://enterprisecraftsmanship.com/posts/always-valid-domain-model/) and so the constructor will prevent us from creating an incorrect object, many tests will fall out because we are sure that this the facility is good

# Useful tools / libraries
So far, we've covered non-language aspects of testing. We will not discuss testing libraries in detail now, but only guide you to what may be useful to you.

- As for the testing framework, we have several - NUnit, MsTest2 or xunit. Which one you will use is a matter of preference because they all work with the dotnet test, but probably the most popular lately is xunit.
- As for the assertion, each framework provides it in a package, but I recommend checking https://fluentassertions.com/ which adds some interesting facilities like checking collections or objects in terms of content. In addition, it can be used and transferred between frameworks from the previous point
- Snapshot Testing can be used to assert complex objects - https://www.danclarke.com/snapshot-testing-with-verify
- Testing the database https://github.com/jbogard/Respawn
- To facilitate the dummy in the arrange phase, https://github.com/AutoFixture/AutoFixture is helpful. In addition to automatically creating objects, we can also inject implementations through the AutoDataAttribute attribute that we use in our test (below the AutoMoq attribute and dependency [Frozen]) TODO
- We often painstakingly rewrite the data collected during debugging to add it to our assertion - https://github.com/thomasgalliker/ObjectDumper will help (Just call ObjectDumper.Dump (dumped_object))
- If we have a piece of code that we want to run super fast, BenchmarkDotNet will be very useful

When it comes to tools, in addition to the well-known dotnet test, Visual Studio and Rider, we have at our disposal

Newman - allows you to send HTTP requests based on collections created in Postman

# Summary
Hopefully at least one person will look differently at code testing after reading this post

![WindowsTerminal](/assets/images/test_feel.png)

# Additional materials
- https://www.manning.com/books/unit-testing - Unit Test book
- https://khorikov.org/files/infographic.pdf?__s=wuvz8ntf18tq8tidedrx - Unit Testing Cheat Sheet
- https://dariuszwozniak.net/posts/kurs-tdd-19-mock-stub-fake-spy-dummy - Suby, Mocki and others
- https://github.com/moq/moq4 - Moq
- https://blog.ploeh.dk/2017/08/15/test-data-builders-in-c/ - Test Data Builder
- https://jimmybogard.com/avoid-in-memory-databases-for-tests/ - Database testing
- https://jeremydmiller.com/2021/08/04/testing-effectively-with-or-without-mocks-or-stubs/ - testing without mocks
- https://blog.ploeh.dk/2013/03/11/auto-mocking-container/ - Automocking container
- http://xunitpatterns.com/Fragile Test.html - Fragile Tests
- https://herbertograca.com/2017/11/16/explicit-architecture-01-ddd-hexagonal-onion-clean-cqrs-how-i-put-it-all-together/ - onion architecture