---
excerpt: In this document you will explore the main design guidelines behind Zircon.
title: The Design Philosophy Behind Zircon
tags: [zircon, documentation, zircon-documentation]
author: hexworks
short_title: The Design Philosophy Behind Zircon
---

Zircon is designed around some very simple, yet powerful design principles.

## Occam's razor
First we try to shave with [Occam's Razor](https://en.wikipedia.org/wiki/Occam%27s_razor). If there are multiple solutions
for the same problem we try to pick the one which is simpler. Of course there are caveats for this: we can only make this
decision if two solutions can solve the problem in a way which does not take away functionality.

## SOLID
We try to make all our classes [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)). This is one of the
most important design principles which are the backbone of the Zircon codebase.

## Clean architecture
We try to keep the architecture of Zircon [Clean](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html).
This is especially important in a text GUI library where users can mix and mach `Font`s, GUI frameworks, and such.
This design principle let us abstract away the details of Swing, Java2D, Font handling and by using clean architecture
we were able to test most of our classes both in isolation (unit tests) and in integration.

## Robust API
By dividing the codebase to an `api` and an `internal` package we can supply you with a robust API which you can
rely on. You can expect all classes / methods in this package to *not change* nor in signature or expected behavior.
Check [this talk by Rich Hickey](https://www.youtube.com/watch?v=oyLBGkS5ICk) if you want to learn more.

## Immutability and Functional Programming
Most components in Zircon which *can be* immutable are immutable. We'll work on this in the future. There are a lot of
[advantages of immutability](http://www.yegor256.com/2014/06/09/objects-should-be-immutable.html), and you can
[read about it](https://miles.no/blogg/why-care-about-functional-programming-part-1-immutability) in a 
[lot of places](https://hackernoon.com/5-benefits-of-immutable-objects-worth-considering-for-your-next-project-f98e7e85b6ac) if
you are interested. We use Functional Programming where applicable and where it makes sense. We think that in most
places it will lead to more concise and also more simple code.
