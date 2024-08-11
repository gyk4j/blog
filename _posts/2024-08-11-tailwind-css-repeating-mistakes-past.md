---
layout: post
title:  "Tailwind CSS: Repeating mistakes of the past?"
---

Had a brief cursory look at [Tailwind CSS][tailwind] for web frontend 
development. It seems to me that it has garnered some interest in recent years 
as a replacement for [Bootstrap][bootstrap] and [Foundation][foundation] 
frontend framework.

My first impression? 

# Pro

Very simple. Looks very similar to traditional CSS inline style. Easy to pick
up if one has had experience to basic CSS.
  
# Con

Simply adding inline CSS styling to contents and text spans and divs does not
help in conveying the semantic of the words. For example, people were enclosing 
text using `<b>` and `</b>`, and it was difficult for programs to distinguish 
between the block of text as being important or simply for asethetic formatting 
purpose only.

# My personal observation and commentary

Isn't this the exact problem that HTML 5 attempted to solve using meaningful
HTML tag elements? And then we attach CSS styling to those tag elements 
instead?

It's funny how we reinvent the wheel and made an U-turn. I hope we're not going 
back and repeating the mistakes of the past. I feel like we're going back in 
time to the mid or early 2000.

# Why it is popular or useful (to some people)

But it is easy for frontend Web developers to tweak and preview the visual 
aspect of contents using Tailwind. So it has its own fair share of supporters
who probably focus more on the asethetic than semantic.

[tailwind]: https://tailwindcss.com/
[bootstrap]: https://getbootstrap.com/
[foundation]: https://get.foundation/
