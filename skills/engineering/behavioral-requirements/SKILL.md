---
name: behavioral-requirements
description: "Maintain the behavioral contract: the persistent statement of what the system must do. Use when a grilling session settles expected behavior, when a spec or ticket must cite the rules it implements, or when the code and the contract disagree."
---

# Behavioral Requirements

The **behavioral contract** is the persistent statement of what the system must do, in the project's own vocabulary. This skill maintains it: it persists the rules a session settled, and loads them for the steps that implement them.

Four artifacts answer four different questions, and each keeps its own. The contract answers *what must be true*. `CONTEXT.md` answers *what the words mean*. The ADRs answer *which hard decisions were made*. The spec answers *how we will build it*.

**The contract is the authority on expected behavior.** Where the code and a requirement disagree, the disagreement is the finding. Report both sides and carry the requirement into the implementation as the behavior to restore.

## Locate the contract

Read the repo's agent configuration before writing anything: `CLAUDE.md` or `AGENTS.md` at the root, plus `docs/agents/domain.md` when it exists. It names where this repo keeps its docs, and often already names a behavioral contract.

- **A behavioral contract already exists** → extend it, matching its file layout and ID scheme.
- **A durable spec or requirements document exists** → ask the user whether to extend it or open a dedicated file, and say what each costs. Two contracts drift, and a second one that quietly disagrees with the first is worse than no contract at all.
- **Nothing exists yet** → propose one file per domain concept under `docs/requirements/`, named for the concept rather than the task that surfaced it, and confirm before writing.

The first run in a repo is the only one that asks. Record the answer in the agent configuration so every later run reads it instead of asking again, and create the location lazily, at the first rule that earns a place in it.

*Done when: the location and ID scheme come from the repo or from the user, and that choice is recorded in the agent configuration.*

## Write a requirement

A rule belongs in the contract when it describes behavior a user or the business can observe: what they can and cannot do, the business rules and invariants, the state transitions they see, validation, permissions and eligibility, externally observable side effects, the error conditions and their outcomes, and the edge cases that change the answer.

Prefer a concrete rule over implementation-neutral prose. "Cancellation closes the order and releases the hold" is a rule. "Cancellation should be handled appropriately" is a sentence about a rule.

Give every rule a stable ID, and keep one rule per ID so a spec can point at exactly one thing. Name IDs after the domain concept they belong to, for example `REQ-ORDER-001`. Keep an ID with its rule when the wording is sharpened, and give a new rule a new ID rather than recycling one.

*Done when: each rule states observable expected behavior, carries a stable ID, and you can name the file it went into.*

## Classify what a session settled

A session settles many kinds of thing. Route each one by what it is:

| What settled | Where it goes |
| --- | --- |
| Behavior a user or the business observes | The contract, under a stable ID |
| The meaning of a term | `domain-modeling`, into `CONTEXT.md` |
| A hard-to-reverse technical decision | `domain-modeling`, as an ADR |
| How we are going to build it | The spec, at `to-spec` |
| A possibility that was rejected | Nowhere |

A decision is ready to persist when its branch of the design tree is settled, not when the latest answer merely sounds plausible. Persist as the decisions land and keep the interview moving: the next round proceeds either way, and a requirements interview run alongside the grilling is a second interview.

*Done when: every settled decision has a destination, and the interview ran to an empty frontier uninterrupted.*

## Reconcile with what exists

Search the contract for a rule that already covers the ground before adding one.

- **The same rule in clearer words** → keep the ID, sharpen the wording.
- **One rule that has become two** → keep the original ID on whichever half is still that rule, and give the other a new one.
- **The user has established different expected behavior** → update the rule, and show them the change when it is material.

A behavioral change reaches the contract from the user. When the implementation and the contract disagree, lay the disagreement out plainly and keep the requirement intact:

```
contract → expected behavior
code      → current behavior
```

Report the disagreement, and ask the user which side is right when that is unclear. Difficulty implementing a rule is a reason to report it, not a reason to edit it.

*Done when: every rule in the contract is one the user stands behind, and each conflict with the code is reported in both directions with the requirement preserved.*

## Keep it testable

State each rule precisely enough that a test could serve as executable evidence for it. Let the repo's existing convention decide whether tests carry the ID in their names; where a repo has no convention yet, the rule stands on its own and the tests follow the repo.

Tests are evidence that the code honours the contract, never the source of the rules in it.

*Done when: each new rule could be evidenced by a test, and no rule has been written as a test case.*

## Return

Report the rules you wrote or changed, by ID, and say which file they went into. Then hand control back to the skill that called you: writing the spec, implementing, and re-interviewing the user belong to the steps that follow.

*Done when: the rules are persisted, the caller is back in control, and no implementation content was carried in with them.*
