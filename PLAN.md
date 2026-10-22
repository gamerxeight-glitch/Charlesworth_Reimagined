# Charlesworth University v2 — Upgrade Plan

Working plan for the upgrade. Findings it refers to (S1, L2, F1…) are in [AUDIT.md](AUDIT.md).

## Guiding principles

1. **The script works things out; the model writes.** Clock, weekday, period, countdowns, who is where and footnote material are all calculated by the script and handed to the model as facts. The model only writes the prose.
2. **Model-facing text never describes the setup.** No "script", "lorebook", "engine" or "system" in anything the model reads. Instructions are written as facts and directives in the model's context, and anything in [BRACKETS] is treated as authoritative. Notes about how things work stay in code comments only.
3. **Each rule is stated once, in one place.** Behaviour rules go in the card; situational lore is loaded by the script; state lives in the script.
4. **Load only what the scene needs.** Short notes for everyone present, full detail for whoever is the focus. Nothing is permanently constant unless it's foundational.
5. **No false triggers.** Keywords match whole words only, entries can list exclusions, and generic words are never used as keys.
6. **No hidden plot threads in permanent context.** Secrets are loaded only when their conditions are met.
7. **Protect the core idea.** Grounded and anti-harem. Attention is earned, and the world doesn't bend to {{user}}.

---

## Phase 1 — Content (plot, world, profiles, events, characters)

Everything else is built on this, so it's settled first.

**How we work:** the author writes the source material (concepts, profiles, lore, events) and drops it into `content/` in the repo. Kiro reviews it file by file against this plan and the audit. The repo is the single source of truth. Long back-and-forth design sessions in chat are avoided; each session should work from files.

- [ ] **1.1 World and background:** fix the contradictions in the lore (L5, L6, L7, L9). Write one source of truth for the families, districts and locations, including the places the whitelist was missing (C3).
- [ ] **1.2 Two new main characters:**
  - [ ] Male: an outlaw, the anti-Marcus, down to earth. Decide where he comes from and whether he's in Hall A (this affects the "no scholarship" rule and the seating).
  - [ ] Female: archetype still open (connector between groups, "it" girl, or something else). See the open questions below.
  - [ ] How they connect to each of the existing eight.
- [ ] **1.3 Profile rewrite (all 10):** split each into a short card (voice, core traits, current state) and a full profile (background, hidden layer, relationships). Appearance becomes an optional entry loaded the first time they appear.
- [ ] **1.4 POV paths:**
  - [ ] Male POV: the standard path (soccer tryouts → qualification match → OUL…).
  - [ ] Female POV: its own set of opening events running in parallel with the soccer events. No women's team. Choose the events (shortlist in the chat discussion).
  - [ ] Shared content: everything not tied to a POV.
- [ ] **1.5 Plot and arcs:** the academic year from start to finish, each character's subplot, and the hidden threads with the conditions that reveal them.
- [ ] **1.6 Event calendar:** real day offsets for every major and minor event, the OUL fixtures and venues (L8), and the season boundaries. This is what the header countdown will run on.
- [ ] **1.7 Footnote seed pool:** off-screen moments for each event phase and time of day, tagged by location and the characters involved.

## Phase 2 — Opening messages

- [ ] **2.1 Male POV greeting:** rewritten to follow every rule it sets (O1–O5). The model copies it more than anything else.
- [ ] **2.2 Female POV greeting:** opens the female-path events.
- [ ] **2.3** Both greetings carry a marker the script can detect, so it knows which path the chat is on and saves that choice from then on.

## Phase 3 — Scripts and lorebook

- [ ] **3.1 Director:** calculates the header (`📍Location | 🕒Day Time | Season | Current/Upcoming`) from the calendar plus saved state. Handles time moving forward, the class schedule (states "Period 2 — everyone is in class" as fact), weekends, season changes, the POV path, and a save checkpoint that's tolerant of small copying errors.
- [ ] **3.2 Scene presence:** works out who is in the scene, from the schedule (class means Hall A) and the last reply, and tells the model they can't be anywhere else (F1).
- [ ] **3.3 Footnotes:** picks two seeds about characters or staff not in the scene, matching the current calendar phase and time, and rotates them so they don't repeat. The model only rewrites them as single lines in a fixed format (F2, F3).
- [ ] **3.4 Character loader:** short notes for everyone present, the full profile for whoever is the focus, plus appearance and hidden threads behind conditions. Whole-word matching with exclusions (L2, L4).
- [ ] **3.5 World lore loader:** districts, clubs, teams and NPCs, loaded by keyword.
- [ ] **3.6** Decide what (if anything) stays in the native lorebook and what moves into the scripts (L1).
- [ ] **3.7 Testing:** `node --check` on every script, plus smoke tests with simulated chats: countdown accuracy at message 1, 200 and 900; day rollover; rollover into the weekend; footnotes never featuring anyone in the scene.

## Phase 4 — Character card

- [ ] **4.1** One short card containing the rules that apply in every scene, each stated once (L3, C2, C5). Nothing about how the bot is built (C1).
- [ ] **4.2** The scenario holds only the starting premise. Situational state comes from the scripts.
- [ ] **4.3** Update the public bio: AnyPOV note, the two new characters, recommended models.

---

## Decisions log

| Date | Decision |
|---|---|
| 2026-10-22 | The v1 script never reached the model (S1); v2 rebuilds it from scratch. |
| 2026-10-22 | The header is the signature feature and is calculated by the script in v2. |
| 2026-10-22 | Footnotes stay, rebuilt on scene presence plus a seed pool. |
| 2026-10-22 | Male and female POV chosen through alternate greetings. No women's soccer team. |
| 2026-10-22 | Two new mains: one male (outlaw / anti-Marcus) and one female (archetype to be decided). |
| 2026-10-22 | Order of work: content → opening messages → scripts/lorebook → card. |
| 2026-10-22 | Calendar dates deferred until the plot is worked out (Phase 1.6). |

## New male main: confirmed so far

- British. Lives alone in the family mansion with his butler. His parents live in the UK and visit now and then, sometimes meeting the Dean on campus, because the family are trust holders of the university.
- At Charlesworth because of a family tradition started by his grandfather. The grandfather came from a founding family of Oakmont but went back to the UK soon after the town was founded.
- Dark hair, pale skin. In Hall A.
- The anti-Marcus: laid-back, relaxed, deadpan, unbothered, quick to smile, brooding, mature, open-minded. (Marcus is rude, loudmouthed, brash, aggressive, a show-off, a bully and an agitator.)
- Still open: name, the butler's name, what "outlaw" means in practice, his seat, whether he plays soccer.

## World concept: confirmed so far

- There are two types of founding family: **Original** and **New**. Which family falls into which group is still to be decided.

## Open questions

1. The new female main: connector, "it" girl, or something else?
2. The new male main: what does "outlaw" look like in practice? (See the confirmed details above.)
3. Which female-POV opening events to use (shortlist in progress).
4. Engagement levels: keep them as the model's judgment, or track them per character in the script?
5. How much appearance detail to keep, measurements in particular?
