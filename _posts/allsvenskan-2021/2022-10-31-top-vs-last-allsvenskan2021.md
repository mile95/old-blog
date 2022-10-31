---
title: "Similarities and differences between top and buttom three teams in Allsvenskan 2021"
date: 2022-09-30T10:48:19+02:00
tags: [data analysis, python]
author: Fredrik Mile
---

Ever thought about the differences between the best and worse teams in a football leauge?
What makes a good team **good**, a bad team **bad**?
In this post, I will go through Allsvenskan 2021, and have a look at trends during games and how these trends differs between top three vs buttom three teams.
I will try to answer why some team wins football games and why other team looses football games from some odd perspectives.
Don't worry, everything will be backed by data.

Backed by data? But, then one need data!? Yes, that is very true! 
Thanks to [PlaymakerAI](https://twitter.com/playmakerai), and their open dataset I have access to this interesting data, allowing for some even more interesting analysis!
Even though this post is backed by data, I tend to do some killgissnigar aswell.

The source code used in this analysis can be found on my [github](https://github.com/mile95/t/tree/main/playmaker_opendata).

Let's start!

## Explenation of graphs 

This post contains a few line plots.
I should probably say that the blue line (Top teams) contains data from Malmö FF, AIK and Djurgårdens IF.
The red line (Buttom teams) contains data from Östersund FK, Örebro FK and Halmstad Bk.
I have grouped all data by intervals of 10 minutes.
If the graph show that at game time 7, 20 goals was scored, that means that the teams scored a total of 20 goals during game minute 70 to 79.


![goals](https://user-images.githubusercontent.com/8545435/199097463-0fb4cf15-0dd5-49a8-a127-6ffb7f6bfeda.png)

## Offside

To score goals you need to be onside and not offside which the buttom three teams seem to have a problem with the first 50 minutes of the games. During the last 15 minutes of the games, the better teams is more likely to push for wins and also more likely to end up in offside position.

![offside](https://user-images.githubusercontent.com/8545435/199097888-fc82de65-b85f-47d4-b638-ae3cc856a381.png)

## Yellow card

We can see the same trend for yellow cards.
The buttom three teams seem be booked more than the top teams in the first 50 minutes of the games.
During the last 15 minutes, the top three teams seems to be eager to win and the number of yellow cards goes a bit out of control.
The top teams maybe have a mentality to do whatever it takes to win the match while the other teams gives a bit less about the result. 

![yellow card](https://user-images.githubusercontent.com/8545435/199098913-eccab286-ad62-4027-8dcf-b75d614d9ce4.png)


##  Fouls 

Let's start this section by having a look at the fouls.
We clearly see that the top teams perform much more fouls during the begining of the games.
Are the top teams more focused and eager to control the flow of the game?
Your youth coach maybe did not lie! 
It seems to be important to go out and kick down your opponent from the very start of the game!
Show them that you are not there to mess around.

![fouls](https://user-images.githubusercontent.com/8545435/199100641-0697acf5-5a02-479b-97b3-376be90b1fda.png)

## Goalkeepers

Poor goalkeepers!

![goalkeeper-inaccurate-kick](https://user-images.githubusercontent.com/8545435/199102400-4990a673-2713-4ec7-bfdf-42f6403d8742.png)


![goalkeeper-throw-accurate](https://user-images.githubusercontent.com/8545435/199102814-4eb7f5bb-2b0b-4f3b-bd3a-7f30aa628d98.png)

## Air challenge

The air seem to be place for fairness.
I wonder how fair space is!

![newplot (8)](https://user-images.githubusercontent.com/8545435/199103541-18acc470-70d9-4284-9e08-a5035a5dd211.png)

![newplot (9)](https://user-images.githubusercontent.com/8545435/199103555-69164d7e-02e1-4fef-b262-8187be83e599.png)


## Bad Dribbles and Lost Balls

All teams seem to be equally bad at dribbles.
That is probably a swedish thing more than anything else.

![dribbles](https://user-images.githubusercontent.com/8545435/199104075-0af51186-ebba-4d00-a488-5e2217a922ba.png)

One thing that suprised me is that there is no difference in the amount of lost balls between top three and buttom three teams.
They seem to loose the ball almost equally amuch. 
I would have thought that the buttom three teams would have lost is signitifcally more. Weird. 

![lost balls](https://user-images.githubusercontent.com/8545435/199104644-7e8652d7-fcae-4f81-8332-99c44e1a8d88.png)


That's all for this post.

You can run the source code yourselves to get similar graphs over other events.
Again, thanks to [PlaymakerAI](https://twitter.com/playmakerai) for the data.

[@milefredik](https://twitter.com/MileFredrik) if you want to get in touch!

![Playmaker_Logo](https://user-images.githubusercontent.com/8545435/199088543-dc8abff9-0fb4-4e5b-a553-9e8f0f8ed5ec.png)
