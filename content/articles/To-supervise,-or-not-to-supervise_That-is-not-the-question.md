---
title: "To supervise, or not to supervise? That is not the question"
date: 2026-09-30
draft: false
description: "Coding agents need supervision. People approach it differently. I ask questions. Some of them are necessary. Some are not."
---

# To supervise, or not to supervise? That is not the question

As coding agents gain popularity, there are more and more questions about how to use them effectively, one of them being that of appropriate supervision. I've observed two extremes in that regard: either watch the coding agent closely and verify everything it does, or give it the task and come back when it’s finished.

Neither was particularly appealing to me. So I chose a different route. I give my coding agent considerable freedom. I don't tell it how to implement every function. But while it works, I read those little progress reports—and occasionally a word or sentence makes me ask a question. Not every time. Just sometimes.

Put simply, those small messages can become a useful debugging tool.

## The problem

I’m building a tool that automates parts of web accessibility auditing. Some of the work is straightforward automation; some of it tries to reproduce decisions I normally make while testing a website manually. That means interacting with real applications, observing what changes, deciding whether the result is meaningful, preserving evidence, and knowing when the tool can safely continue.

Real websites are messy. Interfaces change dynamically, frameworks replace elements, authentication expires, unexpected dialogs appear, and accessibility metadata is often wrong. The automation therefore has to work conservatively when it cannot establish what actually happened.

I use a coding agent for a substantial part of the implementation. The architecture and behavioural contracts are defined beforehand, but I generally let the agent decide how to implement them. The examples below come from that work. The short progress messages are paraphrased from actual implementation sessions.

## How does that work in practice?

At the time of writing, I'm implementing the behaviours the tool needs for constrained workflows, where session continuity or current state is fragile and a given task cannot easily be retried or repeated. The idea is to gather as much evidence as possible without causing irreversible changes, performing destructive or financially binding actions, or wasting opportunities in workflows where interactions are strictly limited.

The important thing is to make sure the probes are exposed to the intended components and perform their observations and evaluations predictably. But then real life appears. Frameworks like React can rerender nodes as application state changes, so those nodes need to be evaluated carefully when they are observation candidates.

Knowing the architectural boundary, a phrase like the one below would catch my eye immediately:

> I rejected an unstable node.

At first glance, nothing substantial. Just a status message. But in the context of the project architecture, that little statement can be a reason to check whether something is wrong. Or it can simply reveal enough of the agent's apparent mental model to make the question worth asking.

I chose to investigate and asked additional questions as the agent was performing the task:

> → what causes a node to be rejected?
> → are we distinguishing DOM identity from logical component continuity?
> → what happens on an ordinary React rerender?

Potential false assumptions could cost me later, cause some strange bugs, or become part of an architecture I would have to revisit. Asking those questions took about ten seconds. Discovering much later that the same assumption had spread through several probe families, tests, and shared abstractions would be considerably more expensive.

Does that mean the implementation was wrong or that the work in progress needed to be stopped? Not in this case. But the assumptions were still worth rechecking. No linguistic divination was involved, either—just a few words that triggered my memory. The architectural boundaries had been discussed and written down earlier. But even well-written plans, when implemented in smaller fragments, can have unexpected consequences.

## Sometimes the answer is "everything is fine"

Another status message was completely innocent, but I asked anyway. During the current phase of the plan, no scheduler work should be implemented. But I saw something in the small progress report along the lines of:

> Replaced empty runner status fields.

The current phase of the plan was not about the scheduler, so I asked about the explicit boundary. I wouldn't want the current task's implementation to bleed into the next phase. The answer was:

> I kept the phase boundary in place.

Fine. No need to worry. But keeping that boundary was important to the overall scheduler integrity, so the ten seconds spent asking the question were worth it, even when everything was perfectly fine.

Checking an expensive invariant cheaply was the right call to make.

## Sometimes a small word exposes a real problem

Small words can expose meaningful errors, too. Let me illustrate that with another example.

One of the probes was testing a drawer's keyboard behaviour. The action was dispatched, but the drawer didn't close, so subsequent probes couldn't safely continue their intended behavioural observations. The tested surface was therefore supposed to be marked as unstable for subsequent probes. And the agent reported something like:

> Marking a probe as unstable forces the runner to stop.

The statement was innocent and technically true. The runner should not allow later probes to continue testing a surface whose required state can no longer be established. Evidence gathered under unjustified starting conditions would not be trustworthy.

But the failed interaction could itself establish a valid accessibility finding.

Did the agent distinguish between the two? Or did it conflate them?

I asked the question. And the agent fixed the incorrect assumption.

You see, the problem isn't “AI wrote bad code.” It's a subtle modelling error that could quite plausibly appear in human-written software too.

## When and why do I ask during implementation rather than afterwards?

The examples above already show some of the answers. If the question can serve the implementation here and now, I tend to ask it, potentially steering the agent and saving myself some future refactoring work.

Encoding a bad or incomplete assumption is costly. And not only in tokens, but also in testing, planning, implementation, and attention later. Sometimes the ten seconds spent asking a question can make all the difference. Sometimes the answer simply confirms that everything is fine. But the question was still worth the effort.

Other times, I see something strange in those small status messages that makes me put the question into my notes and ask it later, after the planned phase is complete. Requesting information too early can delay the work. It can also introduce the very assumptions I want to avoid. Restraint can therefore be as useful as immediate intervention.

How do I know when to ask a question and steer the agent a little? That depends on the architecture, understanding the codebase, understanding the work the agent is supposed to perform, and knowing the project's short- and long-term goals.

I don't ask about every `if` statement I encounter. I intervene when I see something potentially dangerous. Not because the agent is necessarily about to write a thousand lines of bad code, but because it can write a thousand lines of perfectly valid, working code built on a bad assumption. And that can bite me. Given enough time, it probably will.

## What does this approach require from the human?

I've already mentioned some of these things, but they're worth repeating for clarity:

* You need some mental model of the system.
* You need to know which boundaries matter.
* You need enough domain knowledge to recognise when apparently reasonable implementation language has consequences.
* But you **don't** necessarily need to know how you would implement every detail yourself.

The idea is to exercise engineering judgement, not to tell the agent how to write the code line by line.

In fact, domain knowledge can be more important here than knowing exactly how I would implement the solution myself. Some of the hardest mistakes aren't coding mistakes at all. They're perfectly reasonable implementations of the wrong model.

---

I don't supervise every line my coding agent writes. I don't leave it entirely unattended either. I let it work, and I listen.

Most progress reports require no intervention. Occasionally a phrase touches an assumption or architectural boundary I care about. Then I ask a question.

Sometimes I find a bug. Sometimes I confirm that everything is fine. Sometimes I leave myself something to investigate later. All three outcomes are useful.

The goal isn't to intervene often. It's to intervene while intervention is still cheap.