# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

TakeMeter sorts comments from Hacker News, a public discussion site for tech news. The posts come from two threads: "Americans' social activity has steeply declined in the past 30 years" (https://news.ycombinator.com/item?id=50089921) and "How Amazon pushes up prices all across the Internet" (https://news.ycombinator.com/item?id=50084757). Each comment gets exactly one of three labels: **analysis** (argues with a fact a reader could check), **hot_take** (a general claim with no checkable fact behind it), or **reaction** (the writer's own experience or feelings).

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** Argues a point using at least one specific fact a reader could check: a statistic, a price, a public date, a named source, or a link.

**Example 1** (Amazon thread):
> Amazon shipping and logistics are heavily subsidised by the social contract in Western Countries - it's not remotely a fair comparison even if these were removed based on Amazon's effective corporate income tax rates coming in as low as 1.4%.
> In the US alone Amazon has received over $11 billion in cumulative taxpayer subsidies, and governments spend an average of nearly $43,000 per fulfillment center job created.
> This is ramping up exponentially with regard to their data center rollout. In a single instance in rural Oregon, five AWS data centers will avoid $1 billion in property taxes.
> This is completely aside from the qualitative damage done to traditional retail, shopping districts as a whole, SME innovation on consumer products, and distorting market competition beyond all recognition.
> https://goodjobsfirst.org/amazon-tracker/
> https://cictar.org/all-research/amazon

**Example 2** (social activity thread):
> Every single social event is a financial consideration now. Want to go out with the wife for the evening? It's going to cost $150+ for a baby sitter at $25/hour. The dinner + tip is going to cost $200. Parking is going to cost $50 - $100 if you go into a city. You could take public transportation but then you're loooking at that cost shifting back to the babysitter. Before you know it you're in for $300 - $500 just for a night off.

### `hot_take`

**Definition:** Makes a claim about people, companies, or society with no checkable fact behind it, even if it gives reasons.

**Example 1** (social activity thread):
> This was foreseeable.  We all know it.  We all know why.  Yet we fail to do anything about it.

**Example 2** (social activity thread):
> Easy.  Reduce our use of technology and stop consuming so much.  It will require you to lower your standard of living.
> Is your partner willing to do this with you?  Probably not.

### `reaction`

**Definition:** Reports the writer's own experience or feelings instead of making a general claim.

**Example 1** (social activity thread):
> When I lived in the suburbs I knew all my neighbors.  You’d just naturally run into people around the neighborhood.
> Once I moved to the city I didn’t know my neighbors.  People kept to themselves or the turnover was high.

**Example 2** (Amazon thread):
> I'm in the US and I gave up Amazon two years ago, and I haven't noticed any decrease in my quality of life.

### The hardest boundary

**The post:**
> I'm Canadian not American but I feel this keenly
> I had a pretty busy social life in university, 20 years ago
> The first city I landed in afterwards, I was able to make some connections and was lucky enough some of my uni friends were around too
> But I moved for work to a much further away city and my social life has dropped to zero since then. The pandemic and lockdowns didn't help of course, but that was years ago. I don't have a single friend in this city and I've lived here almost 10 years now
> Admittedly I'm kind of socially awkward and quite afraid of new groups, which has only gotten worse over the years. I also haven't had any luck making lasting connections through work or anything like that.
> I tend to blame myself for this but it's not too surprising to hear many others are struggling too

**Which two labels:** `reaction` and `analysis`

**The decision rule I use every time:** A number only counts as a checkable fact if a reader could check it without knowing the writer: a statistic, a price, a public date, a source, or a link. Numbers from the writer's own life ("20 years ago", "I've lived here almost 10 years", "I paid $60 for two") don't count, but what something costs for anyone (a babysitter's hourly rate, a shipping fee) does. So a personal story that happens to contain numbers is `reaction`, and this post is `reaction`.

### Other rules I apply

- **`hot_take` vs `reaction`:** If the post is about what happened to the writer or how they feel, it's `reaction`. If it says something about people, companies, or society in general, it's `hot_take`, even if it's one line or a joke. "together we are alone" is a `hot_take`.
- **`analysis` vs `hot_take`:** If the post uses a checkable fact to support its point, it's `analysis`, even if the tone is heated. A list of reasons with no checkable fact is a `hot_take`.
- **Skipped when collecting:** posts that only ask a question (like a reply asking for a source), link-only posts, deleted comments, and one-line replies that only make sense next to the comment they answer ("Nowhere did I say that."). A rhetorical question that makes a claim ("What competition?") is a `hot_take`.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** Two public Hacker News threads, collected on October 20, 2026: "Americans' social activity has steeply declined in the past 30 years" (103 posts) and "How Amazon pushes up prices all across the Internet" (107 posts). 210 posts total, each a whole comment, word for word. Claude collected them with a short script on the thread pages, and every row was checked character for character against the page.

Skipped while collecting: replies that open by quoting another comment, link-only posts, question-only posts, deleted comments, posts over 900 characters, one-line replies that only make sense next to their parent, and one post that was mostly song lyrics. The first pass was a random sample from each thread. Only about 1 in 6 posts came out as `analysis`, so 18 more posts that cite numbers or sources were added, and random `hot_take` and `reaction` rows were dropped to keep every label between 20% and 50% (criterion 3).

**How I labelled them:** Cold first: the 20 rows marked `cold` in the note column were labelled by me with no tools, before I looked at any pre-labels. The other 190 were pre-labelled by Claude using the definitions and rules above, with a note on every borderline call (40 rows say "hard:"). I then read every pre-label and corrected the ones I disagreed with.
<!-- TODO after your review: say how many pre-labels you changed, and update the note column
     ("pre-labelled, checked" / "pre-labelled, corrected"). Then delete this comment. -->

**Counts per label:**

<!-- TODO after your review: the notebook's section 2 check cell prints these.
     Before review: analysis 53, hot_take 73, reaction 64, plus 20 cold rows still to label. -->

| Label | Count | Share |
|---|---|---|
| `analysis` |  |  |
| `hot_take` |  |  |
| `reaction` |  |  |
| **Total** | 210 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190.
     TODO: these are drafts from the pre-labelling. Keep them only if you agree with the call. -->

**1.**
> *The post:*
>
> A few years ago I looked at the finances of a company that sells on Amazon (FBA). It was pretty astounding the number of fees they charge. They were paying to show up in sponsored results which obviously increased the fees but from what I remember between all the fees roughly 50% of the sale price of their products was just going directly to Amazon.
>
> *Could have been:* `analysis` or `reaction`
>
> *I chose `reaction`, because:* "roughly 50%" sounds like a statistic, but it comes from one private company's books that only the writer saw. A reader can't check it without knowing the writer, so under my boundary rule it doesn't count, and the post is the writer's own experience.

**2.**
> *The post:*
>
> This 40 hr/week need to be combined with
> 1) Increasing job/economic insecurity.
> 2) Prices rising faster then income for majority of population.
> 3) increasing commute time around employment, activity hubs
> 4) kids activities/sports are now taken as seriously as work project deliverables. Previously it could be just gifted kids in school teams now with pay-to-play a lot more kids are involved in "serious sports".
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose `hot_take`, because:* it's a structured, numbered argument and reads like analysis, but none of the four points is a fact a reader could check. My rule says a list of reasons with no checkable fact is a `hot_take`.

**3.**
> *The post:*
>
> The 55+ crowd where I'm from have the most active social life. Constantly at each others houses for a chat, activities (gossip parties) after Sunday mass etc.
>
> *Could have been:* `reaction` or `hot_take`
>
> *I chose `hot_take`, because:* it comes from the writer's own town, so it reads like personal experience, but it's about other people (the 55+ crowd), not about what happened to the writer or how they feel. My `hot_take` vs `reaction` rule puts claims about other people in `hot_take`.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
