---
title: "The framework does it. And now what?"
date: 2026-10-02
draft: false
description: "Frameworks can simplify development, but their limitations remain part of the product. A look at architectural risk, proof-of-concept testing and what happens when abstraction meets reality."
---

# The framework does it. And now what?

The framework does it. Yes, I know. I've heard it hundreds of times before. Some product teams treat it almost like an explanation. Or a holy grail. Or some sacred, untouchable mantra.

For me, that's useful diagnostic information.

Yes, a dependency rendered this component a certain way. Fine. Perhaps the inaccessible behaviour really does originate outside the application code. Maybe the performance issue sits somewhere upstream. The component library might genuinely make a particular interaction difficult to implement correctly.

That's worth knowing. It tells us where to start looking.

But please don't stop the argument there.

Modern frameworks can be extremely useful. I wouldn't argue against that at all. They speed up production, provide valuable abstractions and reduce repetitive work, just to name a few benefits. A good one lets developers concentrate on the product instead of solving the same technical problems for the seventeenth time.

Choosing one, however, has serious engineering consequences, especially for a high-risk product such as a financial or healthcare application. The same applies to anything else carrying substantial compliance requirements.

Security, reliability, accessibility, performance and native platform support are only some of the challenges the team needs to consider. In a high-risk application, these aren't optional extras to be added once everything important works.

They are part of everything important.

Because on the other side of the screen, users have the rather unreasonable expectation that the application should simply work.

## The reasonable arguments

Migrations are part of a product's life. Technology changes. A major redesign can become the perfect opportunity to move to a different stack, and the decision can look perfectly sensible.

* We have to redo every component anyway.
* All the screens will change.
* We can implement the logic once.
* We can eliminate separate code bases for different platforms.
* We can cut development and maintenance costs.

None of those arguments is stupid.

A team can share business logic and component designs while working in a single code base. Communication becomes easier. There is less duplicated effort and, at least in theory, fewer opportunities for implementations to diverge.

We don't have to discuss platform semantics every time we create a component because the abstraction layer handles them. If something doesn't quite behave as required, we can override it. If a missing capability is scheduled for the next release, maybe waiting is reasonable.

The business case looks good. So does the engineering case.

And if the product needs a substantial redesign anyway, doing the job once rather than separately for every supported platform can be very difficult to argue against.

Then the assumptions behind that calculation meet reality.

## The reality check

Maybe a security or accessibility audit is performed halfway through implementation. Maybe it happens later. Or performance testing finally includes older supported hardware rather than whatever shiny devices happen to be sitting on developers' desks.

And suddenly the architecture meets reality.

The ugly cases arrive in all their nasty glory.

One component doesn't expose the semantics required by the platform. Another technically works with a screen reader, provided nobody does anything particularly adventurous, such as actually using it. Something that performs beautifully on a new flagship phone moves with all the enthusiasm of a recently reanimated corpse on an older supported device.

A workaround fixes one issue and introduces another. A control cannot simply be corrected in application code because the offending behaviour lives upstream.

Some defects can be patched locally. Others require replacing components. A few depend on changes outside the team's control. Still others reveal architectural work that somehow failed to appear in the original estimate.

And the board gets a very different spreadsheet.

* Remediation work.
* Upstream dependencies versus locally maintained patches.
* Additional regression testing.
* Compliance requirements that still have to be met.
* Non-negotiable minimums.
* Additional development and maintenance costs.

The audit didn't create those costs. It exposed them.

That's an important distinction.

Before anyone audited the product, the architecture was the same. Its dependencies had exactly the same limitations. The difficult cases were already difficult. The organisation simply didn't have enough information to include them in its calculations.

Now it does.

Suddenly some regression tests are approached with fear and trembling. More person-days, computing power, testing, engineering effort and long-term maintenance are required than anyone originally planned.

The chosen technology may still save considerable effort elsewhere. But the original equation has acquired several new terms, and unfortunately none of them is zero.

## Was the first choice stupid?

Not necessarily.

Hindsight has the wonderful property of making architectural decisions look obvious.

