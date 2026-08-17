---
layout: post
title: "Bear With Me - From a 2007 Java Console Game to Steam"
date: 2026-08-17
description: Dificulty ★★☆☆☆
---

# Bear With Me - From a 2007 Java Console Game to Steam

17 Aug 2026

Dificulty ★★☆☆☆

This one is not about MicroStrategy or Qlik. This is a side project I finally finished after 17 years.

## Where it started

Back in 2007 I wrote a tiny game in Java. It was a text-only console program. It asked for your name, the name of a friend, and then told a very short story: you walk through a forest, you hear a scream, you run to help, and you come face to face with a bear.

That was it. No sound, no images, just `System.out.println` and a few `if` branches.

```java
public static void glavno()
{
    System.out.println();

    String ime;
    System.out.print("Kak ti je ime? ");
    ime = BranjePodatkov.preberiString();

    System.out.print("Ime enga frenda? ");
    String frend;
    frend = BranjePodatkov.preberiString();

    System.out.print("Nekoc je sel po gozdu " + ime + ". ");
    System.out.println("Nakar je srecal " + frend + "-ta. ");
    System.out.println("Ko se pogovarjata zaslisita krik. ");
    System.out.println("Prihitita in naenkrat ");
    System.out.print("srecata medveda!! Kaj storis? ");
    System.out.println();
}
```

Every choice branched into a different (usually terrible) ending. That was the whole joke - most decisions get you killed, in increasingly stupid ways.

## Why I picked it up again

The story always deserved more than a black console window, but drawing art and recording voices for a dumb joke game was never realistic for one person. In 2025/2026 that changed. With AI tools I could finally add the two things it was always missing: **voices** and **images**.

So I rebuilt it.

## The rebuild - Ren'Py

I moved the whole thing from Java to [Ren'Py](https://www.renpy.org/), which is a Python-based engine made for visual novels. It handles the things I did not want to build myself: save/load, a menu system, text display, transitions, persistent data across playthroughs.

The core is still just labels and menus. A choice looks like this:

```python
menu:
    "What do you do?"

    "Start running away":
        jump coward_ending

    "Throw rock at your friend":
        jump murderer_ending

    "Throw rock at the bear":
        jump bear_food_ending

    "Make a call":
        jump call_option
```

Each ending unlocks a flag in `persistent.endings_unlocked`, and there is an ending gallery screen that shows what you have found so far. Once you unlock all the "normal" endings, a hidden **true ending** opens up where the character starts to remember all the previous timelines - basically a Groundhog Day joke baked into the save system.

## AI for voice and images

At first I wired every line up manually with a `play sound` call pointing at a specific file, like `"audio/00_Preglog/rob_Suddenly_you_hear_a_scream.mp3"`. That works, but it gets messy fast with hundreds of lines.

So I switched to Ren'Py's automatic voice system. You set one config line:

```python
$ config.auto_voice = "voice/{id}.mp3"
```

After that, Ren'Py automatically plays the matching voice file for each line of dialogue based on the line's ID - I just had to name the audio files to match. No more manual `play sound` per line, and the images are handled the same "just point at the file" way for each scene.

### One Ren'Py + Steam gotcha: saves

Ren'Py has its own built-in "Sync" feature that uploads saves to Ren'Py's public servers and gives the player a code. On Steam this is redundant and can confuse players, because Steam has its own Steam Cloud.

I disabled the Ren'Py one:

```python
define config.has_sync = False
```

And used **Steam Auto-Cloud** instead (Steamworks > Application > Cloud > Steam Auto-Cloud), pointing at Ren'Py's save location:

- Windows: `%APPDATA%\RenPy\<project>`
- Linux: `~/.renpy/<project>`

Now saves and the endings gallery (`persistent`) sync automatically through Steam, no codes needed.

## Result

A short, fully voice-acted comedy visual novel about two friends, a bear, terrible decision-making, and pizza delivery drones. 14 endings, most of them wrong, one of them true.

17 years is a long time to wait for a punchline, but here it is.

## Play it

The game is **free** and takes about **20 minutes** to play: [Bear With Me: The Pizza Chronicles Prequel](https://store.steampowered.com/app/4913420/Bear_With_Me_The_Pizza_Chronicles_Prequel/) on Steam.

If you play it, let me know which ending you found first - and which one I forgot to include.

![bear](https://kl82slo.github.io/img/20260817_0026/header_capsule_920x430.png

