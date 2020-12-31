---
layout: post
title: "Conquer Berkeley Pacman"
date: 2020-01-01 00:00:00 +0000
categories: uncategorized
---
{% include latex.html %}

## Introduction

[Berkeley Pacman](http://ai.berkeley.edu/contest.html) is a project delicatedly developed for teaching CS188 Intro to AI in UC Berkeley. It's a very appealing and useful project for new AI learners. I remember when I was in Trinity College Dublin, Ireland, I was majoring Intelligent Systems, this project was an assignment, we were only asked to finalize p0 - p3. That time as a rookie in AI industry the project was so hard for me, partly because of my lack of experience in script language Python. I spent days and nights to finish it. Nowaday as an experience person in computational advertising, in this pandamic season, I decided to challenge myself by finish p0 - p5. However, as I was redoing this project I found everything is quite easy now, I can think in a more abstract way and solve them without much effort. I was constantly being anxious that I didn't grow up faster enough. Hmm... afterall, I becomes more powerful.

OK, I am going to post about my solutions and insights. Because it's a 2014 project, I think it's definitely not a spoiler anymore. I will follow questions one by one and also mention important things in the slides.

The github repository is [here](https://github.com/PointerFLY/Pacman-AI).

## Q1, Q2

Classic depth-first and breadth-first search, no bullshit. DFS can use recursion or maintain a stack by yourself, BFS can naturally use a queue, or a fancy tail recursion.

DFS does not guarantee to find a optimal solution, for BFS, generally it's not. But if the cost of action is even, then it is, which leads to uniformed search.

## Q3, Q4

Informed search is about searches that take future cost into consideration. You need to weigh g(x) + h(x), which means current cost plus predicted future cost, among node candidates. The core data structure is a priority queue.

Uniformed search only consider 1 step ahead while a star consider the cost to goal state.

To make sure a star works, the heristic must be

##


##
