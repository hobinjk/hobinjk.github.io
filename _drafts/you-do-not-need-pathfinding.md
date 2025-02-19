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
- Kill those crabs!
- Push the orb to its targets
- Don't stand in the bad circles

These already fit into a hierarchy where I could use utility
