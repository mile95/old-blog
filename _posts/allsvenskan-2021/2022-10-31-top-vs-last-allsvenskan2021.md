---
title: "Comparison of game development between top and bottom three teams in Allsvenskan 2021"
date: 2022-09-30T10:48:19+02:00
tags: [data analysis, python]
author: Fredrik Mile
---

Ever thought about the differences between the best and worse teams in a football league?
What makes a good team **good** and a bad team **bad**?
In this post, I will go through Allsvenskan 2021 and have a look at how games developed for the top three compared to the bottom three teams.
I will try to figure out why some teams win more games and why other teams win fewer games but from some odd perspectives.
You don't need to worry! There will be data supporting my claims.

Supported by data? But, then you need data!? Yes, that is very true!
Thanks to [PlaymakerAI](https://twitter.com/playmakerai) and their open dataset, I have access to this data, allowing for interesting analysis!
Most of the claims in this post are supported by data, but I tend to have some guesses (killgissningar) too.

I have uploaded the source code used for this analysis on my GitHub. You can find it  [here](https://github.com/mile95/t/tree/main/playmaker_opendata).

Let's start!

## Explanation of graphs

This post contains a few line plots.
I should probably say that the blue line (Top teams) contains data from Malmö FF, AIK, and Djurgårdens IF.
The red line (Buttom teams) contains data from Östersund FK, Örebro FK, and Halmstad Bk.
I have grouped all data by intervals of 10 minutes.
If the graph shows that 20 goals were scored at game time 7,  that corresponds to that the teams scored a total of 20 goals during game minutes 70 to 79.

![goals](https://user-images.githubusercontent.com/8545435/199305002-74a45d24-6f46-4021-a0f1-07b11d634e00.png)

## Offside

To score goals, you need to be onside and not offside, which the bottom three teams seem to have a problem with the first 50 minutes of the games. During the last 15 minutes of the games, the better teams are more likely to push for wins and also more likely to end up in an offside position.

![offside] (1)](https://user-images.githubusercontent.com/8545435/199304959-2ef8f447-7771-4ac9-ad9f-72fb0083954e.png)

## Yellow card

We can see the same trend for yellow cards.
The bottom three teams seem to get booked more than the top teams in the first 50 minutes of the games.
During the last 15 minutes, the top three teams seem more eager to win, and the number of yellow cards goes a bit out of control.
The top teams maybe have a mentality to do whatever it takes to win the game,  while the other teams give a bit less about the result.

![yellow card](https://user-images.githubusercontent.com/8545435/199304968-257d3fb1-c56f-402c-b52d-4a7e50407f04.png)

##  Fouls

Let's start this section by having a look at the fouls.
We clearly see that the top teams perform much more fouls at the beginning of the games.
Are the top teams more focused and eager to control the flow of the game?
Your youth coach maybe did not lie!
It seems to be important to go out and kick down your opponent from the very start of the game!
Show them that you are not there to mess around.

![fouls](https://user-images.githubusercontent.com/8545435/199304973-30d45fea-589f-423d-a902-1bb802fd50e7.png)

## Goalkeepers

Poor goalkeepers!

![goalkeeper](https://user-images.githubusercontent.com/8545435/199304980-0b455b62-12df-40b0-b061-c1c152614f57.png)

## Air challenge

The air seems to be a place for equality.

![newplot (5)](https://user-images.githubusercontent.com/8545435/199304986-4068e854-ceda-4923-97b4-a3435fd0103a.png)

![newplot (6)](https://user-images.githubusercontent.com/8545435/199304988-3d784bc4-dfec-462a-8eb7-6c034e922650.png)

## Bad Dribbles and Lost Balls

All teams seem to be equally bad at dribbling.
That is probably a Swedish thing more than anything else.

![newplot (7)](https://user-images.githubusercontent.com/8545435/199304993-630e8e73-5bbf-4692-acf8-670323c57f01.png)

One thing that surprised me is that there is no difference in the number of lost balls between the top three and bottom three teams.
They seem to lose the ball almost equally as much.
I thought the bottom three teams would have lost the ball significantly more. Weird.

![newplot (8)](https://user-images.githubusercontent.com/8545435/199304996-cb2982c4-14ba-4e0c-a958-fd039624ece1.png)

That's all for this post.

You can run the source code to get similar graphs over other events.
Again, thanks to [PlaymakerAI](https://twitter.com/playmakerai) for the data.

[@milefredrik](https://twitter.com/MileFredrik) if you want to get in touch!

