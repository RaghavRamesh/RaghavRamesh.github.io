---
title: "Inline documentation"
date: 2016-10-27
categories: [documentation, team-work]
tags: [documentation, team-work]
---
Before I start this article, I must tell that it is not about why documentation is important, as there are plenty of articles out there telling you why. It focuses on the quality of documentation; more specifically, inline documentation.

Have you been in a situation where documentation is present, but you’re still scratching your head about why the code is implemented a certain way? I did recently, when I was adding new capabilities to code I had written 5 months ago. Yes, there was documentation available, but it was only stating what the code was doing, not why. The intent behind the implementation was missing.

The reason why I missed out from documenting the intent, is because I wrote all of the inline documentation *after* writing the code. This was a dangerous mental state to be in because the intent was mixed with the implementation design. As the implementation gets complex, the original intent can be easily forgotten. There are two problems that occurred due to this.

Firstly, I was under the false assumption that I was writing useful documentation by writing about what the code does. I did not write about the underlying intent as the intent seemed obvious to me at the time. But it wasn't when I was coming back to the code a few months later.

The second problem that occurred was comparitively rare. Since I was only documenting code that was written, I did not document about cases that should not be handled. This is similar to a limitation of white box testing - you can only test functionality that is present, you can't test missing functionality. Some cases should not have been handled in order for the program control flow to behave a certain way to achieve the intent, but I didn't document that. A well-meaning developer could come onboard and handle the missing case, thereby messing the control flow.

So how to avoid getting into this? These days, I document the intent before starting to implement. Then I go on to document how the intent is achieved - implementation design, while implementing or after implementation.

In the first case, since the intent is documented, it's easier to reason about how the code is trying to achieve it. Also, the mind is not yet clouded with implementation design. In the second case, even if the implementation documentation is done after writing the code, the documented intent serves as a reminder to document the cases that should not be handled.

What do you think? How do you write inline documentation?

