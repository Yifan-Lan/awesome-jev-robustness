# Awesome Jev Robustness

Tests, audits and failure-mode studies of [Jev](https://typesafe.ai), TypeSafe AI's System One decision model: calibration, consistency, adversarial inputs, abstention, language coverage, and the jaggedness TypeSafe itself documents.

This list only collects work whose main finding is about how Jev's answers move: with the wording of the question or the options, with the order or number of options, with repeated calls, with the language of the input, with text planted to steer it, with inputs that fit no option, and whether the probabilities it returns can be trusted. Task benchmarks and model comparisons that report an accuracy without probing any of that are kept out of the README; the ones found while building this list are in [`data/task_benchmarks.tsv`](data/task_benchmarks.tsv), and general directories of Jev projects are linked at the end. A project that measured several things is listed once, under the property it says most about.

All numbers below are as reported by the authors. Most were run against `jev-1.13.0` in the two weeks after the model's release on 15 September 2026, usually by one person with a small budget. Read them as evidence to inspect, not as settled results. The machine-readable version with categories, sample sizes and model versions is in [`data/entries.tsv`](data/entries.tsv).

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

## Official material

- [Choice self-consistency cookbook (TypeSafe docs)](https://docs.typesafe.ai/cookbooks/consistency_choice_cookbook) - Repeats 8 Choice questions on one ambiguous post across seven conditions; 99.2% agreement at a 0.60 threshold, flips on 2 of 8 questions.
- [Jev 1.13 jaggedness (TypeSafe docs)](https://docs.typesafe.ai/model-jaggedness/jev-1.13) - TypeSafe's own list of nine failure modes: literal reading, counting, numeric and date comparison, indirection, distracting state, adversarial content, contradictory criteria, non-guaranteed invariants, generation. Qualitative, with one numeric example (a Noul and its negation summing to 1.19).
- [Noul self-consistency cookbook (TypeSafe docs)](https://docs.typesafe.ai/cookbooks/consistency_noul_cookbook) - Repeats 14 Noul questions 15 times each on one insurance claim; standard deviation 0.0102, one borderline answer ranging 0.43 to 0.53 across a 0.5 threshold.
- [TypeSafe evals dashboard](https://evals.typesafe.ai) - TypeSafe's four-workflow comparison of Jev against frontier models, 61.7% to 76.0% agreement; the reference label is the average of two frontier models, which several independent write-ups criticise.

## Calibration and confidence

Whether the probabilities mean what they say: ECE and Brier against labels, confidence as a routing signal, and what happens to calibration off-distribution.

- [does-jev-confidence-mean-anything](https://github.com/Adilmp/does-jev-confidence-mean-anything) - Audits Jev's stated confidence against human-annotated ground truth (civil_comments) rather than another model's opinion: at ~75% stated confidence, only 10% of comments were actually flagged by humans; a 2-parameter recalibration removes 96% of the error.
- [jevrag](https://github.com/ajanm007/jevrag) - Calibration-first evaluation of Jev across five RAG mid-pipeline decisions: confidence ranks well (AURC) but is poorly calibrated on retrieval-stopping (ECE 0.33, Brier skill -0.45) yet well-calibrated on chunk-boundary (ECE 0.087, Brier skill +0.21) with the identical backend and harness.
- [Jev-Calibration](https://github.com/AnthusAI/Jev-Calibration) - Calibration study on 8,801 labeled sentiment examples: Jev's raw Noul/Choice probabilities are overconfident (ECE 0.117 for Noul-as-P(positive), worse for Choice), isotonic regression cuts ECE to 0.008, and calibrated Jev beats Llama 3.1-8B at separating right from wrong answers.
- [jev-biomedical-evidence-screening](https://github.com/cx295410-dot/jev-biomedical-evidence-screening) - Frozen Jev predictions scored for discrimination, calibration and high-recall screening workload on SYNERGY systematic-review data.
- [jevnav](https://github.com/dtduc-git/jevnav) - Browser-automation tool using Jev to pick page elements, with several small measured benchmarks: 41/41 scored element-selection decisions correct across 8 sites (all 30 at p>=0.9 correct), 44/44 in a build-time spike, and loop-mode confidence that does NOT separate correct from wrong decisions (both land p=0.39-0.99).
- [jev-takes-mauboussin](https://github.com/eggmasonvalue/jev-takes-mauboussin) - Ran jev-latest through Mauboussin's 50-question human calibration quiz: 90.0% accuracy at 91.4% mean stated confidence (calibration gap +1.4pts), placing Jev in the top 1.1% of 948 human takers on accuracy and top 7.5% on calibration; 34/34 correct when Jev claimed >=95% confidence.
- [typesafe-ai-jev-example](https://github.com/ItBayMax/typesafe-ai-jev-example) - Comparing hand-authored mock probabilities to 28 real Jev calls found the model's real confidence distribution is extreme (mass at 0.0/1.0) and prefers a clean fallback option over picking among wrong choices.
- [assay-001](https://github.com/jourdanlabs/assay-001) - Calibration audit: Jev's chosen-option probabilities are calibrated on CLINC150 (ECE 0.0204) but overconfident on Banking77 (ECE 0.0936), with zero type errors across 8,576 responses.
- [jev-does-not-play-dice](https://github.com/KantaHayashiAI/jev-does-not-play-dice) - Jev assigns 82.9% mean probability to its chosen option on a fair 6-sided die (true rate 16.7%) while accuracy stays near chance (19.0%), showing reported probabilities do not reflect known uncertainty.
- [jev-arena](https://github.com/meetr1912/jev-arena) - Calibration arena posing questions with analytically known ground-truth probabilities; live Jev scores Brier 0.0059 and ECE 0.0620 on 145 binary events, with a confidence-gated risk-coverage table.
- [jev-vickrey](https://github.com/meetr1912/jev-vickrey) - A sealed-bid auction simulator finds live TypeSafe Jev under-confident on value-threshold probes (ECE 0.1321, Brier 0.1391 vs a perfectly-calibrated oracle's 0.0132/0.0559), causing it to overbid and lose money in second-price auctions.
- [jev-certify](https://github.com/nikkoxgonzales/jev-certify) - Applies conformal prediction and prediction-powered inference to Jev's intent-routing confidence on CLINC150; finds the achievable risk bound floors at 1.95% because Jev returns exactly 1.0 confidence on 56.4% of answers (9 of which are wrong).
- [jev-calibrated-narrative-coding](https://github.com/pozapas/jev-calibrated-narrative-coding) - Research code for 'Calibrated Decisions at Scale': converts police crash narratives to coded variables with pinned jev-1.13.0, audits ECE, Murphy decomposition and calibration slope against coded fields and a human gold set, with a frontier-model baseline.
- [jev-heart-risk-bench](https://github.com/rubinagentagi-tech/jev-heart-risk-bench) - Jev scored on 5,000 real CDC heart-risk survey respondents vs logistic regression, a chat LLM, and base rate; Jev's AUC trails a chat LLM (0.7725 vs 0.7935) and its stated probabilities are badly miscalibrated (Brier skill -1.315).
- [jev-bench](https://github.com/Running-Dolphins/jev-bench) - Calibration/accuracy benchmark of Jev on 12 public classification tasks (500 ex each); reliability tables show over/under-confidence varies sharply by task (e.g. banking77 0.9-1.0 band: stated 0.98, actual 0.90).
- [jev-software-decision-benchmark](https://github.com/scarif-labs/jev-software-decision-benchmark) - Independent AUROC study of Jev vs DeepSeek Flash and static rules for dependency-update auto-merge decisions; Jev leads in-distribution (AUROC 0.851 vs 0.602/0.585) but its calibrated threshold fails to transfer out-of-distribution (50% precision, 15 unsafe merges on 185 OOD cases).
- [jev-probability-experiment](https://github.com/simonmesmith/jev-probability-experiment) - Tested jev-1.13.0 on 68 probability problems (coins, dice, cards); Noul gives the closest probability estimate (MAE 5.56pp) vs outcome-Choice (MAE 21.14pp); numerical-answer Choice got 60/60 correct when the answer was an offered option.
- [jev-noul-vs-choice](https://github.com/TakumiNoguchi2004/jev-noul-vs-choice) - Root-causes a known Jev miscalibration (fair-die "choice" collapses to ~77% confidence on one face) and shows it is specific to the Choice primitive: independent Noul questions recover near-true 1/6 probabilities for every face, with severity of Choice's bias scaling with option count.
- [jev-skills](https://github.com/WanLanglin/jev-skills) - Coding-agent skill pack measuring Jev's own calibration on 4,995 real decisions (Noul ECE 0.169, Choice ECE 0.226, worse), plus batching cost economics vs Claude (145x/363x cheaper for 256 batched judgments).
- [typesafe-jev-tools](https://github.com/wotai-dev/typesafe-jev-tools) - Claude Code hook that judges whether code needs a model at all; separately benchmarks 16 models (incl. Jev) for calibrated binary judgment on 150 passages: Jev is fastest (455ms) and the best-calibrated of the sub-second models, but flags uncertainty 5x more than Haiku.
- [laya-jev-eval](https://github.com/yuvrajrox/laya-jev-eval) - Byte-identical-prompt bake-off of Laya vs Jev on email-intent classification: both hit 98% on a 100-email holdout, but fine-tuned Laya collapses to 1.00 confidence on its errors while Jev's errors sit at lower confidence, enabling a confidence-based escalation cascade.
- [Calibration, decomposition and shadow evals (beri.net)](https://beri.net/article/typesafe-jev-typed-decision-model-calibration-decompo) - Pulls together the phishing study (62.6% as one question, 95.0% decomposed into five) and the 900-ticket OOD calibration test (ECE 0.107, 4.4x the noise floor); recommends per-question calibration and a none option.
- [Can you trust Jev's confidence? (Anthus)](https://anth.us/blog/can-you-trust-jev-confidence) - Calibration by question type on 8,801 labeled sentiment examples: Noul stated 79.0% vs 72.3% actual, Choice 91.4% vs 76.1%; the 50 to 95% confidence band was only 50 to 57% correct.
- [Legal documents yes/no test (X article)](https://x.com/i/article/2100463318209048850) - 544 legal documents, 109 labelled yes/no judgments: Brier 0.030; all 96 answers outside the 0.2 to 0.8 band were correct.
- [Support-ticket benchmark (thoughts.jock.pl)](https://thoughts.jock.pl/p/jev-typesafe-system-one-model-benchmark-2026) - 40 hand-labeled tickets, routing, urgency and anger, against Haiku, Fable, Astra and Gemini Flash: routing 97.5%, 8/40 urgency errors; Choice confidence is bimodal while Score confidence clusters mid-range, which makes one threshold unreliable.
- [When a Judgment Layer's Self-Reported Fields Lie (Zenodo)](https://doi.org/10.5281/zenodo.22901853) - Independent measurement of Jev as one of three judgment layers, testing whether the self-reported confidence fields track correctness.

## Consistency and invariance

Whether the same judgment comes back the same way: repeat runs, option order, Noul against Choice, a statement against its negation, paraphrase, and how many rows share one request.

- [jev-wide](https://github.com/123Satyajeet123/jev-wide) - Measures Jev's behavior when ranking/reranking beyond its per-call limits: probabilities violate IIA (log-odds shift +0.31 to +0.50 with unrelated candidates), 95.8% of a 200-candidate field returns quantized 0.00, and identical repeated calls change 7.3% of a top-10 ranking.
- [jev-system-one-study](https://github.com/agrogov/jev-system-one-study) - Black-box study of Jev plus controlled replays of the same ~8,300-request suites against open models Laya and SemIf (Qwen3.5-4B).
- [audio-jevlike](https://github.com/alperiox/audio-jevlike) - Audio-native 'System One' reproduction (Prosodia) reports that real Jev violates independence of irrelevant alternatives - adding an unrelated option shifts log-odds between two untouched options by ~-0.28 across ten randomized blocks.
- [jev-labs](https://github.com/copyleftdev/jev-labs) - TLA+-verified pharmacy-decision consensus kernel wrapping Jev; found Jev is non-deterministic (identical requests returned 0.03-0.04 across 5 calls) and measured distinct noise floors for identity/reorder/paraphrase, with 0 wrong verdicts across 1,080 golden simulated rounds under seeded chaos.
- [jev-as-a-judge](https://github.com/danielgshea/jev-as-a-judge) - Compares Jev, GPT-5.6 Luna/Terra and Claude Sonnet 4.6 as agent-eval judges on accuracy, repeat-score variance, cost and latency.
- [browser-jev](https://github.com/DowLucas/browser-jev) - Adversarial browser-exploration tool found narrow oracle questions score far better than broad ones (0.99 vs 0.30) and the same page state can score 0.72-0.87 across repeat calls.
- [jev-rag-benchmark](https://github.com/erendikmenn/jev-rag-benchmark) - Jev vs Cohere Rerank 3.5 as a RAG reranker on 1,044 Turkish XQuAD questions; both recover the identical number of gold passages into top-5, with Jev 60.6% cheaper, but a candidate-order permutation audit shows real sensitivity (mean Spearman 0.262).
- [jev-eval](https://github.com/finnhll/jev-eval) - 282-trial harness testing Jev's documented claims via OpenRouter: batching independence confirmed to the digit, repeat stability (sd<=0.0075), but option-order reshuffling shifted a winning probability 0.62->0.48 and uncovered inputs answered wrong at >0.93 confidence.
- [jev-sort](https://github.com/heddendorp/jev-sort) - Jev-powered pairwise "fuzzy sort" library; a live 100-ticket benchmark got 92.85-98.38% pairwise-ordering agreement with a stated priority policy, and found non-transitive judgments.
- [jev-synthetic-survey](https://github.com/jjd-lab/jev-synthetic-survey) - Jev vs GPT-4.1 as synthetic survey respondents on Twin-2K-500; asked as Noul, Jev beats GPT-4.1 probabilities on all 6 measures at 1/34 the cost, but as Choice it loses the headline distribution-gap metric.
- [opencode-jev-compaction](https://github.com/JLegends/opencode-jev-compaction) - Context-compaction plugin found noul-style judgments unreliable on subjective questions (0.996 on hard facts vs 0.003-0.28 on judgment calls) and that a statement and its negation both scored ~0.95 with noul, fixed by switching to explicit-criteria choice questions.
- [pi-heed](https://github.com/nyarlathoteppppp/pi-heed) - Runtime rule-enforcement layer for a coding agent that uses Jev for narrow judgments; measured findings include calibrated-but-slightly-underconfident probabilities, flat latency across question count, and strong anchoring on field order.
- [jev-judge](https://github.com/orq-ai/jev-judge) - Judge-repeatability study scoring 12 frozen agent-run cases 100x each; Jev repeats its verdicts fully while 3 LLM judges (deepseek/gemini/gpt) show real run-to-run variance, none beating a majority-class baseline on binary evals, even with sampling off.
- [jev-vs-ml](https://github.com/vianaR25/jev-vs-ml) - Jev and Laya (zero-shot) vs classic trained ML on three Kaggle datasets; Jev is competitive on AG News/tweets but far behind trained models on Titanic, and is measurably non-deterministic (probability shifts up to 0.13 on repeat queries).
- [duckdb-jev](https://github.com/yodablocks/duckdb-jev) - DuckDB extension gating semantic SQL functions on Jev's calibration; a 6-condition calibration/invariant gate (Brier, ECE, negation symmetry, rubric ordinality) on 360 fresh live requests, 5 of 6 passing.
- [jev-orderby-bench](https://github.com/yodablocks/jev-orderby-bench) - Measures whether ORDER BY over a Jev probability gives a defensible sort: passes all 6 pre-registered gates on 360 labeled rows (inversion 0.036) but fails 4 of 6 on a harder Amazon-ESCI product-ranking probe (inversion 0.255), and batching 40 rows/request breaks the gate that passes.
- [jevfuzz](https://github.com/yottayoshida/jevfuzz) - Fuzzer that renames/reorders question IDs, Choice options and JSON keys and checks whether Jev's decision changes; measured run found six confirmed robustness violations out of 100 mutations across 10 states.
- [structured-decision-bench](https://github.com/zhengbangbo/structured-decision-bench) - Qwen3 8B vs Jev 1.13.0 vs Laya CoreML on 24 typed decisions (choice/noul/score), each repeated 10x: Jev and Qwen are both 100% label-consistent across repeats, but Jev is far slower (808-848ms vs 6-100ms).
- [Repeatability test (DataCamp)](https://www.datacamp.com/blog/system-one-models-jev) - 500/500 agreement with a human oracle across repeated runs; per-case variance 92x to 913x lower than GPT-5.6 and Claude Sonnet comparators.

## Input perturbation and context

How much the answer moves when the input or the criteria are reworded, enriched, or degraded without changing the intended question.

- [jev-phishing-bench](https://github.com/anisselbd/jev-phishing-bench) - Jev vs Claude Haiku on 2,000 phishing emails: single verdict 62.6% vs 81.3% accuracy, but Jev's 5 decomposed signal questions fed into a logistic regression reach 95.0% vs Haiku's 93.2% (not statistically significant).
- [jevaluate](https://github.com/ElshinQ/jevaluate) - Jev integration notes: 60 multilingual routing phrases show 0 confidently-wrong answers (all misses flagged low-confidence), splitting one decision into 3 questions destroys calibration (0.55/0.42 vs merged 1.00), and Jev drives a QA walk 6.9-13.8x cheaper than a vision model.
- [jev-bias-bench](https://github.com/Fox-Islam/jev-bias-bench) - Counterfactual fairness benchmark: swaps 29 demographic attributes (140 levels) one at a time across 10 high-stakes decision scenarios (hiring, lending, bail, clinical triage, etc.) on Jev (11,984 calls) and Claude Opus 5 (2,984 calls), measuring which swaps move the decision beyond the model's own noise.
- [jev-entropy-gate](https://github.com/gordan-code/jev-entropy-gate) - Code-migration triage tool found rewriting a task's phrasing alone flipped the same code site from "auto" (0.81, deterministic) to "manual" (0.50, judgment) with no code change.
- [jev-loan-identity-benchmark](https://github.com/KiishiAD/jev-loan-identity-benchmark) - Synthetic bitemporal loan-matching benchmark with noisy/adversarial-style perturbations (typos, OCR corruption, sponsor confusion); Jev 1.13.0 reached 100% held-out precision, 97.5% recall, 100% Recall@1/@4.
- [jev-playground](https://github.com/kobashi/jev-playground) - Music call-and-response app: Jev's judgment of musical phrases swings 34 points with encoding choice (63% vs 97% cadence detection), well calibrated on clear rules (94 to 100%) but near chance on a subjective echo question (64%).
- [llm-prompt-techniques-on-jev](https://github.com/leepokai/llm-prompt-techniques-on-jev) - Ports LLM prompting techniques (CoT, self-consistency, few-shot, GEPA) to Jev via DSPy and measures effect across LegalBench, BBH, MMLU-Pro and CLERC; most 'reasoning' techniques do nothing since Jev has no scratchpad.
- [jev-audit](https://github.com/phuthuycoding/jev-audit) - Pre-commit auditor using Jev to catch secrets/vulnerabilities in code diffs; 79-case corpus scores 100% strict accuracy (secret F1 1.00, vuln F1 0.98 @0.8), with secrets robust when buried in 49KB of noise.
- [jev-classification-prompting](https://github.com/RastislavDujava/jev-classification-prompting) - Nine prompt-engineering ablations on jev-1.13.0: criteria wording raises paired accuracy 70%->96%; a knowledge-cutoff blind spot fixed 66.7%->100% by rewriting criteria.
- [typesafe-jev-calibrate-for-code-review](https://github.com/Selmar/typesafe-jev-calibrate-for-code-review) - Two days calibrating Jev as a C# code reviewer: rule-by-rule pass/fail probes against a 0.60 threshold, finding comment rules improve with whole-file context and that Jev reads facts better than it derives them; about 1.7M input tokens for the test files.
- [jev-experimental](https://github.com/SYED-M-HUSSAIN/jev-experimental) - Test suite on jev-1.13 covering capability limits, meaning-vs-wording, accuracy/calibration and consistency; accuracy rose from 83% to 100% once criteria (not the question) were spelled out, Brier 0.038.
- [jev-village](https://github.com/tfolkman/jev-village) - Life-sim of 60 villagers decided by live Jev: ~109x cheaper than a simulated frontier LLM, but wording of criteria flipped correct behavior entirely.
- [Korean sentences, one call vs whole document (Threads)](https://www.threads.com/@ebrain.lab/post/DddGgXuoLlL) - 40 Korean sentences: 40/40 when sent one per call, matching Claude Opus at about 1/24 the cost; 62% when the whole document went in one call.
- [Norwegian hearing documents (lindfors.no)](https://lindfors.no/blog/a-first-look-at-typesafes-jev) - 24 Norwegian public-hearing documents: stance 20/24 correct, 97% agreement with a reference model at 0.7 to 0.9 confidence; a stricter question wording worsened ECE from 0.040 to 0.116.

## Prompt injection and adversarial inputs

Text planted in the state to move Jev's own verdict: injected instructions, claimed approvals, misleading framing. Studies of Jev as an injection detector are in the task-benchmark file, not here.

- [openpoke-meets-jev](https://github.com/0xshin0221/openpoke-meets-jev) - Jev vs Claude Sonnet 4 in an email-triage/guardrail fork of OpenPoke; no accuracy claimed, but a targeted prompt-injection/contamination test found the injection gate suppressible 95.3% of the time.
- [jev-vs-sovereign-benchmark](https://github.com/azterizm/jev-vs-sovereign-benchmark) - Benchmarks Jev System One against a specialized sovereign RAG stack (DistilBERT/ColBERT/DeBERTa) on UK legal RAG; Jev is over 2,800x slower for routing and shows 0% abstention (vs 100% clean) on an adversarial fake-law probe.
- [decision-injection-bench](https://github.com/cwhy/decision-injection-bench) - Prompt-injection attack suite on Jev, Winnow, SemIf and Laya classifiers; Jev flipped on 1/1,056 attacks (0.09%) vs 3.0-62.6% for others.
- [jev-engineering](https://github.com/eugeniughelbur/jev-engineering) - A Jev-backed tool-call safety gate for coding agents resists blunt prompt injection (0/30 dangerous commands passed) but 'authority' injection (claimed human approval) got up to 3/30 dangerous commands through, at ~400ms/$0.00002 per check.
- [jev-transaction-guard](https://github.com/finrod21/jev-transaction-guard) - Financial anomaly-detection gate using Jev resists an in-memo prompt-injection bait (99% probability circuit-breaker trip) and beats GLM 5.3 Flash 8-23x on latency/cost at matching verdicts across attack scenarios; 100% deterministic verdicts and token counts over 5 repeats.
- [jev-rcos-study](https://github.com/Foshowithit/jev-rcos-study) - Falsification-first study of Jev as a capability router: confidence-gating AUC 0.44 (inverted, falsified) and 13/13 resistance to adversarial near-duplicate candidates.
- [system1-system2](https://github.com/Iskandeur/system1-system2) - Confidence-gated Jev-then-LLM routing measured on 600 MASSIVE utterances: Jev is well-calibrated (ECE 4.5%, AUROC 0.83), but escalation buys no net accuracy since GPT-5.2 is no better on this task, and under prompt injection GPT-5.2 is flipped 100/80 times by one payload vs 26/80 for Jev.
- [jev-benchmark](https://github.com/themsquared/jev-benchmark) - Jev classifying agent tool-call risk (readonly/destructive/privileged/exfiltration) on 60 hand-labeled cases including adversarially-worded destructive commands: 91.7% accuracy, and every wrong answer carried hedged confidence.
- [jev-evaluation](https://github.com/willkelly/jev-evaluation) - Preregistered adversarial evaluation of jev-1.13.0 (123,805 requests): calibration holds on support routing (ECE 0.075) but collapses on random 3-SAT (P(satisfiable) barely moves despite true rate spanning 0 to 1); batching 60 questions is 20x cheaper/8x faster with identical answers.
- [jagged](https://github.com/zkousama/jagged) - Pre-registered study on 486 Wikipedia deletion discussions finds Jev 1.13.0 96.5% accurate/0.230 ECE at baseline, collapses to 26.5% accuracy under a one-line prompt injection, and shows a mirrored-question probability gap.
- [Prompt injection can influence the verdict (VentureBeat)](https://venturebeat.com/security/companies-are-putting-jev-in-charge-of-ai-age) - Reports an engineer's test of Jev as an agent action gate: block probability for `rm -rf ~/.ssh` fell from 0.76 to 0.48 after a fake pre-approval was injected into tool output.

## Abstention and unanswerable inputs

What Jev does when no option is right, when the answer is not in the state, or when the input is off-distribution; the effect of an explicit unknown or none option.

- [jev-decision-benchmarks](https://github.com/baibizhe/jev-decision-benchmarks) - JEV vs GPT/Llama/Qwen/xLAM on tool-selection and abstention benchmarks (MetaTool, When2Call, BFCL); JEV leads BFCL relevant-action accuracy (87.50%) and abstains best on MetaTool, but hallucinates a tool call on 76% of When2Call's no-tool cases.
- [jev-calibration-audit](https://github.com/jujumilk3/jev-calibration-audit) - API-only audit: removing the abstain option collapses accuracy from 0.950 to 0.000 and ECE from 0.023 to 0.793; option order, batching and Korean-language instructions show negligible effect.
- [jev-ood-calibration](https://github.com/scienthoon/jev-ood-calibration) - Calibration test of Jev on 3 public benchmarks plus a contamination-free synthetic rule task; synthetic ECE is 4.4x the noise floor and the unknowable-label question is confidently wrong.
- [jev-bbq-experiment](https://github.com/simonmesmith/jev-bbq-experiment) - Jev answers all 58,492 public BBQ bias-benchmark questions at 97.28% accuracy (99.96% on ambiguous, 94.60% on informative), with position-reversal changing only 1/484 paired answers.
- [jev-abstentionbench](https://github.com/sshariqali/jev-abstentionbench) - Runs Meta's AbstentionBench against Jev and compares to 20 published 2025 LLM systems; Jev's mean per-dataset abstention F1 of 0.855 ranks 1st, ahead of GPT-4o (0.760), via much higher recall at similar precision.

## Failure modes and capability limits

Probes of the limits TypeSafe documents and of others found since: counting, arithmetic, sequential state, literal reading, option position, and tasks where the signal is not in the text.

- [jev-first-look](https://github.com/colinmcnamara/jev-first-look) - First-look at Jev: reproduces vendor jaggedness examples exactly, finds P(x)+P(not x) sums 0.93-1.19 over 20 negation pairs, and measures ECE ~0.09 on SST-2/AG News, comparable to a self-hosted Qwen 27B baseline that is 1.8-4.5x slower.
- [typesafe-ai-test](https://github.com/dopeCape/typesafe-ai-test) - Six-track stress test of Jev (~8,400 calls): confirms documented API limits exactly, finds it overconfident at low confidence bands but well-calibrated above 0.9, largely immune to prompt injection, and unable to count/sort/add.
- [jev-deterministic-benchmark](https://github.com/etsabary/jev-deterministic-benchmark) - 1,000-decision benchmark across 25 reasoning families; Jev scores 97-100% on static/relational logic tasks but only 13.2% on sequential state-mutation tasks and 33.3% on exact truth-counting, while its stated confidence is highly predictive (>=0.95 correct 492/493 times).
- [jev-field-tests](https://github.com/fly2abhishek/jev-field-tests) - Twelve field tests plus follow-ups on Jev: 89% accuracy/0.03 ECE on 400 BoolQ items, overconfident 4-way calibration (0.998 stated vs 0.90 actual), counting/date weaknesses, and 27/28 SQL-injection and guardrail detection.
- [lunar-terminal](https://github.com/gdchaochao/lunar-terminal) - Measured 327 randomized Robocode decisions show Jev's action choice near-random (Pearson r=-0.099) while its yes/no judgments score 0.94-0.96 on atomic questions.
- [jev_testing](https://github.com/Maxi91f/jev_testing) - Exploratory failure analysis of jev-1.13.0 from 17 to 18 September 2026: runnable scenario scripts for observed counterexamples, explicitly selected because they failed, with a methodology report.
- [llm-jev-laya-bench](https://github.com/PerryLink/llm-jev-laya-bench) - Research paper/artifact: Jev and Laya as judgment layers score 0.225-0.90 on a 77-class battery; both fail tasks needing absence-detection.
- [experiments](https://github.com/phuryn/experiments) - Hardens TypeSafe's own invoice showcase to 50 documents: Jev ties Claude Haiku 4.5 at 50/50, but a hidden house rule Jev was never told makes it wrong 19 of 24 times, 15 of them at high confidence.
- [jev](https://github.com/priorbench/jev) - Pre-registered independent evaluation of Jev (5,721 calls, 21 experiments): 95.9% zero-shot accuracy, but 0/30 out-of-scope inputs flagged without an explicit "none" option, and reliable only above 0.99 confidence.
- [jev-csat-math-probe](https://github.com/pycodinglec/jev-csat-math-probe) - Three Korean CSAT math problems used to probe where Jev stops working as a reasoner and works only as a typed decision model.
- [jev-behavior-study](https://github.com/RINNECODER/jev-behavior-study) - 11,621-request field guide to Jev 1.13.0: arithmetic accuracy 88.0% when the correct option is listed first vs 57.4% when last, and a single wording change moved travel-choice accuracy from 0/20 to 20/20.
- [jev-field-notes](https://github.com/scd13150/jev-field-notes) - Multi-domain measurement project (fighting game, TTS, SVG geometry) plus an 8,000-call empirical boundary study.
- [jev-arc-agi-v1-experiment](https://github.com/simonmesmith/jev-arc-agi-v1-experiment) - Jev fully solved 4/400 ARC-AGI-1 public eval tasks (1.125% score) via per-cell Choice decisions, at $2.32 total cost.
- [can-jev-bayes](https://github.com/TomRichner/can-jev-bayes) - Tests Jev on multi-armed bandit sequential decision-making against Bayesian baselines (Thompson sampling, Bayes-UCB); Jev follows supplied optimal action values 99.93% of the time but under-explores without them, and richer uncertainty summaries did not uniformly help.
- [jev-tetris](https://github.com/Tsagaanbayr1/jev-tetris) - Jev can't judge a Tetris board from raw text (0.71 confidence on the worst option) but ranks well when given computed outcome features instead.
- [yks-bench](https://github.com/UgurcanAkkok/yks-bench) - Jev on the 2026 Turkish university entrance exam (593 text-only questions, contamination-free): 82.3% accuracy vs a 24.6% majority baseline; accuracy drops 32.5pp when the question text is deleted, and math collapses to net-negative when a figure is required.
- [jev-benchmark](https://github.com/wondertwins/jev-benchmark) - Two capability-boundary probes on Jev: chess (worse-than-random from a raw FEN board, ~950 Elo once given hand-computed tactical facts) and NPC-addressee detection (F1 0.96 clean text, 0.93 noisy speech-to-text).
- [should-i-jev](https://github.com/yakubmurcek/should-i-jev) - Recorded Jev answers matched hand-authored expectations only 6/27 at first; literal-reading and compound-question bugs fixed, reaching 27/28 after 3 iterations.
- [Jev is the fish at the poker table](https://backnotprop.com/blog/jev-poker) - 30 solver-checked poker spots: matched the solver 63% of the time, 15 to 30 point probability swings from relabelling the same hand, bet into a made flush 16 of 16 times.
- [Pre-registered test (primeline.cc)](https://primeline.cc/blog/typesafe-jev-pre-registered-test) - About 9,750 calls with pass/fail bars written before the runs: 12 probes of TypeSafe's documented failure modes, 4 real and 8 refuted; 2 of 7 recommended wording fixes null; 40-pair injection test 22.5% misclassified; Choice confidence shown to equal (N*p_max-1)/(N-1).

## Languages other than English

Matched English and non-English items, or the same task in several languages, so the effect of the input language on accuracy and calibration is measured. Benchmarks that simply run in another language are in the task-benchmark file.

- [jev-cyrillic-audit](https://github.com/AHTOOOXA/jev-cyrillic-audit) - Jev's accuracy and calibration in Russian vs English on XNLI (n=600 paired): accuracy 77.3% vs 88.3%, ECE 0.096 vs 0.032; traced to a cross-lingual entailment-recognition failure, not tokenization or length.
- [Jev-Persian-Benchmark](https://github.com/ArmanJR/Jev-Persian-Benchmark) - 480 authored Persian (Farsi + Finglish) questions across 10 linguistic/pragmatic categories run against Jev 1.13.0: 99.6% Choice accuracy, 99.4% Noul, 95.0% Score, with perfect repeatability and English-vs-Persian parity.
- [jev-korean-benchmark](https://github.com/mahlernim/jev-korean-benchmark) - 100-question-per-cell sample check of Jev in Korean vs English and vs GPT-5.6 Luna across 4 public exams; Korean reading comprehension shows no meaningful cost, but Jev trails Luna by 8pp on the Korean medical exam.
- [jev-acento](https://github.com/marcosmartinez/jev-acento) - Spanish-language audit: swapping only the input text from English to Spanish costs Jev 3.0-6.4pp accuracy and roughly doubles calibration error on the hardest two of four datasets; writing instructions in Spanish does not help.

## Evaluation tooling

Harnesses built to probe wording, ordering or calibration, listed when they ship a measured run.

- [jev-kit](https://github.com/FlorianRiquelme/jev-kit) - Typed client + benchmark harness for Jev; the shipped example run against 15 real-project fixtures gets 81.7% overall accuracy but shows one question ('needs_human', an indirect compound judgment) scoring 46.7% - worse than a coin flip - which the harness itself flags.
- [system-one-bench](https://github.com/rssr25/system-one-bench) - Pip-installable benchmark suite (suites A-I) running Jev 1.13.0 and Laya 0.3.4 on identical generated manifests (n=500), measuring calibration, wording sensitivity, and cost/latency scaling.
- [jev-calibrate](https://github.com/smkrv/jev-calibrate) - CLI that grades whether a Jev question's answers are usable against labels; on its bundled example, vague criteria scored 0.69-0.89 accuracy and rewritten criteria reached 0.92-1.00 with holdout AUC up to 1.00.
- [evaljev](https://github.com/xxlya/evaljev) - Runtime-assurance library for Jev decision workflows: derives Jev's confidence-margin formula from live traces, measures batched-vs-separate-request distribution shift, and finds paired requests are 34.7% cheaper and 49.4% faster than separate calls.

## Write-ups, critiques and evidence ledgers

Ledgers and field guides that recompute or collect other people's robustness numbers.

- [jev-exploration](https://github.com/SamuelSacco/jev-exploration) - Evidence-ledger meta-analysis recomputing every published Jev ECE against its sampling noise floor, plus original experiments; finds Jev's probabilities are miscalibrated 2.1-2.5x the noise floor at every difficulty tier and are quantized to 0.01 (can return exact 0 or 1).
- [jev-capability-atlas](https://github.com/Zaious/jev-capability-atlas) - Bilingual capability atlas synthesizing own tests and third-party benchmarks into an axis of "signal-sufficient vs needs-external-knowledge"; own tests include a history-recall miscalibration case (0.90 confidence on a wrong answer after a typo) and measured intent-classification ECE 0.041.

## Contributing

Open a pull request that adds one line in the right section. The entry has to point at something that reports its own measurements of Jev, name the model version it hit, and say how many items it ran. See [CONTRIBUTING.md](CONTRIBUTING.md) for the details and for how to ask for a correction.

## License

[CC0 1.0](LICENSE). Descriptions paraphrase the linked projects; the numbers belong to their authors.
