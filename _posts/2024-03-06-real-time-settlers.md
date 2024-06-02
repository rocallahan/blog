---
layout: "post"
title: "Real-Time Settlers Of Catan"
date: "2024-06-03 11:00:00 +1200"
permalink: "/2024/06/real-time-settlers.html"
---

25 years on _Settlers of Catan_ is still a fine game, but can be tedious when you have to
wait for other players to take long turns. In our family we thought "what if Settlers had
simultaneous time-limited turns?" We implemented it with a few rule tweaks and
[an app](https://settler-support.vercel.app/) written by one of my children. It turns out
to work very well! Every experienced Settlers player we've played with so far has found it
to be a great improvement over the original game. We can finish a six-player _Cities and
Knights_ game in about 45 minutes of intense fun.

## How to play

_I'll assume you're playing Cities and Knights and know how to play it well._

The gist is very simple. Nothing changes during the setup phase. After the setup
phase it is always everyone's turn. You can trade (with the bank
or other players), build freely, use progress cards and activate and use knights at
any time (but you still can't use a knight after you activated it in the same turn).
Every 30 seconds the app rolls the dice to start a new turn and
everyone takes whatever resources, commodities and progress cards they are entitled to.

Whenever the rules require player B to respond to player A, it's unfair if B can stall A,
so stop the clock until the action is resolved. This includes player attacks that
require a response from the target, e.g. if someone plays _Bishop_,
_Master Merchant_, _Wedding_, _Deserter_, or _Commercial Harbour_.  This does not
include trading, because in that case there is no obligation for any player to respond.
While the clock is stopped players cannot take other actions.

When a 7 is rolled and the robber moves, stop the clock while players with too many cards
discard and one player moves the robber. For the first robber move, the starting player
moves it, and then for future 7s proceed around the table in player order.

The app takes care of tracking the barbarian ship. Stop the clock while resolving the barbarian
attack.

When _Alchemist_ is played, press the Alchemist button on the app. When the next turn
starts, the app will not roll the dice for the next turn. Stop the clock until the
player announces the dice values.

Especially in the early game, sometimes no-one wants to do anything and you're just waiting
for the next turn. When everyone agrees, press the Next Roll button to advance immediately.

When someone announces their win, the game is over --- other players don't get until the end of
the round to cut them down. (That might be an interesting variant though!)

Have fun!

## Hints

For large games you'll find it easier to split the resources and commodity piles into two,
one at each end of the table, and for one person to deal out the progress cards when they're
generated.

Wheat and knights are more valuable in this game because with enough resources you can use
all your knights and then reactivate them every turn.

Initially it's easy to take all your actions within 30 seconds, but as the game progresses
you get more cards every turn and the time constraint becomes more of an issue. Late game
you may struggle to do everything you want to do in the time allowed. Compared to
the regular game, it's more difficult to track the progress of other players and target
the leader. These are good improvements, in my opinion.

## FAQ

### How do you resolve simultaneous but conflicting actions, e.g. two players building a settlement in the same location?

Conflicts placing roads and settlements are actually rare. It's pretty much always clear
who got theirs down first. If you can't agree, I suppose you can toss a coin, although
our playing groups are friendly and we've never needed to do that.

Conflicts involving progress cards can happen. For example, one player plays _Resource Monopoly_
and around the same time another player announces they're building a settlement. Again,
at our table all players try to come to a consensus about who said it first.

Occasionally, multiple players want to take conflicting actions when the clock restarts.
To resolve these we allow players to preannounce moves, e.g. "after this, I'm going to build a settlement".
The order of preannouncements determines the order of the actions.
