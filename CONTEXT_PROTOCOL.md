---
name: jens-context-protocol
description: |
  Minimize token load and chat length for Jens' Latin site project. Use when he mentions "token-efficient", "beperk context", "2-3 files", or when you anticipate a large session (layout harmonization, multi-file refactoring, lesson expansion). This skill guides Claude to reference codebase instead of reloading, consolidate CSS, use template patterns, optimize images, batch edits, isolate tasks, and prefer Markdown→HTML workflows. Triggers for any athena/Groenhoveschool site work involving code, styling, or content.
---

# Jens' Context-Optimization Protocol

**Goal:** Reduce token overhead on the athena Latin site project while keeping work flow smooth and your changes easy to verify.

**Your workflow baseline:** 2–3 files per chat | HTML/CSS reload frequently | Lesmateriaal is exploratory | GitHub editor preferred (fast, visible, verifiable).

---

## Quick Protocol Checklist

**Before starting:**
- [ ] Is this task 1 file or 2–3 files?
- [ ] Can I reference a filename instead of loading it?
- [ ] Does this need CSS? If yes: is there a central theme file yet?
- [ ] Are images involved? Path refs or inline?
- [ ] Is this lesson content? Markdown first or HTML direct?

**After finishing:**
- [ ] Did I reload the same file 3+ times? (Red flag)
- [ ] Is there new inline CSS that should move to a central file?
- [ ] Did I batch 5+ small edits or create 5 separate prompts? (Should batch)

---

## The 10 Optimization Layers

### 1. **Codebase Reference, Not Reload** (~5–10% savings)

**Do this:**
- "Check `woordenlijst_volledig.js` lines 150–200 for [issue]"
- "Update the footer pattern in `latijn.html` line 340"

**Not this:**
- Paste entire `woordenlijst_volledig.js` into chat
- Ask Claude to review 50 lines you're not sure about

**How it works:**
- I open GitHub web editor directly on repo
- Read from the loaded CodeMirror view (not context window)
- Make targeted edits
- You see live changes in editor or pull diff

**Best for:** Quick corrections, typo fixes, single-section updates, verifying specific lines.

---

### 2. **CSS/Styling: One Source of Truth** (~15–20% savings)

**Current state:** CSS is scattered (inline in HTML, per-page style tags, no consistency).  
**Target:** Centralize into `_theme.css` or `theme.js` with design tokens.

**Do this:**
- Create `_theme.css` with all variables:
  ```css
  :root {
    --color-blue: #13427f;
    --color-magenta: #E6007E;
    --font-primary: 'Lufga', sans-serif;
    --font-fallback: 'Aptos', Arial, sans-serif;
    --spacing-unit: 1rem;
    --card-radius: 0.5rem;
  }
  ```
- Reference once: "Use `_theme.css` everywhere"
- For harmonization: "Apply `_theme.css` to pages X, Y, Z — remove inline `<style>`"

**Not this:**
- "Here's the full HTML of 5 pages, fix the CSS"
- Inline style tags per page; no shared variables

**Impact:** Reload one theme file, not 5 HTML pages per discussion.

---

### 3. **Template Patterns** (~10–15% savings)

**Do this:**
- Document patterns once:
  - `card-pattern.html` (image + title + link)
  - `hero-pattern.html` (banner + text)
  - `gallery-pattern.html` (grid of items)
  - `form-pattern.html` (input + label + submit)
- Reference: "Use card-pattern for the new Zeus cards"
- Reuse without redefining

**Not this:**
- "Design a card layout" (5 times in different chats)
- Reinvent the wheel per page

**Where to store:** `references/patterns/` folder in repo.

---

### 4. **Data Structure Lock** (~5–8% savings)

