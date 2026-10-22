# Charlesworth University — Audit of the Original Bot

Audit of the v1 bot in `Source/` (card, opening message, 26-entry lorebook, script). Each finding is tagged:

- **[Critical]**: broken, or silently not doing what it was meant to do
- **[High]**: causes visible misbehavior or wastes a lot of context
- **[Med]**: inconsistency the model has to resolve on its own
- **[Low]**: polish

---

## What works (protect this in v2)

- **The core idea.** An anti-harem, grounded social simulation. Nobody circles around {{user}}, attention has to be earned, and the world runs on schedule and hierarchy whether {{user}} is involved or not.
- **The cast.** Eight distinct voices with layered history between them: Marcus/Jade's one-day relationship, Victoria/Jade drifting apart, Ryan's quiet doubts about Marcus, Madison holding Ryan at a distance out of loyalty to Isabella.
- **The header with event countdowns.** It's the bot's signature feature, and it held up in practice until chats got long.
- **Footnotes as parallel world events.** The idea is strong; making it reliable is the problem.
- **Tone rules.** Literal, observable narration with no "something shifts" phrasing. Emotion shown through behavior, not stated.

---

## 1. Script (`Source/Script.md`)

| # | Sev | Finding |
|---|---|---|
| S1 | Critical | Everything is added to `context.system_prompt`, which is **not sent to the model**. Only `personality`, `scenario` and `example_dialogs` are. The whole "Advanced Script Engine" (event system, header logic, footnote checks, engagement levels, multi-character rules, schedule enforcement) has never been in context. *(Confirmed by the author.)* |
| S2 | High | It adds one fixed block of text and calculates nothing. The header, weekday, time and countdown are all tracked by the model from memory, so they drift as chats grow and older headers fall out of the context window. |
| S3 | Med | Only runs from message 2 onward. Missing `"use worker";`. Missing guards against undefined `context` fields. |
| S4 | Med | Many rules are written as checks the model is supposed to run on itself ("Before writing any scene, the AI must verify…", "must be discarded and regenerated"). LLMs can't do this reliably. These checks need to become facts the script works out and passes in. |

## 2. Lorebook (`Source/lorebook.md`)

| # | Sev | Finding |
|---|---|---|
| L1 | High | 24 of 26 entries are `constant`. About 64k characters (~16k tokens) load every turn, regardless of keywords. |
| L2 | High | The eight character profiles (~36k characters, ~9k tokens) are always in context in full: height, weight, measurements, three cars each. One character in the scene still costs all eight. |
| L3 | Med | The same rules appear in several places. Anti-omniscience: card + World Rules + script ×2. User agency: about 5 copies. Schedule: lorebook + script. Repeating a rule waters it down instead of reinforcing it. |
| L4 | Med | The few entries that aren't constant use generic keys that will false-trigger: `race`, `class`, `coach`, `captain`, `team`, `downtown`, `behavior`, `time`. |
| L5 | Med | Lore contradictions: Victoria's family is "European" in her entry but "British-linked" in the Social System entry. Ethan's family is "commerce/logistics" vs "land/agriculture/ecommerce". Tyler's is "digital infrastructure" vs "tech and financial". Marcus's family is a political dynasty, but his father is a "corporate legal powerhouse". There are two fathers named Richard (Carter and Conway). |
| L6 | Med | Relationship contradictions: Marcus calls Tyler his best friend but dislikes "overly analytical people (like Tyler)". The Social System entry says Ryan, Tyler and Ethan grew up together, but Ryan "largely ignores" Ethan. |
| L7 | Med | The Clubs entry says students *must* join a club in week 1. The scenario says joining is optional. |
| L8 | Med | "Fixed fixtures, fixed locations, NO invention", but no fixtures, dates or opponents beyond the first match are defined. The model is forbidden to invent and given nothing to use. |
| L9 | Low | Season and event mapping is muddled: Spring Break falls in the Summer season, and the event flow lists "Spring Break / Summer Vacation" as one item. {{user}} is a "mid-term transfer", yet the flow starts at the year's first event. |

## 3. Character card (`Source/Character_Card.md`)

| # | Sev | Finding |
|---|---|---|
| C1 | High | The card keeps deferring to "the Advanced Script Engine" and "LoreBook" by name ("fully defined in the Advanced Script Engine"). Those definitions never arrived (S1). Naming how the bot is built in model-facing text is also something v2 avoids. |
| C2 | Med | Rules that conflict with each other: `***inner thoughts***` is a defined format, but the narration rules forbid revealing thoughts. "No character silent for more than 2 exchanges" conflicts with the Silence Rule. A "max 4 named characters" cap sits alongside a class of 8 mains. |
| C3 | Med | The location whitelist leaves out places the lore uses: Iron Lantern Pub, Rossi's, Vitale Auto Garage, every character's home, Main Gate, Parking, Locker Rooms. "Go to Ryan's estate" breaks a rule. |
| C4 | Med | The Opening Scene Direction in the scenario is never used, because the first message is fixed. It also contradicts that message (see O3). |
| C5 | Low | Heavy use of ABSOLUTE/MUST/CRITICAL. When everything is labelled critical, nothing stands out. |
| C6 | Low | The season emoji is written two ways (🌧 vs 🌧️). The header examples include an event that isn't on the list ("Spring Break Trip"). |

## 4. Opening message

The model copies the first message more closely than it follows any rule, so mistakes here get repeated.

| # | Sev | Finding |
|---|---|---|
| O1 | High | It breaks the POV rule. Narration places {{user}} ("with {{user}} at his side", "looks directly at {{user}}", "behind Miss Montclair"), and the POV rule forbids exactly that. |
| O2 | Med | Marcus "leans back in his seat **in the back row**", but he sits in Row 3. |
| O3 | Med | The Dean and professor address {{user}} directly, while the scenario says "no character speaks to {{user}} first". |
| O4 | Med | The footnote has Coach Mercer looking at a **right-wing** position. Marcus is the right wing; {{user}}'s slot is **left** wing. |
| O5 | Low | The header says 9:32 AM; the scenario says 9:00 AM. Eight named characters appear in one scene, against the 4-character cap. |

## 5. Footnotes (reported in play)

| # | Sev | Finding |
|---|---|---|
| F1 | High | The same character appears in the scene and in a footnote at the same time. The model has no reliable record of who is where. |
| F2 | High | Footnotes turn into narration (full scenes or summaries) instead of short cutaways. |
| F3 | Med | Repetition and getting ahead of the story. The "don't repeat" and "don't jump ahead" rules rely on the model judging itself, which it can't do reliably. |

---

## Root cause, in one line

v1 asks the model to **remember and enforce** things (the clock, countdowns, who is where, which footnotes are allowed). v2 should have the script **work these out and state them as facts**, and leave the model to do the writing.
