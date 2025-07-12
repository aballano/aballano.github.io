---
layout: post
title: Gamedev Update 2
categories:
  - Gamedev
  - Godot
  - GDScript
  - Gaming
  - Software Development
published: true
---

![](https://raw.githubusercontent.com/aballano/aballano.github.io/master/images/zdv_jam_1.png "ZDV Jam Logo")

---


Hey everyone! It's been some months since my last post on the main project  which you can find [here](https://aballano.github.io/game-dev-update-1/):

The reason for this post is no other than me taking a small break from it because I joined a game jam with three friends that ended in May. The main thing for me is to actually ship a smaller project and learn from doing that. This jam was a good chance to try integrating AI into how I make games.


# Into the Jam: A "Viral" quest and a dummy game

The jam was two months long, and the goal was to make a game "viral", basically, get as many visits as possible. I was allowed to use a tiny JavaScript project I made a few days before using Gemini on the web. It's a water-drinking incremental game. Yes, as dummy as it sounds. Why Gemini, why web, and why JavaScript? Don't ask, it was just a proof of concept, but I liked the game idea.

So I iterated on it, polished as many details as I could, and posted it as a prototype on [itch.io](https://aballano.itch.io/water-drinking-incremental). I also published it on [incrementaldb](https://www.incrementaldb.com/game/drink-water) for extra views. 
As of July 12th, 2025, it got 9300 Views and 6800 browser plays. And I'm honestly shocked! I didn't expect nearly that many views.

The game got mixed-positive reactions: +83/-50 on incrementaldb with 12 reviews (58% recommended). Even though I marked it as a prototype on the page, I noticed it wasn't obvious enough in the game. A lot of negative reviews talk about lack of content or the game being too short. I should have probably added a dialog at the game start. Positive reviews liked the concept and the aquarium details, which was nice. The best thing is that many reviews, both negative and positive, had great ideas and most feedback was very constructive!

# The Initial failure: Integrating AI the "wrong" way

I probably started using AI in the worst way, so I'll share my experience so you can avoid my mistakes 😬

* First, I did very little planning, both for the visuals and for the game itself. Since it was a prototype, I thought it was fine to improvise.
* Second mistake was choosing a language and technology I wasn't good with: JavaScript. JavaScript can be great if you know how to use it, but like any language, it can be a nightmare if you aren't proficient, especially when reviewing code from an AI. These tools might be mature for some things, but not for others, and not all the time.
* Finally, the setup: using an AI tool on the web is very basic these days. The main issue was the short context, meaning it was almost impossible to handle a project with multiple files at the time.

Despite all this, I managed to make a simple, fun, and entertaining game that made some people happy, including me 😀 But I wasn't entirely happy...

# Course Correction: Leveling up my AI Gamedev workflow

The first try left me wanting to really own the project and be proud of making a game where I liked the code and it had a minimum of quality for my taste.
As a developer with some experience, the process of telling an AI what to do without planning, using a language I didn't properly know, not having a good process to talk to the AI, and ending up with a game idea I liked but code I didn't, was something I was committed to improve.

I then decided to port the game to Godot and tweak the process with AI:
* I start with VS Code with GitHub Copilot integration. There I used Claude 3.7, though I don't have a strong opinion on which AI is best now. From VS Code, I have a Godot plugin that lets me open Godot and link the session, which is handy.
* After initialization, I do what Software Engineers do: planning. What do I want to do now? I pick something and think about how I would implement it.
* Then I open the chat and write it down in a very descriptive way, step-by-step. I try to be precise but without losing time specifying exact names for references.
* I read the prompt, make adjustments, and send it.
* I might take a quick break. When I'm back, all changes are presented, and I use Git to review them, almost like a Pull Request, but more like a dummy coding partner.
Now, the review. From my experience, 90% of the time the prompt is the cause of any errors or things not done well in the code. You have to be very explicit. LLMs don't know about clean code, functional programming, or anything if you don't tell them to. But the more consistent your code is, the easier it is for them to adapt new code to it.
* After reviewing, I might make the changes myself or ask the AI to iterate on the fixes, depending on the situation.
* When the process is done, I might go to set up some screens manually. The AI can describe how to do it, but it's not as easy without something like an MCP server for Godot.
* Scene done, time to test. Run the game and... surprisingly, most of the time it runs and works like a charm. Other few times, it's back to the chat to iterate.

This is *when* I decide to use AI for a feature, which is not always the case, many other times I would do it myself if it's something small or I already know. This new way of working has been much better. It feels like I'm working with a tool now.

# How It's Going and Key Learnings

So, that's what I've learned trying to use AI in this jam. It started a bit messy, but it's been a good learning experience. The "Water Drinking Incremental" game, now in Godot, is coming along much better. And I'm actually enjoying making it this way, with code I can be happy with.

A few key things I've learned:
1. Plan first: Figure out what you want to do before you ask the AI. Unless you're brainstorming.
2. Be clear with the AI: Tell it exactly what you need, step-by-step.
3. Use tools you know: Or at least, tools you can get comfortable with quickly. Learning a new language and AI at the same time was tough.
4. AI helps, it doesn't replace you: It's good for speeding things up, but you still need to know what you're doing.
5. Consistent code helps the AI: If your code is neat, the AI does better.
6. Owning your project feels good: It's satisfying to make something you're proud of.

This experience showed me that AI assistants reflect how you work. If you're organized and clear, they can be a great help.

What about you? Used AI in your gamedev projects? Any experiences or tips? 