**Locked schemas (don't repeat):**

**woordenlijst entry:**
```javascript
{
  grondvorm: string,
  aanvullingen: string,
  vertaling: string,
  basis: boolean,
  teksten: [{ludus, tekst, occurrence}],
  volgorde: {ludus: #, index: #}
}
```

**tekst entry:**
```javascript
{
  ludus: number,
  tekst: number,
  title: string,
  content: string,
  vocabulary: [lemma_ids],
  images: [{src, alt, caption}]
}
```

**Do this:**
- "This data follows schema X" (refer to locked structure)
- "Add a `basis` field to 100 lemmas" (I know the schema, no re-explain)

**Not this:**
- Paste the entire woordenlijst every time
- Explain the schema from scratch in each chat

---

### 5. **Image Base64 → Relative Paths** (~20–40% savings per image)

**Icons & small assets** (<5 KB):
- Inline base64 in HTML or CSS: ✅ OK

**Lesson photos, hero images, galleries** (>50 KB):
- Save as PNG to repo `/images/` folder
- Reference: `<img src="images/zeus-statue.png" alt="...">`
- One line vs. 3000+ token base64 string

**Current overhead:** If you're embedding large images inline, each one costs 3–4x more tokens than a path ref.

**Recommendation:** Set up folder structure:
```
repo/
├── images/
│   ├── heroes/ (page banners)
│   ├── lessons/ (Zeus, Caesar, etc.)
│   ├── gallery/ (culture module)
│   └── icons/ (small, OK for base64)
```

---

### 6. **GitHub Editor Workflow: The Fast Path** (~25–30% savings on multi-file work)

**Best practice for 2–3 file edits:**

1. **Open editor directly:** I navigate to GitHub, open CodeMirror on your file
2. **Live edits:** Changes visible in the browser immediately
3. **Verify before closing:** You see the diff, approve, or ask for tweaks
4. **No copy-paste:** I don't send 2000 lines back to chat; I show you the line numbers that changed

**Example dialogue:**
- You: "Fix the footer links in latijn.html, lines 280–300"
- Me: [Open GitHub editor on latijn.html]
- Me: "Done — I updated lines 285, 292, 298 (changed href + added class). Check the live preview?"
- You: "Looks good" or "Change line 292 back"

**When to use this:**
- 1–3 targeted edits per file
- You want to see changes live
- Not a full page redesign

**When NOT to use:**
- Massive refactor (50+ lines changing) → better as "here's the new version, review in artifact"
- File doesn't exist yet → better as artifact first, then copy to repo

---

### 7. **Batch Small Changes** (~20% savings)

**Do this:**
```
"In teksten.html:
- Line 45: fix typo 'Cultuur' → 'Cultuur'
- Line 120: add class 'card-hero' to the section
- Line 200: update footer copyright year
- Line 340: change color from #FF0000 to var(--color-magenta)"
```
(1 prompt, 4 fixes, GitHub editor)

**Not this:**
- 4 separate chats: "fix typo", "add class", "update year", "change color"

**Savings:** 4 context loads → 1. Huge win.

---

### 8. **Lesmateriaal: Markdown → HTML Pipeline** (~15–20% savings)

**Workflow:**

**Step 1: Spec (you → me)**
- "Expand Zeus-Hera lesson for thema 3 (de goden). Include: [list items]"

**Step 2: Draft in Markdown (me → you)**
- I write `.md` with clear structure (headings, bullets, links)
- Markdown is ~60% shorter than rendered HTML
- You review for content, pedagogy, accuracy

**Step 3: Review & Feedback (you → me)**
- "Add more on the Hera monologue", "Reorder sections", etc.
- Still working in lightweight Markdown

**Step 4: Render to HTML (me → you)**
- Once you approve, I convert to HTML with proper styling, embed in site
- One final render, not 10 iterations of HTML

**Why this saves tokens:**
- Markdown loops are cheap (low overhead, easy to edit)
- HTML loops are expensive (large context, styling entangled with content)
- Separation of concerns: you focus on content, I focus on presentation once

**Template for lesson content:**
```markdown
# Lesson Title

## Leeruitkomsten
- [outcome 1]
- [outcome 2]

## Inleiding
[Context]

## Kernstof
### Subtopic 1
[Explanation + examples]

### Subtopic 2
[Explanation + examples]

## Verdieping
[Advanced content]

## Oefeningen
- [Exercise 1]
- [Exercise 2]

## Bronnen
[References]
```

---

### 9. **Task Isolation: Know When to Start Fresh** (~30% savings on multi-task sessions)

**"Isolated" task** (same chat is fine):
- Edit 2–3 related files (e.g., teksten.html + _theme.css for layout harmonization)
- One clear goal
- <50K tokens of context accumulated

**"New chat" signal:**
- You've finished task A (e.g., tekstviewer check)
- Task B is unrelated (e.g., Zeus lesson expansion)
- Current chat is >40K tokens
- OR: You want a clean slate to focus

**Guideline:** Each task in takenlijst → Consider a new chat if it's truly separate.

Example from your 2026-04-09 list:
- ✅ Task 1 (tekstviewer check) → isolated, then close/summarize
- ✅ Task 2 (afbeeldingen embedden) → isolated, same chat if <40K
- ⚠️ Task 3 (Grieks dashboard) → New chat recommended (fresh context, cleaner focus)
- ⚠️ Task 4 (Zeus lesson) → New chat (unrelated, content-heavy)
- ⚠️ Task 5 (layout harmonization) → Depends on prior work

**You signal it:** Just say "nieuwe chat" or "let's start fresh for the layout work".

---

### 10. **Progress Checkpoints: No Redundant Reload** (~10% savings)

**Good:**
- You: "Fix the footer, then move on to the gallery."
- Me: "Done (lines 340–350). Now for gallery: are we using card-pattern or custom?"
- You: "Card pattern. Use the Zeus images."
- Me: [Already in context] "Done. Check the preview?"

**Bad:**
- You: "Fix footer. [I do it.] Now summarize the current state of teksten.html."
- Me: [Reloads entire file] "Here's the current state..."
- You: "Now fix the gallery."
- Me: [Reloads again] "OK, updating gallery..."

**Rule:** Once I've loaded a file, I keep it in context. I don't reload unless you explicitly ask or significant time has passed.

---

## Practical Workflow Examples

### Example 1: Tekstviewer Check (1 file, isolated)

```
You:  "Tekstviewer 7b tekst 4 — last check. Open it and scan for issues."

Me:   [GitHub editor → teksten.html, navigate to the relevant section]
      "Scanning... Found 3 issues:
       - Line 92: Broken image link (src="images/ludus7b.png" missing)
       - Line 156: Typo 'cultuur' → 'cultuur'
       - Line 200: Missing alt text on hero image"

You:  "Fix all three."

Me:   [Make 3 edits, show diffs] "Done — lines 92, 156, 200 updated."

You:  "Looks good."
```

**Tokens used:** ~8K (not 20K if I'd loaded the entire file in context first).

---

### Example 2: Layout Harmonization (5 files, multi-step)

```
You:  "Harmonize layout across latijn.html, teksten.html, cultuur.html, grammatica.html, dashboard.html."

Me:   "Before I start: is there a central CSS yet? [You: "No, it's scattered."]
       OK, I'll:
       1. Create _theme.css with all vars
       2. Update each HTML to link it
       3. Remove inline style tags
       
       This will be 1 new file + 5 small edits. Should I batch it in GitHub editor?"

You:  "Yes, batch it."

Me:   [Open GitHub editor]
       "Step 1: Created _theme.css in root — all variables defined.
        Step 2: Updated latijn.html line 8 (added <link> to _theme.css), removed inline styles (was lines 15–85).
        Step 3: Updated teksten.html line 10, removed lines 30–120.
        ...etc.
        
        All 5 files now link _theme.css. Check the site preview?"

You:  "Looks better. Change the blue a bit."

Me:   "Update _theme.css --color-blue from #13427f to [new value]?"

You:  "Yes, #1a4a96"

Me:   [Edit 1 line in _theme.css] "Done. Propagates to all 5 pages."
```

**Tokens used:** ~15K (not 60K if I'd loaded and discussed each HTML file separately).

---

### Example 3: Zeus Lesson Expansion (content, Markdown first)

```
You:  "Expand the Zeus-Hera lesson for thema 3. Include: origin myth, divine conflict, Hera's perspective, Seneca's interpretation, vocabulary list."

Me:   [Writes in Markdown, ~4K tokens of outline + content]
      "Here's the draft:
      
      # De Goden: Zeus en Hera
      
      ## Leeruitkomsten
      - Understand the dynastic struggle in Olympus
      - Analyze Hera's role as...
      ...
      
      [Review this first. Content OK? Pedagogy aligned with Surma's 12 building blocks?]"

You:  "Good structure. Add more on the Seneca part, move vocab list to end."

Me:   [Update Markdown, lightweight iteration]
      "Updated draft:
      
      [Expanded Seneca section]
      
      Ready for HTML?"

You:  "Yes."

Me:   [Convert to HTML with styling, embed in site]
      "Done. Added to grieks.html as a new section. Check the preview?"
```

**Tokens used:** ~20K (not 40K if I'd been drafting in HTML from the start).

---

## When to Break the Protocol

- **Emergency fix:** Your site is broken. Just tell me what's wrong, and I'll fix it fast (may reload full context).
- **Exploration:** You're unsure of approach. We discuss + try things (OK to reload and experiment).
- **New feature:** First time building something. OK to be less efficient initially; refine later.
- **You ask explicitly:** "Load the whole file, let's redesign" → I do it.

---

## Quick Reference: Token Savings by Layer

| Layer | Savings | Example |
|-------|---------|---------|
| 1. Reference, don't reload | 5–10% | "Check line 150–200" vs. paste full file |
| 2. Central CSS theme | 15–20% | 1 `_theme.css` vs. inline in 5 pages |
| 3. Template patterns | 10–15% | Reuse card-pattern vs. redesign each time |
| 4. Data schema lock | 5–8% | "Follows schema X" vs. re-explain |
| 5. Image paths | 20–40% | `src="images/x.png"` vs. base64 string |
| 6. GitHub editor | 25–30% | Direct edit vs. copy-paste 2000 lines |
| 7. Batch edits | 20% | 4 edits in 1 prompt vs. 4 chats |
| 8. Markdown → HTML | 15–20% | Draft cheap, render once |
| 9. Task isolation | 30% | New chat per major task |
| 10. Progress checkpoints | 10% | Reuse loaded file, don't reload |

**Cumulative effect:** 70–80% reduction in token overhead for multi-file, iterative projects.

---

## How to Signal Claude to Use This Protocol

**Triggers:**
- "Token-efficient mode"
- "Beperk context"
- "2–3 files, let's batch"
- "New chat for this task"
- "Markdown first, then render"
- "GitHub editor" (direct signal to use it)

**I watch for:**
- Large sessions (>40K tokens) → suggest task isolation
- Repeated file reloads → suggest central CSS or reference pattern
- HTML lesson drafts → suggest Markdown pipeline
- Scattered inline images → suggest path refs

---

## Maintenance & Evolution

This protocol should evolve with your project. **Quarterly reviews:**
- What worked?
- What slowed us down?
- New patterns to lock in?
- Update SKILL.md or create `CONTEXT_PROTOCOL_V2.md` in repo

**Current version:** 2026-04-09 (Jens' initial setup)  
**Last reviewed:** [Today]  
**Next review suggested:** After 5 major site tasks completed