Maybe the technology appeared mature enough when it was selected. Its roadmap might have suggested that important missing capabilities would arrive in time. The proof of concept worked. The team had relevant expertise. The benefits of shared code genuinely seemed to outweigh the known risks.

And sometimes the decision really was poorly researched.

Both things happen.

A bad outcome doesn't automatically prove that the original choice was irrational. Engineering decisions are made with incomplete information, and no prototype can predict several years of production development.

Nor does that mean we should shrug and call everything unforeseeable.

A more useful question is how much uncertainty we can remove before committing ourselves to an architecture that may become extremely expensive to change.

And this is where I think many proofs of concept demonstrate precisely the wrong things.

## Happy path proves nothing

Or, to be annoyingly precise, very little.

Of course the new stack can display a login screen. Of course the animation looks beautiful on the latest phone. Of course standard controls behave nicely when used exactly as the documentation expects.

Congratulations. We have successfully demonstrated the demo.

That's the easy part.

If I'm evaluating technology for a high-risk application, I don't particularly care whether it can implement the simplest screen in the product. I want to know what happens when I give it the ugliest realistic case I can find.

Use the oldest and least powerful device the product officially supports.

Increase the text size.

Use a small screen.

Change the system settings away from whatever the development team considers comfortable.

Turn on a screen reader.

Take the touch screen away.

Test the complicated form with validation, dynamic content and error recovery rather than the elegant welcome screen.

Try the interaction that depends most heavily on native platform behaviour.

And don't ask only whether it technically works. Ask what had to be done to make it work.

Did the chosen stack handle the difficult case naturally? Was a documented configuration option enough? Did we replace one component? Write a small platform-specific implementation?

Or have we just produced the first of twenty-seven ingenious workarounds that somebody will have to understand, test and maintain for the next five years?

Those are very different answers.

Product teams understandably like their shiny toys, large screens, fast hardware and comfortable new stacks. Unfortunately, users have an irritating habit of owning different phones, changing system settings and interacting with software in ways that weren't demonstrated during the architecture meeting.

A high-risk application has to survive that inconvenience.

The proof of concept should therefore search deliberately for the conditions most likely to invalidate the architectural decision.

That doesn't eliminate risk. Nothing does. It means we know considerably more about it before the cheap moment for changing our minds has passed.

## And now what?

There are really two versions of this question.

The first happens during the proof of concept.

We have discovered that an important requirement is difficult to satisfy.

Good.

This is exactly when we wanted to discover it.

Now we can investigate whether the community has already solved the issue. We can test alternative libraries. We can find out whether an upstream fix exists or is realistically expected. We can estimate how much platform-specific code we'd have to own ourselves.

And we may decide that the difficulty is acceptable.

One awkward component might not outweigh all the benefits we get elsewhere. Maintaining a small native implementation could be perfectly reasonable. An upstream limitation with a credible resolution may represent a risk we're willing to take.

Or the experiment may have told us something more important: this technology isn't a good foundation for this particular product.

That doesn't make it universally bad.

It means our requirements and its capabilities don't match well enough.

At the proof-of-concept stage, choosing something else may still be relatively cheap.

The second version of "And now what?" is considerably less pleasant.

The product is already well into development. Maybe it has shipped. The audit has landed. The findings are real, the compliance requirements haven't politely disappeared, and replacing the architecture would be enormously expensive.

Now every available option may hurt.

We can configure or override behaviour where possible. We can replace individual components, maintain local patches, contribute fixes upstream, isolate particularly troublesome areas behind native implementations or plan a larger migration.

Sometimes we may even have to accept a temporary limitation while an actual remediation plan is implemented.

There is no universal answer.

But "the framework does it" isn't one of them.

If the root cause lies upstream, we have learned something important. That information may determine who can implement the fix, how expensive it will be, how long it might take and which engineering options remain available.

It doesn't change what the user encounters.

And for a high-risk product, it doesn't magically transform a non-negotiable requirement into an optional one.

The framework does it.

Fine.

Now we know where to start looking.

What are we going to do about it?

