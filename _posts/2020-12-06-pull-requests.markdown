---
layout: post
title:  "Pull Requests"
date:   2020-12-06 18:11:23 +0100
categories: [pull-requests, code-quality]
author: dnf
image: assets/images/pull_requests_main.png
featured: true
comments: true
---

There are many things we can do to make the quality of our code better. We can do all kinds of tests like unit testing, integration testing, end to end tests but there is another way to improve code quality. In this post I will describe Pull Requests. Probably all of You have used them before but are we using them properly?

# Introduction

I wanted to point out right away that I'm not going to claim that we don't need to test our code!! Testing is necessary and I will write about it later, but it is not necessarily used to eliminate errors. Of course, to some extent it will eliminate errors, but even the full coverage of the code cannot falsely prove that our application is error-free.

In the Udie Dahan course I recently watched [Learn Advanced Distributed Systems Design](https://particular.net/adsd) - the author said a very important sentence in which he quoted research which showed that PRs bring us much better results.

What do we need unit tests for? They mainly help in:

1. In creating a better quality design - when writing tests, we separate dependencies better and create code that has less coupling, is better to read and we understand it better
2. Tests become the documentation of our code
3. Tests allow us to maintain regression and confidence in refactor
4. Better separation of reusable code elements
5. Detection of edge use cases - and thus some unusual borderline situations of using our code
   
There are many articles talking about this topis - for example [this one](https://hystericalraisins.net/warning-unit-tests-and-tdd-do-not- Eliminate-defects-72ed5e5d1db7)

So, summing up the tests are very necessary and in other entries we will tell more about them and how to write them well, but they are not sufficient to ensure good quality.

# If not tests then what?

So if not only tests, then what? One of the tools that help maintain good code quality are **Pull Requets**. But we all use them on daily basics, so everything should be ok?
Unfortunately it is not enough to enable them on our CI pipeline because **it is not enough to have something to use it well** - we can buy the most expensive spinning bike or jogging machine to exercise at home but end up to use it as a clothes hanger or an expensive piece of furniture.

![SG Process](/assets/images/pull_requests_hanger.png)

The same thing is with PR's - to make them work we have to use them properly.

# Why are PR's so important?

Because when writing the code and writing tests to the code, we do it by looking through the prism of our "pink glasses" at our "child" that we created, **so it must be good**. Unfortunately, **we do not see our mistakes very often** - it results from many issues. It can be just ignorance but also inattention or plain narcissism 😊. Regardless of the source of the problem, the result is that we are not able to see where we make mistake and hence we will not write a test that will catch our errors. **When we are not aware of the problem, we will not write a test for it**.

If we know about the problem and we are only looking for a solution, we only need the so-called ["Yellow duck"](https://rubberduckdebugging.com/) - in short it is an object or a living person to whom we tell what we do and we realize what the problem while explaining the conditions we are having. Is because when talking about the problem to "the duck" often we discover the problem without even a word on her part (especially when it is rubber 😉).
Even more important in the case of PRs is the element of interaction with other peoples. In a case in which we try to find a solution for many minutes / hours, and after showing it to another person, he shows us the solution immediately - have you ever had a situation when after showing the problem to the other person he is said "here you have a mistake" after only a few seconds to make You feel like a fool?

PR's operate on the same principle, but to work properly, they must be well done.

In addition, PR's gives Your team a good flow of knowledge - a junior reviewing the code of others can learn a lot, and a senior, despite the fact that he knows a lot, can learn even from a code wrote by a junior by discovering new language features of frameworks that have appeared recently.

# How to prepare good PR?

There are couple things we can do to assure we are doing a good job - of course this is not a full list because there is no universal way to to it right. We should learn all the time and extend points mentioned below to fit our needs:

1. First of all, the first step I recommend is **self-review**. Let's respect the time of others and review our code ourselves before adding someone to the reviewer's list. I often find some "remains" of code I left behind while prototyping in code which I have forgotten to delete. By doing this exercise we are sure that the final code that we are sending to the team is good enough for the next faces of PR game :)
2. **Avoid big PR's** - it is easier to check the code that will not "scare away" our reviewers. I think that many people, after seeing a huge amount of files, will either reschedule review to some future or even worse **approve it without doing actual review**. We will talk about this in the next section.
3. Big PR's cannot always be avoided, but there is a way - if we divide our work into logical commits, we can notify the reviewer to check the changes per commit. This is of great help in the case of the next point.
   
    > Remember to avoid many small commits with meaningless comments or many commits with changes to things not connected to each other. If we made a mess locally we can always [squash them or use rebase to make them better](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)

4. **Don't confuse refactor with business changes**! If you have one business code change and the rest are rename changes to 100 files, your change will be "hidden". If you break down the changes into commits (or separate PR), it will be much easier to check the code because the reviewer could see the core of the problem we are resolving without distractions.
5. **Self comments** - I often use the method in which, during self-review, I add comments explaining important changes or directing to a critical change that is controversial or directs a question to a given person. Such actions helps a reviewer to focus on the right elements and can shorten our PR's "ping pong game" because we are explaining things that we expect to be discussed.

    > I'm using "ping pong game" expression because sometimes people are making many comments and could discuss a very long time. It is even worse when people are working in different work zones so you have a response on the next day - just like playing chess by letters ;). **Please be a grow man (women) and pick up a phone to talk it over** - belive me it will be much quicker.

6. I also encourage you to do PR at an early stage to **discuss the solution before we make the final version**. Especially when working remotely, this allows discussions before you get too far. This is one of the agile manifesto rules - to work agile and not like in waterfall when you show the final product and could pray for approval because your client is seeing this for the first time.

    >Unfortunately, some CI tools like bitbucket does not support the "work in progress" functionality [BSERV-4262](https://jira.atlassian.com/browse/BSERV-4262) but you can always write in the titles like [WIP] and agree with Yor team that this is not the final version

7. **PR should be easily accessible** so that the reviewer can download it for offline review on a local machine - it's not always possible to check the code well just by reading it in the browser

8. Remember to always have Your PR code up to date with the target branch - it is important because we prevent conflicts and in the worse case breaking our build. The second could happen in some CI systems like bitbucket when we have long live PR or have many simultaneous PR's that are merged all the time. In the edge case scenario our PR is checked (build have been done) but while waiting for an approve someone have merged the code conflicting with ours but we are not checking this and we merging the code.
9. We should check if PR have all reviewers on place - maybe someone important is missing


# How to make a good review?

Here we come to the most important element, how to do a good review.

1. This point should be double-brushed. **Never allow yourself to approve PR without reading the code**. When it comes to PR, it is the **duty** of the reviewer to thoroughly check the code - **if you do not have time, do not do it at all**. The person submitting something for review **relies on you**. Even if you are a junior and you have a senior to check, it does not mean that you have to just approve. The moment you become a reviewer, your personal relationships do not matter - so whether it is your good colleague or supervisor, as they say

    ![SG Process](/assets/images/pull_requests_godfather.jpg)

2. It is very important that, as a reviewer, **we ourselves become the authors of this change and we take responsibility for it** equally with the author !!! If the error passes the review, it is not the author's fault only, but also the reviewer! Therefore, it is important to do it accurately.
   
    >Many people are defending themselves and claiming that this is not true and the responsibility is not on them. In my opinion in our IT world, we make good money but we also remember that this is not a game and we should take responsibility for our actions. We have a good position in the labor market but beyond the requirements, we should remember about responsibility. To make you see this cleaner you have a factory for toys - one person is making a toy in one department and in the second person from the quality check is checking for quality. You gave complain that child was hurt during the use of the toy - who will you blame? What is the purpose of the quality department if the have no responsibility?

    Of course the decision of blaming someone is not the the purpose of this talk - the most important thing to remember is that PR is equally important as other precess like writing the code.
   
3. When writing comments, let's try to **explain what we mean** - sometimes it is not enough to say "it is wrong". It is worth writing how to do it better, explain why it is wrong or give a link that explain it in more details. If the other person has a better understanding of what is wrong, they will be more likely to improve and avoid ping-pong
   
4. **Avoid teasing / joking** if we are not sure that the other person will not perceive it badly. Remember that it is supposed to be professional and you can destroy someone morally with badly written comments. If this is our friend and we know that we can allow ourselves to a certain degree of malice, then ok, but remember that we cannot do this always
   
5. It is also good practice not only to point out problems - if you notice something cool in the code, you can always write some pleasant words to the author. **Such things help in team interactions** but should be honest and not in corpo way.
   
6. If you have an idea that something can be done differently because you do it this way, but it does not change anything de facto, maybe **it is worth letting go of the topic** 😉.
7. What to check during a review - this is a very complicated topic and everyone will definitely have its own point of view. It is definitely worth looking at:
   
    - **Is the code readable**? - if you find it difficult to read it during the review, it is a sign that the author need to change it. If we allow unreadable code, in a few years we end up with a garbage can that nobody wants to touch
    - Is there a **missing check somewhere**, which may result in an exception?
    - Are all **boundary situations checked**?
    - Is the code architecturally well **divided and isolated** - maybe there is coupling?
    - Whether a small change in code could break something elsewhere - sometimes minor changes can cause the so-called side effect and it's worth keeping in mind
    - Commented out code - should not be there. Uncle Bob in his book said that this is why we have tools like git - we can always look into history. Let's not keep the commented code because **it may turn out that maybe it shouldn't be commented out!**
    - If we connect to other systems in our PR, do we really take into account network problems - i.e. resumption, circular break, etc.?
    - Do we have a set of unit and integration tests?
    - Do we update the documentation together with the changes - **so yes it's worth having something like documentation** !!

8. There are elements that we could spot now, but the machine will do it better for us. So we have to use tools such SonarQube or code analyzers that will check our code with static analytics - remember even if you have good reviewers they are not machines and will skip errors. Machines are better for this.

10. "Be thorough, but not too thorough" - it is important to do good code check but if all code checks will be focused on checking tabs, spaces and other so-called little things it will not make our code better. People could be angry that each time there You war checking for that kind of things but not look fo important stuff - so please live good with your team and try to understand that **not everyone is a pedantic like you**

11. Check if PR is resolving the problem described in the connected issue - we have to know that the whole work is resolving something ;)
12. When during PR review something comes up and it may extend our work, it may be worth to separate a task from it. I recommend that you immediately separate the issue instead of writing TODO in the code - then we know that the topic will not be forgotten

# How to react to a review?

We should always look on both sides of the process so we should talk about our reactions and our emotions. 
A large number of comments may cause the person receiving them to become discouraged from work after overwhelmed by to many comments each time. As a reviewer remember to give up in some point and think about this - of course we should do a accurate review but there are some boundaries. 

**We have to remember to properly interpret what PR is**. Of course despite badly written comments, we usually get some advice on how to do something better with a good intention behind - at the beginning I didn't feel well in the first project in which I got 100 comments 😉 However if they are **constructive**, **don't be offended or take them personally**!! This is work and we should work together to make better code, so you have to think before go emotional and sometimes "be a man" and deal with criticism. With a partnership approach, you can reach a small number of comments after some time, but it requires **good communication and mutual understanding**!

Sometimes in some situations it is better to call someone instead of discussing one line in PR all day long. 

Also, remember **not to be too emotional about your code** - just because you wrote it doesn't mean you have to defend it to the grave. It's worth looking at the code and admitting your mistake. Sometimes, when you compromise, you can agree that someone is right, despite the fact that in our opinion he is not - it's a waste of time to disscuss about proper programming ideology 😊

# Merge strategies

When we finally get to the point of merging our changes we usually use the merge button but we should now that there are more options. [For example bitbucket have optional merge strategies that you can enable](https://confluence.atlassian.com/bitbucketserver/merge-a-pull-request-808488562.html).

- Merge commit (--no-ff) DEFAULT: Always create a new merge commit and update the target branch to it, even if the source branch is already up to date with the target branch.
- Fast-forward (--ff): If the source branch is out of date with the target branch, create a merge commit. Otherwise, update the target branch to the latest commit on the source branch.
- Fast-forward only (--ff-only): If the source branch is out of date with the target branch, reject the merge request. Otherwise, update the target branch to the latest commit on the source branch.
- Rebase, merge  (rebase + merge --no-ff): Commits from the source branch onto the target branch, creating a new non-merge commit for each incoming commit. Creates a merge commit to update the target branch. The PR branch is not modified by this operation.
- Rebase, fast-forward (rebase + merge --ff-only): Commits from the source branch onto the target branch, creating a new non-merge commit for each incoming commit. Fast-forwards the target branch with the resulting commits. The PR branch is not modified by this operation.
- Squash (--squash): Combine all commits into one new non-merge commit on the target branch.
- Squash, fast-forward only (--squash --ff-only): If the source branch is out of date with the target branch, reject the merge request. Otherwise, combine all commits into one new non-merge commit on the target branch.

it is out of the scope of this article to go deep on each option but in short you can consider **Squash** if you have many commits and maybe finally you would like to have one or when you don't want to have a merge commits you can use rebase.

# Tools

1. Static code analysis - even when analyzing on the server, it is worth having a set of tools that will catch potential errors immediately on our machine. In the new [.NET 5 it will be standard](https://devblogs.microsoft.com/dotnet/automatically-find-latent-bugs-in-your-code-with-net-5/)
- [FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)
- [Roslynator.Analyzers](https://www.nuget.org/packages/Roslynator.Analyzers/)
- [Web API analyzers](https://docs.microsoft.com/en-us/aspnet/core/web-api/advanced/analyzers?view=aspnetcore-3.1&tabs=visual-studio)
- [Microsoft.VisualStudio.Threading.Analyzers]( https://www.nuget.org/packages/Microsoft.VisualStudio.Threading.Analyzers/)
2. If you make typos like I do, I recommend spell checking
- [Visual Studio Spell Checker](https://marketplace.visualstudio.com/items?itemName=EWoodruff.VisualStudioSpellCheckerVS2017andLater)
- [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker)
3. Set the TreatWarningsAsErrors flag - time to stop turning a blind eye to warnings !!

# Summary

As You can see there are many things you have to know when you are playing "PR game" and I hope now You will be do it better. I hope that I will get good constructive review with honest approve for this article ;).