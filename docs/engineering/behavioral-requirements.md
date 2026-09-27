## What it does

`behavioral-requirements` keeps the **behavioral contract**: the persistent statement of what the system must do, written in your project's own vocabulary and carrying a stable ID on every rule.

The contract is authoritative for expected behavior, and that is the fact that makes it behave differently from the obvious default. An obvious default would document what the code currently does. This one assumes the code is the thing that is wrong, reports the disagreement rather than resolving it by editing the rule, and leaves the decision about which side is right with you.

## When to reach for it

Type `/behavioral-requirements`, or the [agent](https://www.aihero.dev/ai-coding-dictionary/agent) reaches for it on its own when the task fits. Most of the time you never fire it directly: [grilling](https://aihero.dev/skills-grilling) calls it as a [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) session closes, and the [spec](https://www.aihero.dev/ai-coding-dictionary/spec) and ticket steps call it to read.

Reach for it when a decision has established, changed, or clarified what the system must do. The branches:

| Where you are | What happens |
| --- | --- |
| A [grilling](https://aihero.dev/skills-grilling) session has just settled | The rules it settled are written to the contract, with IDs |
| The code does something the contract says it must not | The disagreement is reported, both sides, and the rule is left standing |
| A [spec](https://www.aihero.dev/ai-coding-dictionary/spec) is being written | The applicable rules are loaded and cited by ID |
| Tickets are being derived | The applicable rules are loaded and their IDs carried onto the tickets |
| A session settled architecture, naming, or a build plan | Nothing is written; those belong to [domain-modeling](https://aihero.dev/skills-domain-modeling) or the spec |

## Prerequisites

The skill is [stateful](https://www.aihero.dev/ai-coding-dictionary/stateful): it writes into your repo. On its first run in a repo it reads your agent configuration, and where it finds no behavioral contract to extend it asks you where the rules should live, then records the answer so later runs read it instead of asking again. Running [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) first is worth it, since that is what establishes the doc layout the skill looks for.

## The contract

A **contract** is the agreement the system is held to, and it outlives any single [context window](https://www.aihero.dev/ai-coding-dictionary/context-window). The spec beside it does not: the spec is a record of the decisions made at one moment, it goes stale the first time implementation teaches you something, and it is disposable once the work ships. The contract is the artifact meant to still be true next year, which is exactly why it cannot be regenerated from the code, since the code is the thing it exists to hold to account.

Four artifacts sit beside it and each answers a different question. Confusing them is how a repo ends up with two competing descriptions of the truth:

| Artifact | The question it answers |
| --- | --- |
| The contract | What must be true? |
| [`CONTEXT.md`](https://aihero.dev/skills-domain-modeling) | What do the words mean? |
| The [ADRs](https://aihero.dev/skills-domain-modeling) | Which hard-to-reverse decisions were made? |
| The [spec](https://aihero.dev/skills-to-spec) | How are we going to build it? |

## Where each settled decision goes

Grilling settles many kinds of thing, and the routing is what keeps the contract from turning into a second architecture document. Every settled decision lands in exactly one place:

| What the session settled | Where it goes |
| --- | --- |
| Behavior a user or the business can observe | The contract, under a stable ID |
| The meaning of a term | `CONTEXT.md`, via [domain-modeling](https://aihero.dev/skills-domain-modeling) |
| A hard-to-reverse technical decision | An ADR, via [domain-modeling](https://aihero.dev/skills-domain-modeling) |
| How we are going to build it | The [spec](https://aihero.dev/skills-to-spec) |
| A possibility that was rejected | Nowhere |

A rule earns its place when it names something a user or the business can observe, and it wants to be a concrete rule rather than neutral prose: "cancellation closes the order and releases the hold" is a rule, and "cancellation should be handled appropriately" is a sentence about one.

## Common questions

**The agent changed a rule because the current architecture makes the old behavior hard to build. Is that allowed?**
No, and seeing it happen is the signal the layer exists to give you. The rule moves only when you establish different expected behavior, in the same [primary source](https://www.aihero.dev/ai-coding-dictionary/primary-source) the rest of the flow takes behavior from: you. Implementation difficulty is a reason to report the conflict to you, and then for a human to decide whether the business behavior genuinely changes. An agent that edits the rule instead has quietly redefined the requirement and made its own tests agree with it.

**Do I now have two things to maintain, the contract and the spec?**
One durable thing and one disposable thing, and the difference is what they are for. The spec is scoped to a piece of work and gets thrown away when it ships; the contract accumulates across every piece of work, which is why the agent cites its IDs into the spec rather than rewriting each rule as a user story. If you find yourself maintaining both forever, the contract has absorbed things that were really build decisions, and the routing table above is the thing to check.

**Will it interrupt my grilling session with a second round of questions?**
No. It classifies each decision as it settles and writes at the end, so the interview runs to an empty frontier uninterrupted. Where nothing behavioral settled, such as a session about where tile caching should live, it writes nothing at all rather than manufacturing a rule to justify the call.

## It's working if

- The rules are in your own nouns, and each one carries an ID you could point a ticket at.
- You can see the boundary holding: a session about architecture left the contract untouched.
- A spec arrives citing rule IDs instead of restating each rule as a user story.
- When the code and a rule disagree, you get shown both sides and asked, rather than finding the rule quietly rewritten.
- The first run asked you where the rules should live, and later runs did not ask again.

## Where it fits

`behavioral-requirements` is a [vocabulary layer](https://aihero.dev/skills-ask-matt) that runs beneath the main build chain, reached by the steps in it rather than driven by you:

```txt
grill-with-docs -> to-spec -> to-tickets -> implement -> code-review
        |              |           |
        +--------------+-----------+--> behavioral-requirements
```

It sits beside [domain-modeling](https://aihero.dev/skills-domain-modeling), which owns the vocabulary of the domain's words, because the two are settled in the same conversation and kept in different files: that one records what a term means, this one records what the system must do. Downstream, [code-review](https://aihero.dev/skills-code-review) is the natural place to check code against the contract, and deliberately has no hook into it yet. When you're unsure which skill or flow fits, [ask-matt](https://aihero.dev/skills-ask-matt) routes you.
