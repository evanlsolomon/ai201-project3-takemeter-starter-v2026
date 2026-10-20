# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy

Overall accuracy on the held-out test set is **at least 0.60** on all three seeds.

**Why this target:** Random guessing across three labels gets about 0.33, and always guessing the biggest label can get at most 0.50, because criterion 3 caps any label at 50%. 0.60 means getting 18 of the 30 test posts right, clearly above both. I'm not aiming higher because the model only trains on about 140 posts.

---

## 2. Every label is learned

Every label has an F1 of **at least 0.45** on the held-out test set, on all three seeds.

**Why this target:** `analysis` will probably be my smallest label, since comments that cite a checkable fact are much rarer in these threads than opinions and personal stories. With about 40 examples it gets only about 6 posts in the test set, so one wrong post moves its score by roughly 15 points. That's why this floor is lower than the accuracy target. 0.45 still means the model is actually predicting every label, because a label it never predicts scores 0.

---

## 3. Balance

In `labels.csv`, **no label is above 50%** of the rows and **no label is below 20%**.

**Why this target:** Below 20% (40 of 200 posts), a label gets about 6 test posts or fewer, which is too few for a stable score. Above 50%, guessing the biggest label would get close to the 0.60 in criterion 1, and the accuracy target would mean less. Most HN comments in these threads are opinions or personal stories, so I'll have to collect `analysis` posts on purpose to stay above 20%.

---

## 4. Labeling consistency

On the 30-post staff set, `agreement.py` reports **at least 70% agreement** (21 of 30) between my labels and the staff labels.

**Why this target:** Staff say an agreement rate in the 70s is normal for this set. The staff taxonomy uses the same three label names as mine, with similar rules, so if I can't apply those rules at least 70% of the time, my own 200 labels are probably inconsistent too.

---

## 5. Confidence means something

On all three seeds, the model's **most-confident third** of test predictions is **at least 0.20 more accurate** than its least-confident third (both numbers are in `results.json`).

**Why this target:** Each third is 10 posts, so a gap of 0.20 means 2 more correct out of 10. A 1-post gap could be luck. If confident predictions aren't right more often, the confidence score can't be used to flag the posts that need a person to check them.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
