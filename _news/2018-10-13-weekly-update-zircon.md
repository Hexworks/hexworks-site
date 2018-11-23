---
excerpt: "Some old issues closed: TextArea now works as intended, plus some usability upgrades."
title: "Weekly update: Zircon"
tags: [zircon, project-update, project-update-zircon]
author: hexworks
short_title: "Weekly update: Zircon"
---

This week was mainly spent with preparing for the next major release and upgrading stuff.
Nothing fancy was done this week, just hard work: code cleanup, testing, and some streamlining:

- A pretty old issue was finally closed: the `TextArea` was refactored and now it implements the [Operational Transformation](https://en.wikipedia.org/wiki/Operational_transformation)
  model for text editing.
- Added `ComponentMetadata` which holds all the necessary data for `Component`s. This greatly simplified the way `Component`s are created
- Fixed an error in the `RadioButton` which was reported by @Milo
- Added `Observable` which is just a pub/sub tool for event handlers
- Now you can have `Position`s with negative `x` and `y` coordinates. This was an unnecessary limitation as
  /u/coldwarrl pointed out so we removed it and added checks where negative `Position`s wouldn't make sense
- Fixed `Boundable` because it was a code smell for a long time now. It turned out that some classes like `TileGrid` and `TileGraphics` don't
  need `Boundable` at all, they just need a simple `Size` (`width` + `height`). This lead to a significant simplification in code.
- Introduced `TileComposite` which is the common parent of `TileImage` and `DrawSurface`. This was also a code smell which I had no proper solution
  for a long time, but now everything is in place
- The JaCoCo setup for coverage was also upgraded. Now it properly reports coverage data
- Since we are close to release and all the features are complete it is customary to refurbish tests. 3 bugs were luriking in the codebase (so far)
  which got fixed after tests were added for some cases. I was surprised to see that Zircon now almost has 1000 tests in place.
- The `Builder`s got cleaned up and their API got streamlined.
- We'll add Kotlin builders for Kotlin users soon next to the Java-friendly `Builder`s we have currently.

A next major release is coming up for this Autumn, so **stay tuned.**
