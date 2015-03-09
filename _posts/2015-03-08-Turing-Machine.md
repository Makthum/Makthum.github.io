---
layout: post
title: Turing Machine
description: "Turing machine in python"
headline: "How to design turing machine in python"
categories:
  - Softwaredevelopment

tags: 
  - Turing Machine
  - python
  - Finite state Machine
comments: true
mathjax: null
featured: true
published: true
---

Turing Machine is a hypothetical machine which is believed to consist of a scanner to read/write character and a infinitely long Tape containing characters inscribed inside a square. Read/write head is programmable to read different characters based on some finite rules.	Take a look at this wiki [Turing Machine](http://en.wikipedia.org/wiki/Turing_machine).

Its been more than a month since I came up with something. Recently I was asked to code Turing machine using Python and I have to warn everyone here I am not good at python . Lately i m trying to pick up python and so any suggestion or improvements you can mention them in the comments section.  


![Turing Machine]({{ site.url }}/images/Turing_Tape.jpg)


Design
========
Turing machine was suppose to read it contents from tape and based on the character read it should either read or write characters and move the head to right or left based on the rules. Which basically is nothing but state transitions or finite state machine. Based on the current state and character read you do some action and move to a new state. So decide to create objects to store states and the actions. 

Another point to mention here is that turing machine starts at a particular state and stops when a particular state is reached. To sum up following are the characteristics of a turing machine

1) Turing Machine starts at a particular state , with scanner head at paticular position 
2) Read the character under head, 
3) Now based on the state-Action table either write some character or do nothing 
4) After writing character move the head left or right again based on the rule 
5) Move to a new state
6) if new state is the final state stop the turing machine 
7) I have also decided to include that if there are no rules found , Turing machine halts.

![state-Action]({{ site.url }}/images/state-Action.gif)


Now when it comes to object design i am used to think in terms of java, so i decided to create separate objects for turing machine , tape , state and action. As i can asssociate certain attributes to each of those objects.

Turing Machine :- Some of the attributes associated with the Turing machine i can think of were initial state, final state . Tape contents and state-Action table. Basically any attribute that has go to do with the working of our Turing machine since this object will serve as our soul of the Turing Machine. 

It is also make sense to move all the configurable properties of the turing machine to be read from an input file so none of the config parameters are hard coded 

State:-  state index and character read together makes up the current state of the turing machine. So that it makes easier when you do the state transitions

Action:-  Action includes information on what character to be written , which direction to move ur head and what is the new state of the turing machine 

![state Diagram]({{ site.url }}/images/StateDiagram.jpeg)


Tape:- Tape object merely stores the content of the string file we need to work on. Tape used for prototype are small so it is obsolutely fine to store entire contents on a Array. If its an infinitely large input I would consider reading chunks of character(blocks of characters) and make our Turing machine to run on it. 

There are few things to consider here State-Action table might consist of wild card characters which indicates that rule applies to any character under the head. If an '*' appears under the write character section, then it means no character is written to the Tape(Do nothing just move the head).

you can download my code from [here]({{ site.url }}/images/turingmachine.zip)
![Results]({{ site.url }}/images/TuringResults.png)

[MyCode]() 
