---
title: "Coming Soon: FluentChess"
description: "Scienced-backed chess tactics trainer with spaced repetition and curriculum sequencer."
date: "Mar 15 2026"
demoURL: " "
repoURL: " "
---

**Stack:** Next.js · TypeScript · Convex · Neon · Postgres · Clerk · LichessAPI 

![FluentChess Dashboard](/images/dashboard-screenshot.png)

---

## Overview

Based on the idea that the primary driver behind chess improvement is "tactical fluency", or the ability to see typical patterns automatically, FluentChess combines a data-driven curriculum sequencer whith spaced repetition to introduce and revisit tactical themes at the optimal time. 

FluentChess introduces puzzles by theme (mate-in-one, pin, fork, skewer, etc.) so the user engrains these into their pattern recognition. The app then drills these patterns into long-term memory: using FSRS (the spaced repetition algorithm used in Anki), the user is then tested on these same puzzles at the optimal time, so they see a puzzle again when they're actually about to forget it. 

More about the science of using spaced repetition for chess puzzles can be found [here](https://www.alexcrompton.com/blog/how-to-learn-chess). 

![FluentChess Review Screen](/images/review-screenshot.png)

---

## How it works

The curriculum is based on SQL analysis I ran of the Lichess puzzle database — each theme gets a minimum rating threshold based on where it naturally appears in the data, with a small discount applied so the user meets new patterns just below their full difficulty ceiling. The idea is borrowed from the comprehensible input theory in language learning.

FSRS runs underneath everything, tracking stability and difficulty scores per card and scheduling the next review accordingly, at increasingly larger intervals if the user gets puzzles correct.
