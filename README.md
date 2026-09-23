# Awesome Jev Robustness [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> How [Jev](https://typesafe.ai)'s answers move, and whether its probabilities can be trusted. Independent tests of TypeSafe's System One decision model, grouped by what they measured.

109 entries · almost all against `jev-1.13.0` · snapshot 2026-09-23 · numbers are the authors' own

Most of these were run in the two weeks after Jev's release on 15 September 2026, usually by one person with a small budget. Read them as evidence to inspect, not settled results. Task benchmarks that only report an accuracy are kept out of this page; they are in [`data/task_benchmarks.tsv`](data/task_benchmarks.tsv). The machine-readable version of this page is [`data/entries.tsv`](data/entries.tsv).

<details>
<summary>What counts as robustness here</summary>

An entry has to show how Jev's answer or probability changes with the wording of the question or options, the order or number of options, repeated calls, the language of the input, text planted to steer it, or inputs that fit no option; or it has to test whether the returned probability is calibrated. A project that measured several things is listed once, under the property it says most about. Tools, SDKs, applications and general directories are out of scope; general lists are linked at the end.

</details>

## Start here

- [jujumilk3/jev-calibration-audit](https://github.com/jujumilk3/jev-calibration-audit) - The largest single audit: 400-item Noul vs Choice gap, negation complements, option order, batching with hostile questions, Korean vs English, and the abstain-option collapse. Every ECE printed next to its noise floor.
- [willkelly/jev-evaluation](https://github.com/willkelly/jev-evaluation) - 123,805 pre-registered requests. Calibration holds on support routing and collapses on random 3-SAT, where the probability barely moves while the true rate spans 0 to 1.
- [primeline.cc pre-registered test](https://primeline.cc/blog/typesafe-jev-pre-registered-test) - 12 probes of TypeSafe's own failure-mode list with pass bars written first: 4 real, 8 refuted. Also shows Choice confidence is a fixed formula of the top probability.
- [zkousama/jagged](https://github.com/zkousama/jagged) - 486 Wikipedia deletion discussions: 96.5% accurate at baseline, 26.5% under a one-line injected instruction.
- [yodablocks/jev-orderby-bench](https://github.com/yodablocks/jev-orderby-bench) - Can you ORDER BY a Jev probability? Six pre-registered gates; passes on topic membership, fails four on graded product relevance, and 40 rows per request breaks a gate that one row per request passes.
- [KantaHayashiAI/jev-does-not-play-dice](https://github.com/KantaHayashiAI/jev-does-not-play-dice) - A hidden fair die: Choice puts 82.9% on its pick at 19% accuracy. Noul on the same die stays near 1/6.
- [RINNECODER/jev-behavior-study](https://github.com/RINNECODER/jev-behavior-study) - 11,621 requests. Arithmetic accuracy 88% when the correct option is listed first, 57% when last.
- [SamuelSacco/jev-exploration](https://github.com/SamuelSacco/jev-exploration) - Recomputes every published Jev ECE against its sampling noise floor, so the audits above can be compared on one scale.

## What the evidence says so far

One line per property, as the linked authors report it. Where two studies disagree, both are linked.

| Property | Finding | Sources |
|---|---|---|
| Option order | No measurable effect. Reversing two options moved probability by 0.005 and flipped 0 of 400 argmaxes; 4 permutations of 139 items flipped 5%, within repeat noise. | [calibration-audit](https://github.com/jujumilk3/jev-calibration-audit), [JevBench issue 40](https://github.com/fstandhartinger/jevbench/issues/40) |
| Repeated identical calls | Near-deterministic but not exact: std 0.001 to 0.015, 15 distinct answer sets in 50 identical requests. Larger option sets move more. | [calibration-audit](https://github.com/jujumilk3/jev-calibration-audit), [jev-labs](https://github.com/copyleftdev/jev-labs), [jev-vs-ml](https://github.com/vianaR25/jev-vs-ml) |
| Noul vs two-option Choice | Same judgment, different number: mean absolute gap 0.125, over 0.2 on one item in six. | [calibration-audit](https://github.com/jujumilk3/jev-calibration-audit), [jev-synthetic-survey](https://github.com/jjd-lab/jev-synthetic-survey) |
| A statement and its negation | P(x) + P(not x) averages 1.02 but ranges 0.71 to 1.42; a plain paraphrase moves the answer as much as negation does. | [calibration-audit](https://github.com/jujumilk3/jev-calibration-audit), [jev-orderby-bench](https://github.com/yodablocks/jev-orderby-bench), [jev-first-look](https://github.com/colinmcnamara/jev-first-look) |
| Wording of criteria | The largest lever found. Criteria rewrites moved paired accuracy 70% to 96%, 83% to 100%, and flipped a life-sim's behaviour outright. | [jev-classification-prompting](https://github.com/RastislavDujava/jev-classification-prompting), [jev-experimental](https://github.com/SYED-M-HUSSAIN/jev-experimental), [jev-village](https://github.com/tfolkman/jev-village) |
| Batching many questions | Questions do not interfere, even with hostile neighbours (shift 0.008). Many *rows* in one state do: 40 rows per request broke a ranking gate. | [calibration-audit](https://github.com/jujumilk3/jev-calibration-audit), [jev-orderby-bench](https://github.com/yodablocks/jev-orderby-bench) |
| No option is right | Without an explicit unknown or none option Jev answers anyway: 79% stereotype picks at 0.79 confidence on KoBBQ, 0 of 30 out-of-scope inputs flagged elsewhere. | [calibration-audit](https://github.com/jujumilk3/jev-calibration-audit), [priorbench/jev](https://github.com/priorbench/jev) |
| Calibration by primitive | Noul tends under-confident, Choice and Score over-confident on the same inputs; Choice confidence is (N·p_max − 1)/(N − 1), not a separate signal. | [jev-ood-calibration](https://github.com/scienthoon/jev-ood-calibration), [primeline.cc](https://primeline.cc/blog/typesafe-jev-pre-registered-test), [Anthus](https://anth.us/blog/can-you-trust-jev-confidence) |
| Injected text | Works when it reads as evidence about the judged item (a claimed approval, an editor's note); blunt system-style commands mostly fail. Effect size depends on task. | [jagged](https://github.com/zkousama/jagged), [jev-engineering](https://github.com/eugeniughelbur/jev-engineering), [system1-system2](https://github.com/Iskandeur/system1-system2) |
| Input language | Accuracy drops (Russian −11 pp, Spanish −3 to −6 pp, Korean −6.5 pp) and calibration usually worsens with it; the language of the instruction does not matter, only the state's. | [jev-cyrillic-audit](https://github.com/AHTOOOXA/jev-cyrillic-audit), [jev-acento](https://github.com/marcosmartinez/jev-acento), [calibration-audit](https://github.com/jujumilk3/jev-calibration-audit) |

## Contents

- [Official material](#official-material)
- [Calibration and confidence](#calibration-and-confidence)
- [Consistency and invariance](#consistency-and-invariance)
- [Input perturbation and context](#input-perturbation-and-context)
- [Prompt injection and adversarial inputs](#prompt-injection-and-adversarial-inputs)
- [Abstention and unanswerable inputs](#abstention-and-unanswerable-inputs)
- [Failure modes and capability limits](#failure-modes-and-capability-limits)
- [Languages other than English](#languages-other-than-english)
- [Evaluation tooling](#evaluation-tooling)
- [Write-ups, critiques and evidence ledgers](#write-ups-critiques-and-evidence-ledgers)
- [Related lists](#related-lists)
- [Contributing](#contributing)
- [Citation](#citation)

## Official material

- [Choice self-consistency cookbook (TypeSafe docs)](https://docs.typesafe.ai/cookbooks/consistency_choice_cookbook) - 8 Choice questions on one ambiguous post, seven conditions: 99.2% agreement at a 0.60 threshold, flips on 2 of 8.
- [Jev 1.13 jaggedness (TypeSafe docs)](https://docs.typesafe.ai/model-jaggedness/jev-1.13) - TypeSafe's own nine failure modes: literal reading, counting, numbers and dates, indirection, distracting state, adversarial content, contradictory criteria, invariants, generation. Qualitative.
- [Noul self-consistency cookbook (TypeSafe docs)](https://docs.typesafe.ai/cookbooks/consistency_noul_cookbook) - 14 Noul questions repeated 15 times on one claim: standard deviation 0.0102; one borderline answer spans 0.43 to 0.53 across the 0.5 threshold.
- [TypeSafe evals dashboard](https://evals.typesafe.ai) - Four workflows against frontier models, 61.7% to 76.0% agreement; the reference label is the average of two frontier models.

## Calibration and confidence

Whether the probabilities mean what they say.

### Probability audits against labels

- [Adilmp/does-jev-confidence-mean-anything](https://github.com/Adilmp/does-jev-confidence-mean-anything) - Stated confidence against human labels on civil_comments: at about 75% stated confidence only 10% of comments were human-flagged; a two-parameter recalibration removes 96% of the error. `jev-1.13.0 · n=8,000 judgments`
- [ajanm007/jevrag](https://github.com/ajanm007/jevrag) - Five RAG mid-pipeline decisions: confidence ranks well but is miscalibrated on retrieval-stopping (ECE 0.33) and well calibrated on chunk boundaries (ECE 0.087), same harness. `n=700 questions`
- [AnthusAI/Jev-Calibration](https://github.com/AnthusAI/Jev-Calibration) - 8,801 labeled sentiment examples: raw Noul probabilities overconfident (ECE 0.117, Choice worse); isotonic regression brings ECE to 0.008. `jev-1.13.0 · n=8,801 examples`
- [Calibration, decomposition and shadow evals (beri.net)](https://beri.net/article/typesafe-jev-typed-decision-model-calibration-decompo) - Ties together the phishing decomposition study (62.6% as one question, 95.0% as five) and the 900-ticket OOD test (ECE 0.107, 4.4x the noise floor).
- [Can you trust Jev's confidence? (Anthus)](https://anth.us/blog/can-you-trust-jev-confidence) - By question type on 8,801 examples: Noul stated 79.0% vs 72.3% actual, Choice 91.4% vs 76.1%; the 50 to 95% band was only 50 to 57% correct.
- [cx295410-dot/jev-biomedical-evidence-screening](https://github.com/cx295410-dot/jev-biomedical-evidence-screening) - Frozen predictions on SYNERGY systematic-review data scored for discrimination, calibration and screening workload at high recall. `jev-1.13.0 · n=17,191 pairs`
- [jourdanlabs/assay-001](https://github.com/jourdanlabs/assay-001) - Calibrated on CLINC150 (ECE 0.020) but overconfident on Banking77 (ECE 0.094); zero type errors in 8,576 responses. `jev-1.13.0 · n=8,576 responses`
- [Legal documents yes/no test (X article)](https://x.com/i/article/2100463318209048850) - 544 legal documents, 109 yes/no judgments: Brier 0.030; all 96 answers outside the 0.2 to 0.8 band were correct.
- [pozapas/jev-calibrated-narrative-coding](https://github.com/pozapas/jev-calibrated-narrative-coding) - Paper artifact converting police crash narratives to coded variables; audits ECE, calibration slope and selective prediction against coded fields and a human gold set. `jev-1.13.0 · pre-registered design`
- [rubinagentagi-tech/jev-heart-risk-bench](https://github.com/rubinagentagi-tech/jev-heart-risk-bench) - 5,000 CDC heart-risk respondents: AUC 0.773 vs 0.794 for a chat LLM, and stated probabilities badly miscalibrated (Brier skill -1.3). `jev-1.13.0 · n=5,000 respondents`
- [Running-Dolphins/jev-bench](https://github.com/Running-Dolphins/jev-bench) - 12 public classification tasks, 500 items each; reliability tables show over- and under-confidence varying sharply by task (Banking77 top band: stated 0.98, actual 0.90). `jev-1.13.0 · n=6,000 items`
- [WanLanglin/jev-skills](https://github.com/WanLanglin/jev-skills) - Own calibration on 4,995 real coding-agent decisions: Noul ECE 0.169, Choice ECE 0.226. `n=4,995 decisions`
- [When a Judgment Layer's Self-Reported Fields Lie (Zenodo)](https://doi.org/10.5281/zenodo.22901853) - Jev as one of three judgment layers; tests whether the self-reported confidence fields track correctness.
- [wotai-dev/typesafe-jev-tools](https://github.com/wotai-dev/typesafe-jev-tools) - 16 models on 150 passages for calibrated binary judgment: Jev fastest and best calibrated of the sub-second models, but flags uncertainty 5x more often than Haiku. `jev-1.13.0 · n=2,400 calls`

### Confidence as a routing or gating signal

- [dtduc-git/jevnav](https://github.com/dtduc-git/jevnav) - 41 of 41 element-selection decisions correct, but in loop mode confidence does not separate right from wrong (both land 0.39 to 0.99). `jev-1.13.0`
- [ItBayMax/typesafe-ai-jev-example](https://github.com/ItBayMax/typesafe-ai-jev-example) - 28 live calls vs hand-written mock probabilities: the real distribution is extreme (mass at 0 and 1) and prefers a clean fallback option to picking among wrong ones. `jev-1.13.0 · n=28 calls`
- [nikkoxgonzales/jev-certify](https://github.com/nikkoxgonzales/jev-certify) - Conformal thresholds on CLINC150: the achievable risk bound floors at 1.95% because Jev returns exactly 1.0 confidence on 56.4% of answers, nine of them wrong. `jev-1.13.0 · n=2,412 answers`
- [scarif-labs/jev-software-decision-benchmark](https://github.com/scarif-labs/jev-software-decision-benchmark) - Dependency auto-merge decisions: AUROC 0.851 in distribution, but the tuned threshold fails to transfer out of distribution (50% precision, 15 unsafe merges on 185 cases). `jev-latest · n=185 OOD cases`
- [Support-ticket benchmark (thoughts.jock.pl)](https://thoughts.jock.pl/p/jev-typesafe-system-one-model-benchmark-2026) - 40 support tickets: Choice confidence is bimodal while Score confidence clusters mid-range, so one threshold cannot serve both.
- [yuvrajrox/laya-jev-eval](https://github.com/yuvrajrox/laya-jev-eval) - Same prompt to Laya and Jev on 100 emails, both 98%; fine-tuned Laya puts 1.00 confidence on its errors, Jev's errors sit at lower confidence. `jev-1.13.0 · n=100 emails`

### Probes with a known true probability

- [eggmasonvalue/jev-takes-mauboussin](https://github.com/eggmasonvalue/jev-takes-mauboussin) - Mauboussin's 50-question calibration quiz: 90.0% accuracy at 91.4% mean stated confidence; 34 of 34 correct when Jev claimed 95% or more. `jev-latest · n=50 questions`
- [KantaHayashiAI/jev-does-not-play-dice](https://github.com/KantaHayashiAI/jev-does-not-play-dice) - Hidden fair die: Choice puts 82.9% on its pick at 19.0% accuracy; Noul on the same die stays near 1/6. `jev-1.13.0 · n=400 rolls`
- [meetr1912/jev-arena](https://github.com/meetr1912/jev-arena) - Questions with analytically known probabilities: Brier 0.0059, ECE 0.062 on 145 binary events. `jev-1.13.0 · n=145 events`
- [meetr1912/jev-vickrey](https://github.com/meetr1912/jev-vickrey) - Sealed-bid auction probes: under-confident on value thresholds (ECE 0.132 vs 0.013 for an oracle), so it overbids and loses money. `jev-1.13.0`
- [simonmesmith/jev-probability-experiment](https://github.com/simonmesmith/jev-probability-experiment) - 68 coin, dice and card problems: Noul is closest to the true probability (MAE 5.6 points); asking for the outcome as a Choice is far worse (MAE 21.1). `jev-1.13.0 · n=68 problems`
- [TakumiNoguchi2004/jev-noul-vs-choice](https://github.com/TakumiNoguchi2004/jev-noul-vs-choice) - Root-causes the fair-die collapse: it is specific to Choice; independent Nouls recover 1/6 for every face, and the Choice bias grows with option count. `jev-1.13.0 · n=50 trials`

## Consistency and invariance

Whether the same judgment comes back the same way.

### Repeated calls

- [agrogov/jev-system-one-study](https://github.com/agrogov/jev-system-one-study) - Black-box study of about 8,300 requests, replayed identically against Laya and SemIf to separate Jev's variance from task noise. `jev-1.13.0 · n=8,300 requests`
- [copyleftdev/jev-labs](https://github.com/copyleftdev/jev-labs) - Identical requests returned probabilities 0.03 to 0.04 apart across five calls; separate noise floors measured for identity, reorder and paraphrase. `jev-1.13.0`
- [danielgshea/jev-as-a-judge](https://github.com/danielgshea/jev-as-a-judge) - Jev, GPT-5.6 Luna and Terra, Claude Sonnet 4.6 as agent-eval judges: accuracy, repeat-score variance, cost and latency side by side. `n=500 decisions`
- [orq-ai/jev-judge](https://github.com/orq-ai/jev-judge) - 12 frozen agent runs scored 100 times each: Jev repeats every verdict; three LLM judges vary run to run even with sampling off. `jev-latest · n=1,200 calls`
- [Repeatability test (DataCamp)](https://www.datacamp.com/blog/system-one-models-jev) - 500 of 500 agreements with a human oracle across repeats; per-case variance 92x to 913x lower than GPT-5.6 and Claude Sonnet.
- [vianaR25/jev-vs-ml](https://github.com/vianaR25/jev-vs-ml) - Three Kaggle datasets against trained ML; repeat queries shift Jev's probability by up to 0.13. `jev-1.13.0`
- [zhengbangbo/structured-decision-bench](https://github.com/zhengbangbo/structured-decision-bench) - 24 typed decisions repeated 10 times: Jev and Qwen3 8B both 100% label-consistent; Jev 808 to 848 ms vs 6 to 100 ms locally. `jev-1.13.0 · n=240 calls`

### Options: order, set and count

- [123Satyajeet123/jev-wide](https://github.com/123Satyajeet123/jev-wide) - Ranking beyond per-call limits: adding unrelated candidates shifts log-odds between untouched options by 0.31 to 0.50; 95.8% of a 200-candidate field returns 0.00; 7.3% of a top-10 changes on repeat. `jev-1.13.0`
- [alperiox/audio-jevlike](https://github.com/alperiox/audio-jevlike) - Adding one unrelated option shifts the log-odds between two untouched options by about -0.28 across ten randomized blocks.
- [erendikmenn/jev-rag-benchmark](https://github.com/erendikmenn/jev-rag-benchmark) - 1,044 Turkish questions: same gold-passage recall as Cohere Rerank 3.5, but permuting candidate order gives mean Spearman 0.262 with the original ranking. `jev-1.13.0 · n=1,044 questions`
- [finnhll/jev-eval](https://github.com/finnhll/jev-eval) - 282 trials: batching independence and repeat stability (sd 0.0075 or less) confirmed, but reshuffling options moved a winning probability from 0.62 to 0.48. `jev-1.13.0 · n=282 trials`
- [heddendorp/jev-sort](https://github.com/heddendorp/jev-sort) - Pairwise sorting on 100 tickets: 92.9% to 98.4% agreement with the stated policy, with non-transitive triples. `jev-latest · n=100 tickets`
- [yodablocks/jev-orderby-bench](https://github.com/yodablocks/jev-orderby-bench) - Six pre-registered gates: pass on topic membership, four fail on graded product relevance; 40 rows per request breaks a gate one row per request passes; 53 of 360 rows tie at 0.99. `jev-1.13.0 · n=666 rows · pre-registered`
- [yottayoshida/jevfuzz](https://github.com/yottayoshida/jevfuzz) - Renames question IDs and reorders options and JSON keys: six confirmed decision changes in 100 mutations over 10 states. `jev-latest · n=100 mutations`

### Question form and request shape

- [DowLucas/browser-jev](https://github.com/DowLucas/browser-jev) - Narrow oracle questions score 0.99 where broad ones score 0.30 on the same page; the same state scores 0.72 to 0.87 across repeats.
- [jjd-lab/jev-synthetic-survey](https://github.com/jjd-lab/jev-synthetic-survey) - Synthetic survey respondents: asked as Noul Jev beats GPT-4.1 on all six measures; asked as Choice it loses the headline metric. `jev-1.13.0 · n=300 respondents`
- [JLegends/opencode-jev-compaction](https://github.com/JLegends/opencode-jev-compaction) - Noul at 0.996 on hard facts but 0.003 to 0.28 on judgment calls; a statement and its negation both scored about 0.95 until switched to explicit-criteria Choice.
- [nyarlathoteppppp/pi-heed](https://github.com/nyarlathoteppppp/pi-heed) - Slightly under-confident probabilities, flat latency in question count, and strong anchoring on the order of state fields. `jev-latest`
- [yodablocks/duckdb-jev](https://github.com/yodablocks/duckdb-jev) - Six-condition gate (Brier, ECE, negation symmetry, rubric ordinality) on 360 live requests: five of six pass. `jev-1.13.0 · n=360 rows`

## Input perturbation and context

How much the answer moves when the input or the criteria are reworded, enriched or degraded.

- [anisselbd/jev-phishing-bench](https://github.com/anisselbd/jev-phishing-bench) - 2,000 phishing emails: one question 62.6% vs Haiku 81.3%; the same judgment split into five signal questions and combined in code reaches 95.0%. `jev-1.13.0 · n=2,000 emails`
- [ElshinQ/jevaluate](https://github.com/ElshinQ/jevaluate) - Splitting one decision into three questions destroyed calibration (0.55 and 0.42 vs 1.00 merged); 60 multilingual phrases, zero confidently wrong. `jev-1.13.0`
- [Fox-Islam/jev-bias-bench](https://github.com/Fox-Islam/jev-bias-bench) - Counterfactual fairness: 29 demographic attributes swapped one at a time across 10 high-stakes scenarios, 11,984 calls, measured against Jev's own repeat noise. `jev-latest · n=11,984 calls`
- [gordan-code/jev-entropy-gate](https://github.com/gordan-code/jev-entropy-gate) - Rephrasing the task alone moved the same code site from auto (0.81) to manual (0.50) with no code change.
- [KiishiAD/jev-loan-identity-benchmark](https://github.com/KiishiAD/jev-loan-identity-benchmark) - Typos, OCR corruption and confusable names in loan matching: 100% precision, 97.5% recall held out. `jev-1.13.0`
- [kobashi/jev-playground](https://github.com/kobashi/jev-playground) - Encoding of the same musical phrase moves cadence detection from 63% to 97%; near chance (64%) on a subjective question. `jev-1.13.0 · n=126 requests`
- [Korean sentences, one call vs whole document (Threads)](https://www.threads.com/@ebrain.lab/post/DddGgXuoLlL) - 40 Korean sentences: 40 of 40 sent one per call, 62% when the whole document went in one call.
- [leepokai/llm-prompt-techniques-on-jev](https://github.com/leepokai/llm-prompt-techniques-on-jev) - CoT, self-consistency, few-shot and GEPA ported to Jev over LegalBench, BBH, MMLU-Pro and CLERC: the reasoning-style techniques do nothing, there is no scratchpad. `jev-1.13.0 · n=23 tasks`
- [Norwegian hearing documents (lindfors.no)](https://lindfors.no/blog/a-first-look-at-typesafes-jev) - 24 Norwegian hearing documents: a stricter question wording worsened ECE from 0.040 to 0.116 while agreement at high confidence stayed 97%.
- [phuthuycoding/jev-audit](https://github.com/phuthuycoding/jev-audit) - 79-case secrets and vulnerability corpus: F1 1.00 and 0.98, and detection holds when the secret is buried in 49 KB of noise. `jev-1.13.0 · n=79 cases`
- [RastislavDujava/jev-classification-prompting](https://github.com/RastislavDujava/jev-classification-prompting) - Nine ablations: criteria wording moves paired accuracy from 70% to 96%; a knowledge-cutoff blind spot goes from 66.7% to 100% by rewriting criteria. `jev-1.13.0 · n=41 items`
- [Selmar/typesafe-jev-calibrate-for-code-review](https://github.com/Selmar/typesafe-jev-calibrate-for-code-review) - Rule-by-rule probes against a 0.60 threshold for C# review: comment rules improve with whole-file context, and Jev reads facts better than it derives them.
- [SYED-M-HUSSAIN/jev-experimental](https://github.com/SYED-M-HUSSAIN/jev-experimental) - Accuracy 83% to 100% once criteria, not the question, spelled out the boundary; Brier 0.038. `jev-1.13.0`
- [tfolkman/jev-village](https://github.com/tfolkman/jev-village) - 60 simulated villagers: wording of the criteria alone flipped correct behaviour to incorrect across the whole population. `jev-1.13.0`

## Prompt injection and adversarial inputs

Text planted in the state to move Jev's own verdict. Jev used as an injection *detector* is in `data/task_benchmarks.tsv`.

- [0xshin0221/openpoke-meets-jev](https://github.com/0xshin0221/openpoke-meets-jev) - Targeted injection and contamination test on an email triage gate: the injection check could be suppressed 95.3% of the time. `jev-1.13.0 · n=12 emails`
- [azterizm/jev-vs-sovereign-benchmark](https://github.com/azterizm/jev-vs-sovereign-benchmark) - Fake-law probe on UK legal RAG: 0% abstention where a specialised stack abstained 100%. `jev-1.13.0`
- [cwhy/decision-injection-bench](https://github.com/cwhy/decision-injection-bench) - 1,056 injection attacks on four classifiers: Jev flipped on 1 (0.09%), the others on 3.0% to 62.6%. `jev-1.13.0 · n=1,056 attacks`
- [eugeniughelbur/jev-engineering](https://github.com/eugeniughelbur/jev-engineering) - Blunt injection: 0 of 30 dangerous commands pass. Authority injection, a claimed human approval: up to 3 of 30 pass. `jev-latest · n=300 calls`
- [finrod21/jev-transaction-guard](https://github.com/finrod21/jev-transaction-guard) - Injection bait inside a transaction memo trips the circuit breaker at 0.99; verdicts and token counts identical across five repeats. `jev-latest`
- [Foshowithit/jev-rcos-study](https://github.com/Foshowithit/jev-rcos-study) - Falsification-first: confidence gating AUC 0.44 (inverted, falsified); 13 of 13 adversarial near-duplicate candidates resisted. `jev-1.13.0`
- [Iskandeur/system1-system2](https://github.com/Iskandeur/system1-system2) - 600 MASSIVE utterances: one injected payload flips GPT-5.2 on all 80 trials and Jev on 26 of 80; Jev ECE 4.5%. `jev-1.13.0 · n=600 utterances`
- [Prompt injection can influence the verdict (VentureBeat)](https://venturebeat.com/security/companies-are-putting-jev-in-charge-of-ai-age) - An agent action gate: block probability for `rm -rf ~/.ssh` fell from 0.76 to 0.48 after a fake pre-approval was injected into tool output.
- [themsquared/jev-benchmark](https://github.com/themsquared/jev-benchmark) - 60 tool-call risk cases with adversarially worded destructive commands: 91.7% accuracy, every wrong answer at hedged confidence. `jev-1.13.0 · n=60 cases`
- [willkelly/jev-evaluation](https://github.com/willkelly/jev-evaluation) - 123,805 requests: calibration holds on support routing (ECE 0.075) and collapses on random 3-SAT, where the probability barely moves as the true rate spans 0 to 1. `jev-1.13.0 · n=123,805 requests · pre-registered`
- [zkousama/jagged](https://github.com/zkousama/jagged) - 486 Wikipedia deletion discussions: 96.5% accurate at baseline, 26.5% under a one-line injected instruction; mirrored questions disagree. `jev-1.13.0 · n=486 · pre-registered`

## Abstention and unanswerable inputs

What Jev does when no option is right or the answer is not in the state.

- [baibizhe/jev-decision-benchmarks](https://github.com/baibizhe/jev-decision-benchmarks) - Tool selection and abstention (MetaTool, When2Call, BFCL): best abstention on MetaTool, but hallucinates a tool call on 76% of When2Call's no-tool cases. `jev-1.13.0 · n=10,938 requests`
- [jujumilk3/jev-calibration-audit](https://github.com/jujumilk3/jev-calibration-audit) - Removing the abstain option takes KoBBQ accuracy from 0.950 to 0.000 and ECE from 0.023 to 0.793; option order, batching and instruction language show negligible effect. `jev-1.13.0 · n=11,759 items`
- [scienthoon/jev-ood-calibration](https://github.com/scienthoon/jev-ood-calibration) - Near-calibrated on three public sets; on an unseen rule task ECE is 4.4x the noise floor and the unknowable label gets 0.74 average probability. `jev-1.13.0 · n=900 tickets`
- [simonmesmith/jev-bbq-experiment](https://github.com/simonmesmith/jev-bbq-experiment) - All 58,492 BBQ questions: 97.28% (99.96% on ambiguous items); position reversal changed 1 of 484 paired answers. `jev-1.13.0 · n=58,492 questions`
- [sshariqali/jev-abstentionbench](https://github.com/sshariqali/jev-abstentionbench) - Meta's AbstentionBench: mean abstention F1 0.855, first among 20 published systems, via higher recall at similar precision. `n=5,933 questions`

## Failure modes and capability limits

Probes of the limits TypeSafe documents and of others found since.

### Reproducing the documented jaggedness

- [colinmcnamara/jev-first-look](https://github.com/colinmcnamara/jev-first-look) - Reproduces the vendor's jaggedness examples; P(x) + P(not x) spans 0.93 to 1.19 over 20 negation pairs; ECE about 0.09 on SST-2 and AG News. `jev-1.13.0 · n=500`
- [dopeCape/typesafe-ai-test](https://github.com/dopeCape/typesafe-ai-test) - Six tracks, about 8,400 calls: API limits confirmed, overconfident in low bands but calibrated above 0.9, largely immune to injection, unable to count, sort or add. `jev-latest · n=8,400 calls`
- [fly2abhishek/jev-field-tests](https://github.com/fly2abhishek/jev-field-tests) - Twelve field tests: 89% and ECE 0.03 on 400 BoolQ items; four-way Choice overconfident (0.998 stated, 0.90 actual); counting and date weaknesses. `jev-1.13.0`
- [Maxi91f/jev_testing](https://github.com/Maxi91f/jev_testing) - Runnable counterexamples collected 17 to 18 September 2026, selected because they failed; a methodology report, not a representative benchmark. `jev-1.13.0`
- [phuryn/experiments](https://github.com/phuryn/experiments) - TypeSafe's invoice showcase hardened to 50 documents: ties Haiku 4.5 at 50 of 50, but a hidden house rule Jev was never told makes it wrong 19 of 24 times at high confidence. `n=50 documents`
- [Pre-registered test (primeline.cc)](https://primeline.cc/blog/typesafe-jev-pre-registered-test) - About 9,750 calls with pass bars fixed in advance: 12 probes of the documented failure modes, 4 real and 8 refuted; 2 of 7 recommended wording fixes null; 40-pair injection test 22.5% misclassified.
- [priorbench/jev](https://github.com/priorbench/jev) - 5,721 calls, 21 experiments: 95.9% zero-shot accuracy, but 0 of 30 out-of-scope inputs flagged without an explicit none option; reliable only above 0.99. `jev-1.13.0 · n=5,721 calls · pre-registered`
- [RINNECODER/jev-behavior-study](https://github.com/RINNECODER/jev-behavior-study) - 11,621 requests: arithmetic accuracy 88.0% with the correct option listed first, 57.4% listed last; one wording change moved travel-choice accuracy from 0 to 20 of 20. `jev-1.13.0 · n=11,621 requests`
- [scd13150/jev-field-notes](https://github.com/scd13150/jev-field-notes) - Fighting game, TTS and SVG geometry probes plus an 8,000-call boundary study. `jev-1.13.0 · n=8,000 calls`
- [yakubmurcek/should-i-jev](https://github.com/yakubmurcek/should-i-jev) - 6 of 27 recorded answers matched expectations at first; literal reading and compound questions fixed, 27 of 28 after three iterations. `jev-1.13.0`

### Counting, sequence and state

- [etsabary/jev-deterministic-benchmark](https://github.com/etsabary/jev-deterministic-benchmark) - 1,000 decisions across 25 reasoning families: 97% to 100% on static logic, 13.2% on sequential state mutation, 33.3% on exact counting; confidence 0.95 or more correct 492 of 493 times. `jev-1.13.0 · n=1,000 decisions`
- [pycodinglec/jev-csat-math-probe](https://github.com/pycodinglec/jev-csat-math-probe) - Three Korean CSAT math problems marking where Jev stops working as a reasoner. `n=3`
- [simonmesmith/jev-arc-agi-v1-experiment](https://github.com/simonmesmith/jev-arc-agi-v1-experiment) - 4 of 400 ARC-AGI-1 tasks solved via per-cell Choice decisions, $2.32 in total. `jev-1.13.0 · n=400 tasks`
- [TomRichner/can-jev-bayes](https://github.com/TomRichner/can-jev-bayes) - Multi-armed bandits: follows supplied optimal action values 99.93% of the time but under-explores without them; richer uncertainty summaries did not help uniformly. `jev-1.13.0`
- [UgurcanAkkok/yks-bench](https://github.com/UgurcanAkkok/yks-bench) - 593 Turkish exam questions: 82.3% vs a 24.6% majority baseline; deleting the question text costs 32.5 points; math collapses when a figure is required. `jev-1.13.0 · n=593 questions`

### Tasks whose signal is not in the text

- [gdchaochao/lunar-terminal](https://github.com/gdchaochao/lunar-terminal) - 327 Robocode decisions: action choice near random (r = -0.10) while yes/no judgments on atomic questions score 0.94 to 0.96. `n=327 decisions`
- [Jev is the fish at the poker table](https://backnotprop.com/blog/jev-poker) - 30 solver-checked poker spots: 63% match, 15 to 30 point swings from relabelling the same hand, bets into a made flush 16 of 16 times.
- [PerryLink/llm-jev-laya-bench](https://github.com/PerryLink/llm-jev-laya-bench) - Jev and Laya as judgment layers on a 77-class battery: 0.225 to 0.90; both fail tasks that need detecting an absence. `n=1,100`
- [Tsagaanbayr1/jev-tetris](https://github.com/Tsagaanbayr1/jev-tetris) - Cannot judge a Tetris board from raw text (0.71 confidence on the worst option); ranks well once given computed outcome features.
- [wondertwins/jev-benchmark](https://github.com/wondertwins/jev-benchmark) - Chess from a raw FEN is worse than random, about 950 Elo once given hand-computed tactical facts; NPC addressee detection F1 0.96 clean, 0.93 on noisy speech-to-text. `jev-latest`

## Languages other than English

Matched English and non-English items, so the effect of input language is isolated.

- [AHTOOOXA/jev-cyrillic-audit](https://github.com/AHTOOOXA/jev-cyrillic-audit) - 600 paired XNLI items: Russian 77.3% vs English 88.3%, ECE 0.096 vs 0.032; traced to cross-lingual entailment, not tokenization or length. `jev-1.13.0 · n=600 pairs · pre-registered`
- [ArmanJR/Jev-Persian-Benchmark](https://github.com/ArmanJR/Jev-Persian-Benchmark) - 480 authored Persian and Finglish questions in 10 categories: 99.6% Choice, 99.4% Noul, 95.0% Score, parity with English. `jev-1.13.0 · n=480 questions`
- [mahlernim/jev-korean-benchmark](https://github.com/mahlernim/jev-korean-benchmark) - Korean vs English on four public exams, 100 per cell: no cost on reading comprehension, 8 points behind GPT-5.6 Luna on the Korean medical exam. `n=800 questions`
- [marcosmartinez/jev-acento](https://github.com/marcosmartinez/jev-acento) - Same items in English and Spanish: 3.0 to 6.4 points lost and calibration error roughly doubled on the two hardest of four datasets; Spanish instructions do not help. `jev-1.13.0 · n=3,200 items`

## Evaluation tooling

Harnesses built to probe wording, ordering or calibration, listed when they ship a measured run.

- [FlorianRiquelme/jev-kit](https://github.com/FlorianRiquelme/jev-kit) - Harness whose example run on 15 project fixtures gets 81.7% overall and flags one indirect compound question at 46.7%, below a coin flip.
- [rssr25/system-one-bench](https://github.com/rssr25/system-one-bench) - Suites A to I on generated manifests (n=500) for Jev and Laya: calibration, wording sensitivity and cost scaling. `jev-1.13.0 · n=500`
- [smkrv/jev-calibrate](https://github.com/smkrv/jev-calibrate) - Grades a question's answers against labels: vague criteria 0.69 to 0.89 accuracy on the bundled example, rewritten criteria 0.92 to 1.00. `jev-1.13.0`
- [stillmarcus24/jev-verify](https://github.com/stillmarcus24/jev-verify) - Recomputes the confidence and expected-score identities against published artifacts instead of live calls: vendor-channel examples 10/10, recorded responses 843/854, hand-authored fixtures 115/296, with all 121 fractional-part-of-score anomalies in the last group. `jev-1.13.0 · n=1240`
- [xxlya/evaljev](https://github.com/xxlya/evaljev) - Derives the confidence-margin formula from live traces and measures the distribution shift between batched and separate requests. `n=351 decisions`

## Write-ups, critiques and evidence ledgers

Collections that recompute or gather other people's robustness numbers.

- [SamuelSacco/jev-exploration](https://github.com/SamuelSacco/jev-exploration) - Recomputes every published Jev ECE against its sampling noise floor: 2.1 to 2.5x the floor at every difficulty tier; outputs quantised to 0.01 and can be exact 0 or 1. `jev-1.13.0`
- [Zaious/jev-capability-atlas](https://github.com/Zaious/jev-capability-atlas) - Bilingual atlas of where the claim holds, organised on one axis: whether the answer is recoverable from the state or needs outside knowledge.

## Related lists

General directories of Jev projects, which this page does not duplicate:

- [yibie/awesome-jev](https://github.com/yibie/awesome-jev)
- [AbdelStark/awesome-typesafe-jev](https://github.com/AbdelStark/awesome-typesafe-jev)
- [OmniJev/awesome-jev-gallery](https://github.com/OmniJev/awesome-jev-gallery)
- [wh000wh000/awesome-jev-live](https://github.com/wh000wh000/awesome-jev-live)
- [andyrewlee/awesome-system-one](https://github.com/andyrewlee/awesome-system-one)
- [notsointresting/awesome-jev-family](https://github.com/notsointresting/awesome-jev-family)

## Contributing

One line, in the section that fits, pointing at something that reports its own measurements of Jev with the model version and the number of items. Details and how to ask for a correction: [CONTRIBUTING.md](CONTRIBUTING.md).

## Citation

If this list is useful in your work, cite it as a snapshot: the entries and the model version both change over time, so include the date you read it.

```bibtex
@misc{lan2026awesomejevrobustness,
  author       = {Lan, Yifan},
  title        = {Awesome Jev Robustness: independent tests of calibration, consistency and failure modes of the Jev decision model},
  year         = {2026},
  howpublished = {\url{https://github.com/Yifan-Lan/awesome-jev-robustness}},
  note         = {Curated list. Accessed 2026-09-23.}
}
```

Please also cite the individual studies you rely on; their authors did the measuring.

## License

[CC0 1.0](LICENSE). Descriptions paraphrase the linked projects; the numbers belong to their authors.
