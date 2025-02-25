---
layout: post
title: "Utility AI: You Do (Not) Need Pathfinding"
date: 2025-01-26 15:00:00
categories: rust ai gamedev
---

Once upon a time, I made a 2d clone of a challenging boss fight from one of my
favorite games, Guild Wars 2. The Dragonfruitvoid, as it was called, adapted
Guild Wars 2's The Dragonvoid's challenges into two-dimensional form and
everyone liked it! Story over.

Or, it would have been, were it not for a small complication: The
Dragonfruitvoid was single player, Guild Wars 2's encounter requires the
collaboration of 10 players. While this mismatch was an issue, the main reason
I made a simulator in the first place is that the real boss's hardest part is
scheduling other people. I had to aim for a middle ground:
artificial-intelligence-powered teammates.

Artificial intelligence may conjure to mind visions of 700 billion parameter language
models trained for millions of dollars but the problem I had was much simpler:
taking actions quickly in response to external stimuli. Instead of responding
to, "You have a dragon's tail slamming down to your right. React accordingly."
with an eight-paragraph chain of thought involving wakeboarding, I needed a
system that would take that same information and say, "go left." Luckily, before
LLMs swallowed the world there grew great forests of decision trees sustained by
the concept of utility. Decision trees and the wider idea of utility see a ton
of use in game development for AI. To accomplish my goal of artificial
teammates, it was time to plant a decision sapling.

The first step I took was to list down the basic decisions a player needs to
make during The Dragonfruitvoid. My list looked like this:

- Don't fall off the edge!
- Don't stand in the bad circles
- Kill those crabs!
- Push the orb to its targets

I had already ordered this list by importance: falling off the edge is an
instant death while pushing the orb is something that just needs to get done at
some point. This idea of importance maps exactly to that of utility. For the
implementation in my game, I defined utility as the priority of an action
between 0 (don't bother) and 1 (drop everything to get it done). The simplest
implementation is the Don't Fall Off function which is usually a stand-in-place
action with 0 utility, but becomes a move-to-center action with 1 utility if
the AI player is close to the edge. The Kill Crab function is more complex.
Instead of either being an emergency or nothing, the utility of killing crabs
scales up as they become more of a threat and scales down if the player is too
far away to hit it. A crab minding its own business: no issue, a crab about to
collide with the orb and explode: big issue. Therefore, the utility of killing
crabs scales based on their distance from the orb and the ease of hitting them. Similarly, the Push Orb
function scales on whether the orb is happily on the way to its destination or
if it needs its path adjusted. This leads to the first example of how a
utility-based decision tree enables dynamic behavior to emerge. AI players near
crabs will take them out while others ready to push the orb will shepherd it to
its destination. Impressively, these four thoughts working in harmony were
enough to clear three of the nine phases of The Dragonfruitvoid, but getting
the remaining phases done required grafting new branches onto the decision
tree.

Up until this point, the decision tree was only a single layer deep, simply
comparing a set of thoughts with utilities and choosing the most important.
We can greatly improve the AI's performance by emulating what players in the
real game do: assign roles within the group. Now, instead of being a bunch of clones
each rushing between every possible responsibility, each player will have its
own decision tree with a subset of branches to reduce its (virtual) cognitive
load. In the orb phase, this means that the group can split into two teams that
alternate pushing and crab killing to make sure that there's no chaos from
swapping rapidly between priorities.
