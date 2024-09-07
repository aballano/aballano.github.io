---
layout: post
title: Getting into Game development
categories:
  - Gamedev
  - Godot
  - GDScript
  - Gaming
  - Software Development
published: true
---

![](https://raw.githubusercontent.com/aballano/aballano.github.io/master/images/Godot_logo.svg "Godot logo")

---

It's been a long time since I last wrote a post!

So my TL;DR is that I'm doing some Game development in my spare time, and I wanted to share some of my learnings as an Android developer as I see some similarities and techniques that can be imported into game dev.

# Getting into Game development

As usual, my first task was to choose tools. At this point I already knew how to use Unity and Godot due to past projects and learning courses I watched.

### Tools

Even ignoring all the social media news regarding Unity's actions and so, **Godot** was a clear choice for me to go with little debate. And the reasons were simple:

* I found it simpler than Unity for 2D games, which is recommended when starting due to the simplicity of Axes to work with.
* The editor is embedded in Godot and you don't really need third-party paid tools (Rider) to code, although it would be nice to have more advanced tools (sorry, IntelliJ has spoiled me) so I might try the Rider plugin for Godot in the future.
* I find Godot's script language GDScript to be wonderful. It's based in Python but you can declare types explicitly and it's easy to configure to report errors if you don't ;) On top of that, it has support for **async/await** code and **high order functions**, which is awesome, especially when coming from **Kotlin**.

Ok, so having the game engine decided, the rest of the tools are also relatively personal and situational, but I went with the following:

* Git for version control, Fork as visual Git Client. I always set at least a local project I can commit to if I'm drafting something but this time I also wanted to code from different devices so I created a private Github repo too.
* Procreate/Krita as image editor. 
  * Procreate is just amazing, but you need an iPad with the pen, and it is a one-time payment, which I find really worth it after you learn to use the tool.
  * Krita is free and works perfectly for most cases. I would probably explore Aseprite (paid) in the future as I heard very good things about it, especially for animations.

And that's pretty much it.

### Avoiding traps

The first thing really is to **get used to** the game engine, so I did a couple of **tutorials**, some from YouTube, others from SkillShare, and most of them from a Humble Bundle I bought with 2D courses.

Next, you need an **idea**, and this is the really hard part. From a mobile dev perspective, it's relatively "easy" to come up with ideas depending on what's your target: maybe you want to create an app to solve a problem (tools, automation, files apps, etc) or you want to create a product from that or from another idea of a service you think people would like to pay for.

But an idea for a game has been something much more challenging for me to come up with. The reason is simple: Obviously, because games are not regular business apps, but mainly because I do this as a hobby, so I want to make something relatively original and curated in a way that is pleasant for others to play. I found this to be moving more to a role of writer/director sometimes rather than a programmer, and it turns out it's fun! (and challenging).

Additionally, it's very easy to fall into the trap of overscoping, or worse: looking at what other people do... I can tell you already: people create and share a lot of things and a lot of what you'll see is amazing creations of people who worked very hard for a long time to reach that point, and oh boy is that discouraging sometimes. You just have to follow a bit of the Indie game dev community in Twitter to see this and other typical issues that most people encounter.

### Reducing scope

In my case, I wanted to create an adventure game, with a short story that any friend could play. The idea would be as simple as setting the player on a small island with 3-4 areas with the task of finding a **ruler** for it. Despite my big love for RPG games, I don't think I'm ready to fall into that trap yet, so this time I decided to create a game where you would mainly **talk** your way out of things, so no combat expected.

I found that reducing scope is a great way of achieving more and lets you work in an MVP (minimal viable product) way much easier. But it also presents some problems, as scoping too small can leave you with something that is not fun to play, so the process should be iterable.

Ok, so I've got a goal, basic mechanics that I would have to spice up, and a relatively small scope. Time to do the boring but one of the most important parts: documenting...

### GDD - Game Design Document

I was advised several times that writing things down makes the process easier, and I agree. I already overdid in the past involving multiple tools that I was not used to and it ended up being too overwhelming.

A GDD is a template you can get from multiple places (ping me on Twitter and I can share the one I used, although it is pretty basic). The idea is to define your game in different sections so it's easy to do a document-code-code-document or whatever combination works for you but in an iterative way. I write parts of the Story when I'm inspired or get ideas, and the same goes for pretty much anything, few times I switch to writing when I'm kinda done with coding and I'm just looking for things to refactor with no real purpose, like "ey, you seem lost, what about reading your own ideas and adding stuff to it that you can create?"

### Art and visuals

For the art I chose 2D, pixel art, and top-down style so I searched for some sprites and PJs from Itch.io that I would start using at least for now. Picking sprites is tricky, because you may want to start over scoping and thinking about all the items you might need and then realize that there's no pack with everything you need, even less free, and mixing packs is a no-go for visual consistency. 

In my case, I started doing an RPG game tutorial and I got some sprites in it that I used for the player and first map until I decided to switch to a limited free set that was much better visually. My idea would be to use as much as I can and, in the future, use those assets as a base to create new ones on my own.

Again, my advice would be to pick up whatever works and start with it, once the game is in shape and with clearer goals is easy to switch to different graphics and add better details.


---

Well, I didn't want to extend myself that much, so I better end up here for today. I'll try to write more often with updates and learnings if this gets helpful for me or anyone reading :)

