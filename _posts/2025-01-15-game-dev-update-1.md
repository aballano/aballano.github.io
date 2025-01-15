---
layout: post
title: Gamedev Update #1
categories:
  - Gamedev
  - Godot
  - GDScript
  - Gaming
  - Software Development
published: true
---

![](https://raw.githubusercontent.com/aballano/aballano.github.io/master/images/Godot_logo_u1.svg "Godot logo")

---

It was time to do a Gamedev update!


I'm gonna do a quick recap on what I've been doing for the past 5 months.


# My game idea

As I mentioned in my first post, I want to be very conscious of the scope of the game, therefore I decided on a story and I want to make a sort of detective/exploration/talking/quest kind of game, no combat, no rpg.


The idea is that the player has to find the right ruler for an island and there will be different ways to achieve their goals through dialog options and decisions.


# The work

During this 5 months I've actually worked in the game for 41 separate days according to my [calculations](https://bsky.app/profile/aballano.bsky.social/post/3lea2yk5his2z) which would be a ~30% of the time, not bad!


This are the main features I've build/got from plugins during this time:


* A 18 page GDD.
* A half-done Excalidraw decision tree for quests.
* 4 Area maps (outside) decorations.
* 4 interior/special maps for quests.
* Player camera, movement, actions and death.
* NPC animations and movement.
* Warps between zones.
* Area transition animations (plugin).
* Cinematic events on warp travel or by contact.
* Dialog system (plugin)
* Inventory system (plugin)
* Quest system (plugin)
* Functional menu (plugin)
* Day/night cycle (external)
* 9 Quests
* 10 different NPCs + dialogues
* Working save system (half plugin/half self built).
* Temporary free sprites that don't match each other.
* 1 single and very basic track.


# Learnings

* If you're new to it, maybe trying to create a pseudo-complex system like Dialog, inventory, quest, etc could be a good idea for learning, but if it starts becoming hard try looking for plugins for a speedup.
* Plugins are great but not perfect: It's good to use plugins and you save a lot of time not having to implement complex systems or tedious UIs, but you might need to do adjustments or change things, so invest some time taking a brief look at the plugins code to get to know it.
* The hierarchy of your nodes matters, a lot! Maybe you're thinking on having your camera inside the player, but how will you communicate with it if you need to apply an effect to it or focus elsewhere? Of course most of these kinds of issues will be popping during development because it's almost impossible to prevent everything. If you're using a singleton/static global state somewhere you can benefit from global signals! Is not perfect, but you can have your camera listen for a camera_focus event that you can trigger from anywhere else in the code (a cinematic, for example), so you don't lose a lot of time rebuilding the hierarchy.
* Even simple animations, music/sounds and effects add a lot of value! Consider adding some from time to time to improve your own vision of the game.
* Having good sprites is difficult! Either you are lucky and get some free sprites that match or that you can easily modify for that, or you'll end up having to paint them yourself or paying someone else to do it. Starting with free non-matching sprites is good for speedup but might become annoying after some time.
* Building the MVP (minimum viable product) first helps a lot for scoping and to keep focus on what's important. That super cool quest that you created for a particular guy can be improved, or even implemented, later.