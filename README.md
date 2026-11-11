# Spending the least on H1N1: who gets a vaccine in the post? (Group I, Dataset B)

**Group I · Data Science Challenge hackathon · Wednesday 11 November 2026 · Dataset B**

| | |
|---|---|
| GitHub organisation | `https://github.com/<our-organisation>`  |
| Shared Colab notebook | `https://colab.research.google.com/drive/<id>`  |

## The task

A town must decide, for each of 4,749 residents, whether to post them an H1N1 vaccine (`1`) or not (`0`). The score is **the money the town spends**, not accuracy:

* a posted vaccine costs **$500**, used or not;
* a resident who wants a vaccine and is not sent one is paid **$500** in cash and stays unprotected;
* everyone catches H1N1, and a hospital stay costs **$25,000**.

Prices are from `Dataset B Information.pdf`. The target column is `h1n1_vaccine`.

## What is in this folder

```
.
├── 00_FINAL_GroupI_H1N1_TownBill.ipynb   the whole project, executed (about 5 minutes to re-run)
├── submission/                           the five files to upload to Canvas, best to worst
│   ├── challenge_submission_group_I_order_1.csv
│   ├── challenge_submission_group_I_order_2.csv
│   ├── challenge_submission_group_I_order_3.csv
│   ├── challenge_submission_group_I_order_4.csv
│   └── challenge_submission_group_I_order_5.csv
├── ranking.csv                           model, cross-validated bill and spread for each file
├── dataset_B_training.csv                4,756 residents with the answer
├── dataset_B_testing.csv                 4,749 residents without the answer
├── dataset_B_example_submission.csv      the format our files copy
├── Dataset B Information.pdf             the dataset sheet
├── HOW_TO_PUSH_TO_GITHUB.md              step-by-step guide: organisation, repository, commits, Colab
├── requirements.txt                      libraries
└── README.md                             this file
```

## Result

Bill per resident, measured on 20 cross-validation folds the models had not seen:

| Policy | Bill | File |
|---|---|---|
| Post to everybody | about $658 | |
| Post to nobody | $418.09 | |
| **XGBoost (tuned) + isotonic calibration** | **$393.23** | `order_1` |
| Blend of five models | $393.26 | `order_2` |
| Blend of three models | $393.48 | `order_3` |
| XGBoost (tuned, dollar-weighted) | $393.54 | `order_4` |
| Elastic net (tuned, dollar-weighted) | $393.54 | `order_5` |
| Perfect knowledge (lower limit) | $354.66 | |

* The plan saves about **$25 per resident** against posting to nobody, roughly **$118,000** for the test residents.
* It posts a vaccine to about one resident in six.
* The five files are statistically tied with each other (paired test, notebook Q6.2), so their order on the hidden test set is partly chance. All five beat the simple policies in every fold.

## How we got there

The notebook is written as nine questions.

| | Question | Main point |
|---|---|---|
| Q1 | What have we been given? | every column checked against the dataset sheet |
| Q2 | What do the answers tell us? | nine exploration steps, each ending in a decision |
| Q3 | What exactly is the bill? | post a vaccine when `q > 500 / (500 + (r_u - r_v) x 25,000)`, a different cut-off for every resident |
| Q4 | How should the data be prepared? | six recipes compared; flagging unanswered questions saves about $3.4 per resident |
| Q5 | Which model predicts acceptance best? | seven families, tuning, calibration, dollar-weighted training, blends |
| Q6 | How far can we trust the comparison? | 20 folds, paired tests, error analysis, 30-scenario stress test |
| Q7 | What do we hand in? | five different files, ordered by cross-validated bill, format asserted in code |
| Q8 | Why do the models decide this way? | permutation importance in dollars, SHAP, partial dependence, surrogate tree, for all five models |
| Q9 | What did we learn? | critical review, plain-language summary, requirement check against the brief |

`q` is the model's probability that a resident accepts a vaccine; `r_u` and `r_v` are the resident's hospital risk without and with it. The risks come from three generative-AI models asked the same question (notebook Q3.3), averaged, and stress-tested.

**Biggest caution.** The hospital risks are AI estimates and uncertain by about a factor of two. If the true risks are higher, more vaccines should be posted than our files do.

## Running it

```bash
pip install -r requirements.txt
```

Open `00_FINAL_GroupI_H1N1_TownBill.ipynb` and choose **Run all**. It reads the three csv files from this folder and rewrites `submission/` and `ranking.csv`. Results are reproducible: every random step uses `SEED = 2009`.

In Google Colab, set `GITHUB_RAW` in section Q1.2 to the raw address of the public repository and run `!pip install catboost shap` first. See `HOW_TO_PUSH_TO_GITHUB.md`, Part 4.

## Team

| Member | Student ID | Part of the notebook |
|---|---|---|
| (name) | | Q1 data checks, Q7 files and upload |
| (name) | | Q2 exploration |
| (name) | | Q2 exploration |
| (name) | | Q3 the bill, AI research |
| (name) | | Q4 pre-processing |
| (name) | | Q5 models and tuning |
| (name) | | Q5 models and tuning |
| (name) | | Q6 comparison and stress test |
| (name) | | Q8 explanations, Q9 review, slides |

## Still to do

- [ ] Create the organisation and public repository, push this folder (`HOW_TO_PUSH_TO_GITHUB.md`).
- [ ] Put both links at the top of this file, in the first notebook cell and on slide 1.
- [ ] Fill in names and student IDs here and in the notebook.
- [ ] Every member commits from their own GitHub account.
- [ ] Upload the five files in `submission/` and both links to Canvas before **16:00 today**.
- [ ] Optional: add answers from other AIs (ChatGPT, Gemini) to `AI_ANSWERS` in Q3.3 and re-run.
- [ ] Slides on Canvas before **10:00, Tuesday 17 November**; notebook Q9.2 is the draft of the non-expert slide.
- [ ] Feedback Fruits peer review
