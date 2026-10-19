# Awesome Jev Robustness [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> How [Jev](https://typesafe.ai)'s answers move, and whether its probabilities can be trusted. Independent tests of TypeSafe's System One decision model, grouped by what they measured.

161 entries · almost all against `jev-1.13.0` · snapshot 2026-10-18 · numbers are the authors' own

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
| Option order | Negligible on most tasks: reversing two options moved probability by 0.005 and flipped 0 of 400 argmaxes. On ambiguous or value-laden questions it matters: the first-listed option gained 0.37. | [calibration-audit](https://github.com/jujumilk3/jev-calibration-audit), [JevBench issue 40](https://github.com/fstandhartinger/jevbench/issues/40), [jev-bias-audit](https://github.com/pawarbi/jev-bias-audit) |
| Option names | Names carry meaning the rubric does not override: binding no/yes instead of 0/1 to the same two rubrics dropped hosted Jev from AUC .81 to .58; random-string names remove the effect. | [Sun and Xu](https://arxiv.org/abs/2609.26758) |
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
- [Papers](#papers)
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

## Papers

Preprints that evaluate hosted Jev on a robustness or calibration question. Application papers are in `data/task_benchmarks.tsv`.

- [Adversarial Cues in Decision Models Used as Judges: The Role of Request Presentation](https://arxiv.org/abs/2610.11436) - Liu: a judge grading the final commitment should reject a wrong final value, yet adding one colon to a candidate raises Jev's false acceptance from 1.0% to 26.0% (three labels) and 3.0% to 26.5% (four labels) under sorted-JSON presentation, on 200 unused DROP and GSM8K clusters; the same edit is rejected under insertion presentation. Presentation of the request decides whether a trivial edit is an attack. `jev-1.13.0 · n=200 clusters`
- [Auditing System-1 Models on Biosecurity-Relevant Benchmarks: Calibration, Selective Prediction, and Permutation Instability](https://arxiv.org/abs/2609.30454) - Provatas and Georgakopoulos-Soares: 6,020 biosecurity-relevant items (WMDP, paraphrase-robust WMDP-Bio, six LAB-Bench subtasks). Once the uncertainty field is read correctly the model is reasonably calibrated (pooled ECE 0.034, AUROC 0.820 for error detection), but both degrade substantially on harder subtasks and answers are sensitive to option permutation. `jev-1.13.0 · n=6,020`
- [Benchmarking Candidate Coverage and Rejection Policy Transfer in Typed Decision Models](https://arxiv.org/abs/2610.03387) - Lu and Wu: rejection policies do not transfer. A Jev rejection threshold calibrated on DBpedia rejects 69.3% of covered Emotion inputs while an Emotion-calibrated one loses detection entirely; with ten intent candidates Laya detects 99.0% of out-of-scope queries but rejects 48.8% of covered ones. Benchmark separates artificial omission, retrieval misses and out-of-scope queries. `jev-1.13.0 · Laya · Qwen2.5-7B`
- [Benchmarking System One decision models against trained classifiers and language models for automated decision gates](https://arxiv.org/abs/2610.00346) - Rafe and Das: one matched harness over eight decision-model checkpoints (incl. hosted Jev), two generative comparators and trained/zero-shot classifiers on workflow, intent and social-science items. Swapping yes and no flips 50.5 answers per hundred for Jev; a held-out 5%-risk threshold still admits 31% of out-of-scope requests; temperatures fitted on few options raise calibration error with many. Small trained classifiers win on intents when labels exist. `jev-1.13.0 · 8 checkpoints / 6 families`
- [Beyond Answer Confidence: A Controlled Audit of Self-Knowledge in a Black-Box Decision Model](https://arxiv.org/abs/2610.01006) - Shankaranarayana, Runje and Jannink: does low confidence mean missing knowledge? Across 15+ datasets and six generated task families with paired information interventions, Jev is calibrated on familiar tasks but assigns up to 0.80 to a salient option with no answer-relevant information, and beyond its knowledge boundary confidence exceeds accuracy by 0.21-0.33, which recalibration does not close. A separate 'does the evidence suffice?' question reads it out at AUROC 0.95. `jev-1.13.0 · 15+ datasets`
- [Beyond Calibration: Do a Typed-Decision Model's Probabilities Obey the Probability Axioms?](https://arxiv.org/abs/2609.33209) - Li et al.: whether Jev's probabilities for logically related questions cohere (obey the probability axioms), tested label-free. On 480 negation pairs, P('label is X') and P('label is not X') miss summing to one by 0.064 on average (95% CI 0.055-0.072). `jev-1.13.0 · n=160 items`
- [Calibrated to Whom? Persona and Language Effects on Cultural Values in JEV](https://arxiv.org/abs/2609.36399) - Asseri and Asseri: JEV answers the Values Survey Module as Saudi and American personas, with no persona, in English and Arabic, under eight request formulations (288,000 answers). Highly repeatable (ICC 0.997) and American-leaning by default; a Saudi persona reproduces 87% of the human Saudi-US difference in English but 62% in Arabic, the shortfall coming from the language of the items; age shifts answers about as much as nationality. `jev-1.13.0 · 288,000 answers`
- [Cheap, Consistent, Calibrated, but Biased? Typed Decision Models as Judges](https://arxiv.org/abs/2610.18122) - Lukassen, Weisser and Silbersdorff: CALM judge-bias tests on eight typed decision models and seven LLM judges. Every TDM is biased in three to eight of twelve tests; rewriting the better answer in an emotional tone drops a typical TDM from 99% to 42%; all eight change verdicts when answers swap places, which 2026 LLM judges hardly do; the flips land mostly on verdicts the TDM was unsure of, and calibration does not protect against bias. `8 TDMs incl. Jev-class · CALM`
- [Correct Choices, Failed Sequences: Choice2Seq Benchmarks Correctness Retention Across Output Interfaces](https://arxiv.org/abs/2610.16191) - Lai et al.: Choice2Seq asks whether a correctly selected answer survives being built step by step. Jev selects correctly 97.1% (arithmetic) and 99.5% (lexical retrieval) yet retains only 33.0% and 2.1% of those under serial construction; restricting local options to candidate characters and explicit position indexing recover part of the gap. `jev-1.13.0 · Choice2Seq`
- [Decision Hijacking: Prompt Injection Attacks on Jev's Typed Probabilistic Decisions](https://arxiv.org/abs/2609.28613) - Ren et al.: prompt-injection attacks on Jev over 510 reconstructed InjecAgent cases. Malicious content shifts action probabilities but rarely selects the attacker's target; adaptive attacks with score feedback double the mean attacker-target probability and lift fresh-validation success from 1.8% to 3.5%, concentrated where the initial decision margin is small. `jev-1.13.0 · n=510 cases`
- [DICE: Tilting the Odds through Interface Attacks on System One Model](https://arxiv.org/abs/2610.14404) - Dong et al.: DICE, interface attacks that leave Jev's authentic responses untouched and instead substitute bounded changes at the adapters around it (state extraction, question generation, output interpretation). In three small pilots each adapter can change the final decision while Jev's request-level judgments stay correct; binding checks stop the tested cases but reject some valid clean decisions too. `jev-1.13.0 · pilot`
- [Do System One Decisions Add Up? A Study of Probabilistic Coherence](https://arxiv.org/abs/2609.33971) - Joy: does the same decision add up at two granularities? On 72,000 questions over TREC, CLINC150 and MASSIVE, Jev's direct fine labels and its broad-category probabilities disagree with total variation 0.219-0.349 (Laya 0.424-0.689); reconstructing fine labels through categories cuts Jev's CLINC150 accuracy by 22.9 points [-24.9, -20.9] while raising Laya's by 21.3. `jev-1.13.0 · 72,000 questions`
- [Does a Hosted Decision Model Respect Irrelevant Alternatives? A Pre-Registered Test of Option-Set Dependence in Jev](https://arxiv.org/abs/2610.16655) - Rajput, Chauhan and Kosaraju: pre-registered IIA test on jev-1.13.0 with 299,463 calls over BANKING77, CLINC150 and AG News. Adding an option that is neither the answer nor its strongest rival still moves the gold-vs-rival log-odds by 0.20-0.52 nats RMS, 1.2 to 3 times the run-to-run noise, even when the added option takes at most 0.1% of the mass; the shift is directional. `jev-1.13.0 · 299,463 calls · pre-registered`
- [Does System One Transfer? Calibrated Typed-Decision Models, Label Budgets, and Translation for Indonesian Regional Languages](https://arxiv.org/abs/2610.14112) - Fadli et al.: pre-registered Jev vs Laya-multilingual on Indonesian regional languages (NusaX 12 languages, NusaTranslation 7). Jev is right more often on sentences lexically closer to Indonesian, and supplying the human Indonesian translation lifts accuracy by up to 37.2 points; its ECE does not grow on regional languages but its ability to rank its own errors degrades everywhere. `jev-1.13.0 · 19 languages · pre-registered`
- [Evaluating System One Models for Agent Security Decisions: Reliability, Calibration, and Selective Automation](https://arxiv.org/abs/2609.33401) - Evaluates Jev, Laya, Decider and Bespoke Nimble as agent-security judges (injection detection, risk, harmful-request screening) against classifiers and LLM judges. Strong aggregate accuracy and calibration hide failures concentrated in particular attack groups, including attacks classified safe with high confidence; adapted configurations don't consistently beat their base models. `jev-1.13.0 · 4 System One models`
- [Fast Models, Slow Evidence: A Paired and Self-Audited Evaluation of System-1 Decision Models for LLM Agent Harnesses](https://arxiv.org/abs/2610.02267) - Li: paired, self-audited Jev-vs-Laya evaluation on 11 agent-harness decision points, 7,283 base cases plus 6,640 robustness variants with byte-identical inputs and cross-day reruns. Jev wins 9/11 (+10.8 to +46.0 pp); neither beats chance on zero-shot model routing; Laya flips 30% of answers under option-order reversal and falls to 31% at 50 similar tools vs Jev's 98%. The paper audits its own errors: a reported 23.9% cost saving was really 4.3%, gate accuracy 58% had been reported as end-to-end 98%. `jev-1.13.0 · 7,283 + 6,640 cases`
- [Hidden Risks of Jev: An Empirical Study of Security, Privacy, and Dual Use](https://arxiv.org/abs/2610.04985) - Wang et al.: the first systematic security, privacy and dual-use study of Jev as a decision layer, on the official API plus a local NanoJev with controllable training data. Adapts prompt injection and adversarial suffixes, implants backdoors through poisoned training data, and recovers sensitive information with membership, private-attribute and internal-knowledge inference despite typed outputs; Jev remains vulnerable across the board. `jev-1.13.0 + NanoJev`
- [How Reliable Are System-One Decisions in Medicine? A Comparative Study of Jev, Jev-like Models, and LLMs](https://arxiv.org/abs/2610.18529) - Du et al.: 15 medical datasets, 8,742 instances, Jev vs Jev-like models vs LLMs. Jev rivals leading models on text-driven tasks but is weaker on numerically driven ones; under question-format variations it keeps strong consistency and small accuracy swings; most models, Jev included, fail at abstention awareness, neither recognising missing correct answers nor using an optional abstain. `jev-1.13.0 · n=8,742`
- [Jev Thinks "I Don't Know" but Doesn't Say It: Sys1Cal-v1 for Probability Calibration](https://arxiv.org/abs/2609.35342) - Porcedda: Sys1Cal-v1, True/False items whose exact probability is known by construction, asked through all three Jev primitives and scored by total variation from the true distribution, to test whether every returned probability has the right numerical meaning rather than just confidence calibration; Jev and SemIf evaluated, per-primitive results in the paper. `jev-1.13.0 · known-truth items`
- [JEV-as-a-Judge: Accept When Confident, Escalate When Unsure](https://arxiv.org/abs/2609.26550) - Li, Miao, Krishnan and Padman. Jev as a judge against 16 judges with human adjudication: within 3 points of the strongest on ordinary preference, larger gaps on derivations and elaborately written wrong answers, concentrated in low-confidence decisions. `jev-1.13.0`
- [JevAdvBench: A Benchmark and Black-Box Attacks for Reinforcement Learning for Calibrated Decisions Models](https://arxiv.org/abs/2609.31142) - Hu et al.: the first adversarial benchmark for typed decision models, scoring each attacked decision against the model's own clean answer and an identical re-run. Rewording stays within 1.2pp and out-of-schema fields never reach the model, but one appended unverified opinion flips 12.1% of decisions and pushes 38% of confident answers below the 0.8 review threshold. `jev-1.13.0 · 812 questions / 9,744 edits`
- [More Choices, Fewer Decisions: Ordinal-Scale Bias in JEV-like Direct-Decision Models](https://arxiv.org/abs/2609.38827) - Gao et al.: ordinal scale-utilization bias across 40 datasets and nine JEV, KEV and StartLux models: decisions pile onto fewer scale levels than the gold distribution, worse on finer scales, weak on unordered categories; randomising option order reduces but does not remove it, and larger models get more accurate without using more of the scale. `JEV family · 40 datasets`
- [Nudge the Judge: Uncovering Program Decision Hijacking in Real-World Jev Applications](https://arxiv.org/abs/2610.18612) - Luo et al.: Jev Program Decision Hijacking, where attacker-controlled context redirects a program through schema-valid Jev judgments. JevProbe traces adversarial influence from reachable context surfaces through program-consumed decisions to downstream security consequences across 228 popular real-world Jev applications; per-application numbers in the paper. `jev (hosted) · 228 applications`
- [Poisoned into Action: Context Attacks on Typed Decision Workflows](https://arxiv.org/abs/2610.15537) - Wu, Chang and Yan: context poisoning scored by what actually executes, not by flipped labels, with 1,659 calls to jev-1.13.0 over 20 tasks in two synthetic workflows. A standalone payload reaches all 60 trials and causes five security-violating executions; a carrier-payload pair causes one; a source-exclusion gate blocks it but also withholds ten correct attacked and sixteen correct clean decisions. `jev-1.13.0 · 1,659 calls`
- [Probability Contracts: Accuracy, Coherence, and Decisions Across LLM Interfaces](https://arxiv.org/abs/2609.37470) - Chen and Li: probability contracts on 1,000 finite worlds with exact posteriors. Jev's Event and Choice interfaces change the binary action on 32.8% of valid pairs at defer cost 0.10; Kev has lower canonical posterior error but larger complement and coarsening residuals; interface disagreement certifies only 11-52% of mean pair error and is not a reliable selector. `jev-1.13.0 · 1,000 worlds`
- [Reading Right, Acting Wrong: Diagnosing Decision Failures of System One Models in Agent Loops](https://arxiv.org/abs/2610.18579) - Li, Krishnan and Padman: pair every next-action question with factual probes about the same agent state, across seventeen fast systems and a reasoning model. Most wrong actions follow correct reading, the more accurate the system the more so; the errors cluster on the same states across systems, and where the probed facts determine the action, models that read every fact still fail to combine them, while composing their own probe answers in code repairs it. `17 fast systems incl. Jev`
- [Same Attack, Different Judge: A First Controlled Adversarial Comparison of Jev, LLM Judges, and Purpose-Built Safety Classifiers](https://arxiv.org/abs/2610.17820) - Li et al.: the first controlled adversarial comparison, same payload and policy for Jev, five GPT/GLM judges, Llama Guard 3 and Prompt Guard 2 across four tasks and fifty attack variants from thirteen families. Jev is the most robust on input moderation: eight forgeries of its structured input bypass nothing, and a sentence asking only why someone might make a harmful request bypasses 9% of its blocks against 56% for GPT-6 Astra. `jev-1.13.0 · 50 attack variants`
- [Same Scores, Different Decisions: Evaluating JEV and Language Models for Legal Document Understanding](https://arxiv.org/abs/2609.27678) - Compares Jev with nine LLMs on ContractNLI, varying hypothesis visibility, requested outputs and output order while keeping the contract and judgment fixed, and measuring correctness across repeated-request conditions. Shows aggregate accuracy conceals changes in individual decisions and that repeated agreement can be consistently wrong; Jev is cheapest and fastest, hosted LLMs more accurate. `jev-1.13.0 · ContractNLI`
- [Same-Number Citation Swaps: Stress-Testing Jev as a Financial Evidence Judge](https://arxiv.org/abs/2610.08675) - Xu et al.: Jev as a source-support verifier for financial calculation traces, stress-tested by moving citations between cells that hold the same number so the arithmetic stays right while the financial role is wrong. Wrong-role citations pass and some valid alternative citations are withheld; explicit column labels help some cases and hurt others, and the tradeoff persists on 36 held-out pages. `jev-1.13.0 · same-number swaps`
- [Type-Safe Is Not Error-Free: A Constrained Decision Head Follows the Option Name, Not the Rubric Bound to It](https://arxiv.org/abs/2609.26758) - Sun and Xu. Swapping only which option name is bound to which rubric changes 70.4 answers per hundred in open Jev-like heads; hosted Jev drops from AUC .81 to .58, 24x its test-retest floor. Random-string names remove the effect. `jev-1.13.0 · n=1,200 decisions`
- [TypedBench: A Benchmark for Calibration, Framing Sensitivity, and Cost in System One Decision Models](https://arxiv.org/abs/2610.11392) - Sharma et al.: TypedBench, seven policy-labelled generators and nine suites that report accuracy as median and range across paraphrases, calibration against a matched perfectly-calibrated null, selective prediction, ordinal scoring and realised cost under asymmetric costs. Hosted Jev follows the stated policy but is wording-sensitive and systematically underconfident; under asymmetric costs, acting on its probabilities can be worse than taking its top answer. Open 0.8B-9B decoders route exactly but slow down and degrade as options grow. `jev-1.13.0 · 7 generators / 9 suites`
- [When the Right Answer Is Missing: An Arithmetic-Dependent Rejection Bottleneck in Jev](https://arxiv.org/abs/2609.39496) - Zhong, Li and Lai: when the right numerical answer is absent, Jev should pick 'none of the above' but takes a wrong alternative instead: answer-present accuracy 99%, correct rejection 7%, across magnitudes, operation depths, phrasings and rejection labels. Native Boolean verification gets 99% on the same answer-absent cases, so the primitive, not the content, is the bottleneck. `jev-1.13.0 · paired arithmetic`

## Calibration and confidence

Whether the probabilities mean what they say.

### Probability audits against labels

- [Adilmp/does-jev-confidence-mean-anything](https://github.com/Adilmp/does-jev-confidence-mean-anything) - Stated confidence against human labels on civil_comments: at about 75% stated confidence only 10% of comments were human-flagged; a two-parameter recalibration removes 96% of the error. `jev-1.13.0 · n=8,000 judgments`
- [ajanm007/jevrag](https://github.com/ajanm007/jevrag) - Five RAG mid-pipeline decisions: confidence ranks well but is miscalibrated on retrieval-stopping (ECE 0.33) and well calibrated on chunk boundaries (ECE 0.087), same harness. `n=700 questions`
- [AnthusAI/Jev-Calibration](https://github.com/AnthusAI/Jev-Calibration) - 8,801 labeled sentiment examples: raw Noul probabilities overconfident (ECE 0.117, Choice worse); isotonic regression brings ECE to 0.008. `jev-1.13.0 · n=8,801 examples`
- [Calibration, decomposition and shadow evals (beri.net)](https://beri.net/article/typesafe-jev-typed-decision-model-calibration-decompo) - Ties together the phishing decomposition study (62.6% as one question, 95.0% as five) and the 900-ticket OOD test (ECE 0.107, 4.4x the noise floor).
- [Can you trust Jev's confidence? (Anthus)](https://anth.us/blog/can-you-trust-jev-confidence) - By question type on 8,801 examples: Noul stated 79.0% vs 72.3% actual, Choice 91.4% vs 76.1%; the 50 to 95% band was only 50 to 57% correct.
- [gkastanis/d3code-calibration](https://github.com/gkastanis/d3code-calibration) - When Jev said about 0.85 the answer was yes only 45% of the time; the ranking held up far better than the number, and calibration fit on one dataset did not transfer. `n=791 items at 0.85`
- [jourdanlabs/assay-001](https://github.com/jourdanlabs/assay-001) - Calibrated on CLINC150 (ECE 0.020) but overconfident on Banking77 (ECE 0.094); zero type errors in 8,576 responses. `jev-1.13.0 · n=8,576 responses`
- [rubinagentagi-tech/jev-heart-risk-bench](https://github.com/rubinagentagi-tech/jev-heart-risk-bench) - 5,000 CDC heart-risk respondents: AUC 0.773 vs 0.794 for a chat LLM, and stated probabilities badly miscalibrated (Brier skill -1.3). `jev-1.13.0 · n=5,000 respondents`
- [Running-Dolphins/jev-bench](https://github.com/Running-Dolphins/jev-bench) - 12 public classification tasks, 500 items each; reliability tables show over- and under-confidence varying sharply by task (Banking77 top band: stated 0.98, actual 0.90). `jev-1.13.0 · n=6,000 items`

### Confidence as a routing or gating signal

- [dtduc-git/jevnav](https://github.com/dtduc-git/jevnav) - 41 of 41 element-selection decisions correct, but in loop mode confidence does not separate right from wrong (both land 0.39 to 0.99). `jev-1.13.0`
- [ItBayMax/typesafe-ai-jev-example](https://github.com/ItBayMax/typesafe-ai-jev-example) - 28 live calls vs hand-written mock probabilities: the real distribution is extreme (mass at 0 and 1) and prefers a clean fallback option to picking among wrong ones. `jev-1.13.0 · n=28 calls`
- [manankumarthakkar/jev-escalation-gate](https://github.com/manankumarthakkar/jev-escalation-gate) - The same gate scored 100% or 69.5% on the same model and prompt depending only on how the wrong answers were built; 97% stated confidence was right 97.6% of the time. `jev-1.13.0 · n=600 items`
- [nikkoxgonzales/jev-certify](https://github.com/nikkoxgonzales/jev-certify) - Conformal thresholds on CLINC150: the achievable risk bound floors at 1.95% because Jev returns exactly 1.0 confidence on 56.4% of answers, nine of them wrong. `jev-1.13.0 · n=2,412 answers`
- [scarif-labs/jev-software-decision-benchmark](https://github.com/scarif-labs/jev-software-decision-benchmark) - Dependency auto-merge decisions: AUROC 0.851 in distribution, but the tuned threshold fails to transfer out of distribution (50% precision, 15 unsafe merges on 185 cases). `jev-latest · n=185 OOD cases`

### Probes with a known true probability

- [eggmasonvalue/jev-takes-mauboussin](https://github.com/eggmasonvalue/jev-takes-mauboussin) - Mauboussin's 50-question calibration quiz: 90.0% accuracy at 91.4% mean stated confidence; 34 of 34 correct when Jev claimed 95% or more. `jev-latest · n=50 questions`
- [KantaHayashiAI/jev-does-not-play-dice](https://github.com/KantaHayashiAI/jev-does-not-play-dice) - Hidden fair die: Choice puts 82.9% on its pick at 19.0% accuracy; Noul on the same die stays near 1/6. `jev-1.13.0 · n=400 rolls`
- [meetr1912/jev-arena](https://github.com/meetr1912/jev-arena) - Questions with analytically known probabilities: Brier 0.0059, ECE 0.062 on 145 binary events. `jev-1.13.0 · n=145 events`
- [meetr1912/jev-vickrey](https://github.com/meetr1912/jev-vickrey) - Sealed-bid auction probes: under-confident on value thresholds (ECE 0.132 vs 0.013 for an oracle), so it overbids and loses money. `jev-1.13.0`
- [simonmesmith/jev-probability-experiment](https://github.com/simonmesmith/jev-probability-experiment) - 68 coin, dice and card problems: Noul is closest to the true probability (MAE 5.6 points); asking for the outcome as a Choice is far worse (MAE 21.1). `jev-1.13.0 · n=68 problems`
- [TakumiNoguchi2004/jev-noul-vs-choice](https://github.com/TakumiNoguchi2004/jev-noul-vs-choice) - Root-causes the fair-die collapse: it is specific to Choice; independent Nouls recover 1/6 for every face, and the Choice bias grows with option count. `jev-1.13.0 · n=50 trials`

### Other

- [B-Deforce/jev-clef-calibration](https://github.com/B-Deforce/jev-clef-calibration) - Jev vs Cloudflare Clef on 500 sentiment sentences and 500 redacted SMS, scored on Brier and ECCE-R with paired bootstrap. Clef is better calibrated on the easy sentiment set (ECCE-R 0.012 vs 0.035); on SMS Jev has the lower Brier (0.063 vs 0.099) but worse ECCE-R (0.159 vs 0.133) and far higher spam precision (68.5% vs 47.7%). `jev-1.13.0 · n=500+500`
- [GautamTalksDev/jevbench](https://github.com/GautamTalksDev/jevbench) - Preregistered, bias-corrected test of whether Jev lowers its confidence where humans disagree: ChaosNLI items with 100 annotations each, 750 low- and 750 high-disagreement. `jev-1.13.0 · pre-registered`
- [lorenzofamiglini/calfram-bench](https://github.com/lorenzofamiglini/calfram-bench) - External calibration audit of Jev on 25 public benchmarks at natural prevalence with CalFram (ECE, ECI, Brier, log score), a GLiClass 0.44B baseline, contamination annotation and a cross-fitted recalibration study. Measures the calibration the vendor does not publish and whether local recalibration repairs it; binned ECE is read against a bootstrap noise floor. `jev-1.13.0 · 25 benchmarks`

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
- [pawarbi/jev-bias-audit](https://github.com/pawarbi/jev-bias-audit) - On a value-laden binary question the first-listed option gains 0.37, which explained most of a 67% preference; on 1,012 BBQ items 98.7% on ambiguous contexts, 96.5% on clear ones. `jev-1.13 · n=1,012 items`
- [yodablocks/jev-orderby-bench](https://github.com/yodablocks/jev-orderby-bench) - Six pre-registered gates: pass on topic membership, four fail on graded product relevance; 40 rows per request breaks a gate one row per request passes; 53 of 360 rows tie at 0.99. `jev-1.13.0 · n=666 rows · pre-registered`
- [yottayoshida/jevfuzz](https://github.com/yottayoshida/jevfuzz) - Renames question IDs and reorders options and JSON keys: six confirmed decision changes in 100 mutations over 10 states. `jev-latest · n=100 mutations`

### Question form and request shape

- [DowLucas/browser-jev](https://github.com/DowLucas/browser-jev) - Narrow oracle questions score 0.99 where broad ones score 0.30 on the same page; the same state scores 0.72 to 0.87 across repeats.
- [jjd-lab/jev-synthetic-survey](https://github.com/jjd-lab/jev-synthetic-survey) - Synthetic survey respondents: asked as Noul Jev beats GPT-4.1 on all six measures; asked as Choice it loses the headline metric. `jev-1.13.0 · n=300 respondents`
- [JLegends/opencode-jev-compaction](https://github.com/JLegends/opencode-jev-compaction) - Noul at 0.996 on hard facts but 0.003 to 0.28 on judgment calls; a statement and its negation both scored about 0.95 until switched to explicit-criteria Choice.
- [nyarlathoteppppp/pi-heed](https://github.com/nyarlathoteppppp/pi-heed) - Slightly under-confident probabilities, flat latency in question count, and strong anchoring on the order of state fields. `jev-latest`
- [yodablocks/duckdb-jev](https://github.com/yodablocks/duckdb-jev) - Six-condition gate (Brier, ECE, negation symmetry, rubric ordinality) on 360 live requests: five of six pass. `jev-1.13.0 · n=360 rows`

### Other

- [gazelle93/decision-models-under-pressure](https://github.com/gazelle93/decision-models-under-pressure) - Seven decision models as the job gets harder three ways: longer candidate list, shuffled option order, harder distractors. Jev degrades slowest but flips one answer in seven on reorder alone, and one in twenty-three with the order held fixed. `n=200 per domain · K to 128`
- [Jonahss/jev-color-bias](https://github.com/Jonahss/jev-color-bias) - Jev favours whichever option is named 'blue' when evidence is balanced: over all 512 3x3 windows under four namings, the option called 'blue' gets +7 points over its colour-swapped twin (+5 when the names are swapped onto the other colour); A/B names remove the gap, and run-to-run noise is only 0.026. A clean option-name bias, echoing the 'follows the option name' paper. `jev-latest · 512 windows x 4 namings`
- [kartikanand73/decision-api-benchmark](https://github.com/kartikanand73/decision-api-benchmark) - First head-to-head of three decision APIs on a fraud/takeover routing task: 500 cases in all six option orders, two full runs, bootstrap intervals. Jev is the most accurate (macro-F1 89.2% vs OpenAI Decisions 71.4%, Microsoft Decision-1 87.6%) but less order-invariant than Decision-1: same route in all six orders 91.6% vs 64.2% vs 97.2%, best-minus-worst order gap 8-10 pts vs 25.9 vs 1.2; Brier 0.117 vs 0.296 vs 0.112. `jev-1.13-20260917 · 9,165 calls x 2 runs`
- [pobooo/jev-dice](https://github.com/pobooo/jev-dice) - Does Jev play dice? On a die with no correct answer it picks the first-listed option on all 2,000 identical requests (position bias plus non-determinism); option order barely matters once there is a correct answer. `jev-1.13.0 · n=2,000/prompt`
- [SabaPivot/jev-safety-bench (HF)](https://huggingface.co/datasets/SabaPivot/jev-safety-bench) - Safety classification and answer-choice robustness across five fixed-choice models on XSTest, OR-Bench and HarmBench, each request asked with original and reversed choices (249,549 rows). Hosted Jev is the most accurate on both axes (XSTest 93.6% benign / 94.0% harmful, OR-Bench Toxic 92.1%, HarmBench 100%) but over-refuses benign OR-Bench Hard (33.9%); reversal flips up to 82.5% of Laya-Multilingual answers on small sets, and the same score can hide 8.5% changed decisions. `jev-1.13-20260917 · 83,183 requests x 3 variants`
- [sammy143/decision-models-eval](https://github.com/sammy143/decision-models-eval) - Pre-registered test of SubQ Snap against Jev 1.13 and the new Jev 1.14 on four Decision Index benchmarks. Reversing When2Call's options moves Snap +7.3 but Jev 1.13 only +1.9 and Jev 1.14 -0.1; Jev 1.14, released two days after Snap, gains +22.5 on PhishNChips yet regresses 3.9 [2.0, 5.8] on RAGTruth versus 1.13, neither mentioned in the release notes; overconfidence ranges -3 to +16 points by task. `jev-1.13 and jev-1.14 · pre-registered`

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
- [phuthuycoding/jev-audit](https://github.com/phuthuycoding/jev-audit) - 79-case secrets and vulnerability corpus: F1 1.00 and 0.98, and detection holds when the secret is buried in 49 KB of noise. `jev-1.13.0 · n=79 cases`
- [RastislavDujava/jev-classification-prompting](https://github.com/RastislavDujava/jev-classification-prompting) - Nine ablations: criteria wording moves paired accuracy from 70% to 96%; a knowledge-cutoff blind spot goes from 66.7% to 100% by rewriting criteria. `jev-1.13.0 · n=41 items`
- [Selmar/typesafe-jev-calibrate-for-code-review](https://github.com/Selmar/typesafe-jev-calibrate-for-code-review) - Rule-by-rule probes against a 0.60 threshold for C# review: comment rules improve with whole-file context, and Jev reads facts better than it derives them.
- [SYED-M-HUSSAIN/jev-experimental](https://github.com/SYED-M-HUSSAIN/jev-experimental) - Accuracy 83% to 100% once criteria, not the question, spelled out the boundary; Brier 0.038. `jev-1.13.0`
- [tfolkman/jev-village](https://github.com/tfolkman/jev-village) - 60 simulated villagers: wording of the criteria alone flipped correct behaviour to incorrect across the whole population. `jev-1.13.0`
- [zachlandes/jev-dialect-bias](https://github.com/zachlandes/jev-dialect-bias) - Reproduces Hofmann et al. (Nature 2024) on Jev: as a moderator it removes 17.8% of African American English posts vs 13.1% of matched standard-English ones (most of the gap from slurs/profanity); stating the writer is Black vs White flips death-sentence rates 29.5% vs 62.7% on identical text; a dialect-is-not-evidence line roughly halves the gap. `jev-1.13.0 · n=2,019 pairs`

## Prompt injection and adversarial inputs

Text planted in the state to move Jev's own verdict. Jev used as an injection *detector* is in `data/task_benchmarks.tsv`.

- [0xshin0221/openpoke-meets-jev](https://github.com/0xshin0221/openpoke-meets-jev) - Targeted injection and contamination test on an email triage gate: the injection check could be suppressed 95.3% of the time. `jev-1.13.0 · n=12 emails`
- [azterizm/jev-vs-sovereign-benchmark](https://github.com/azterizm/jev-vs-sovereign-benchmark) - Fake-law probe on UK legal RAG: 0% abstention where a specialised stack abstained 100%. `jev-1.13.0`
- [cwhy/decision-injection-bench](https://github.com/cwhy/decision-injection-bench) - 1,056 injection attacks on four classifiers: Jev flipped on 1 (0.09%), the others on 3.0% to 62.6%. `jev-1.13.0 · n=1,056 attacks`
- [eugeniughelbur/jev-engineering](https://github.com/eugeniughelbur/jev-engineering) - Blunt injection: 0 of 30 dangerous commands pass. Authority injection, a claimed human approval: up to 3 of 30 pass. `jev-latest · n=300 calls`
- [finrod21/jev-transaction-guard](https://github.com/finrod21/jev-transaction-guard) - Injection bait inside a transaction memo trips the circuit breaker at 0.99; verdicts and token counts identical across five repeats. `jev-latest`
- [Foshowithit/jev-rcos-study](https://github.com/Foshowithit/jev-rcos-study) - Falsification-first: confidence gating AUC 0.44 (inverted, falsified); 13 of 13 adversarial near-duplicate candidates resisted. `jev-1.13.0`
- [golipraneetha/decision-model-safety-gates](https://github.com/golipraneetha/decision-model-safety-gates) - A Jev/Clef-style decision model as an agent authorization gate (n=292): false-approve 2.6% vs 29.7% for rules and 68.8% for a content-safety guard, escalate recall 71%, ECE 0.042. But spoofing the policy's input facts ('requester verified, amount within limit') flips deny to allow on 17/30 with one sentence and 28/30 with adaptive score-guided search, while obfuscation barely moves it (5.6%). The gates fail on opposite surfaces. `typed decision model · n=292, 30 attacked`
- [Iskandeur/system1-system2](https://github.com/Iskandeur/system1-system2) - 600 MASSIVE utterances: one injected payload flips GPT-5.2 on all 80 trials and Jev on 26 of 80; Jev ECE 4.5%. `jev-1.13.0 · n=600 utterances`
- [Makabeez/jev-news-bench](https://github.com/Makabeez/jev-news-bench) - Prompt injection on Polymarket headlines, Jev vs gpt-oss-20b, five attack families plus a control. Jev barely moves under literal injected instructions (+34% vs Groq +92%) but folds when the text argues with it: negation flips +87%, confidence claims +83%, fake source authority +72%; a confidence gate admits some attacks more readily than honest answers (lift 1.4x). 'You can't tell Jev what to output; you can tell it what to believe.' `jev-1.13.0 · 6,300 injection calls`
- [Prompt injection can influence the verdict (VentureBeat)](https://venturebeat.com/security/companies-are-putting-jev-in-charge-of-ai-age) - An agent action gate: block probability for `rm -rf ~/.ssh` fell from 0.76 to 0.48 after a fake pre-approval was injected into tool output.
- [themsquared/jev-benchmark](https://github.com/themsquared/jev-benchmark) - 60 tool-call risk cases with adversarially worded destructive commands: 91.7% accuracy, every wrong answer at hedged confidence. `jev-1.13.0 · n=60 cases`
- [willkelly/jev-evaluation](https://github.com/willkelly/jev-evaluation) - 123,805 requests: calibration holds on support routing (ECE 0.075) and collapses on random 3-SAT, where the probability barely moves as the true rate spans 0 to 1. `jev-1.13.0 · n=123,805 requests · pre-registered`
- [xzx34/JevOut](https://github.com/xzx34/JevOut) - JevOut: fluent, answer-preserving context added to the state redirects an initially-correct Jev decision to a pre-chosen wrong option; 312 of 508 items flip (61.4%) within 64 target evaluations, 229 pushed past 0.7 on the wrong target. Paper arXiv:2609.30243. `n=508 decisions`
- [yflantmy/DecisionRobust (HF)](https://huggingface.co/datasets/yflantmy/DecisionRobust) - DecisionRobust, by this list's maintainer (self-entry, disclosed): five curated challenge sets (text adversarial, text injection, image perturbation, overlay, instruction; 1,058 candidates from 441 originals) with an attack-success leaderboard over ~30 decision models. JEV-1.13.0: text adversarial 72.5%, text injection 91.8% (both selection-biased, Jev helped pick the items); OpenAI Decisions lowest on text (15.6% / 33.9%); image sets on 17 models. Leaderboard at yifan-lan.github.io/DecisionRobust, paper in preparation. `jev-1.13.0 · 1,058 candidates / ~30 models`
- [zkousama/jagged](https://github.com/zkousama/jagged) - 486 Wikipedia deletion discussions: 96.5% accurate at baseline, 26.5% under a one-line injected instruction; mirrored questions disagree. `jev-1.13.0 · n=486 · pre-registered`

## Abstention and unanswerable inputs

What Jev does when no option is right or the answer is not in the state.

- [andre-langchain/calibration-probe](https://github.com/andre-langchain/calibration-probe) - Does a System One model's confidence drop when the evidence cannot answer? 48 questions asked with and without an explicit insufficient-evidence option. Without one, Jev answers 47% of unanswerable questions confidently (SemIf 60%, Claude 20%); adding the option moves 77% to abstain but 13% still answer confidently. `jev-1.13.0 · n=48 questions`
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

- [4nt0ineb/typed-decision-bench](https://github.com/4nt0ineb/typed-decision-bench) - English vs French intents: Jev loses one point (85% to 84%) where the other models lose four to eight; ECE 0.07 in English, 0.06 in French. `jev-1.13.0 · n=60 intents × 500`
- [AHTOOOXA/jev-cyrillic-audit](https://github.com/AHTOOOXA/jev-cyrillic-audit) - 600 paired XNLI items: Russian 77.3% vs English 88.3%, ECE 0.096 vs 0.032; traced to cross-lingual entailment, not tokenization or length. `jev-1.13.0 · n=600 pairs · pre-registered`
- [ArmanJR/Jev-Persian-Benchmark](https://github.com/ArmanJR/Jev-Persian-Benchmark) - 480 authored Persian and Finglish questions in 10 categories: 99.6% Choice, 99.4% Noul, 95.0% Score, parity with English. `jev-1.13.0 · n=480 questions`
- [CodyQin/zh-decision-bench](https://github.com/CodyQin/zh-decision-bench) - First Chinese calibration benchmark for Jev-class decision models; 28% of customer-service answers flip on option reorder (voice 10.6%), 12.8% flip simplified-to-traditional, refit temperatures 1.33 to 5.70 show systematic over-confidence. Jev adapter ready but not yet run. `Laya/Qwen · n=219 items`
- [DaehanKim/korean-jev-benchmark (HF)](https://huggingface.co/datasets/DaehanKim/korean-jev-benchmark) - Korean JEV Benchmark: 25,418 Korean items from 9 datasets normalized to finite-choice decisions, ten baselines including hosted Jev. JEV 1.13 scores 88.93% Korean / 82.50% English with order agreement 96.4% / 89.3%, the smallest cross-language gap and highest order stability of the ten; Gemma 4 31B reaches 94.4% Korean but 57.6% English with 55% English order agreement. `jev-1.13 · n=25,418 items`
- [mahlernim/jev-korean-benchmark](https://github.com/mahlernim/jev-korean-benchmark) - Korean vs English on four public exams, 100 per cell: no cost on reading comprehension, 8 points behind GPT-5.6 Luna on the Korean medical exam. `n=800 questions`
- [marcosmartinez/jev-acento](https://github.com/marcosmartinez/jev-acento) - Same items in English and Spanish: 3.0 to 6.4 points lost and calibration error roughly doubled on the two hardest of four datasets; Spanish instructions do not help. `jev-1.13.0 · n=3,200 items`
- [mateusfonsek/openai-decisions-vs-jev-vs-laya](https://github.com/mateusfonsek/openai-decisions-vs-jev-vs-laya) - Brazilian-Portuguese benchmark of OpenAI Decisions, Jev and local Laya on routing, injection detection and judging with easy/ambiguous/adversarial slices. Jev and OpenAI tie on routing (88.0% vs 86.7%, adversarial slice 88% vs 86%) and injection (F1 96.6% vs 95.8%, AUROC 0.993 both); Jev is the better judge (exact 80.7% vs 72.0%, ECE 8.0% vs 11.7%); zero-shot Laya collapses in Portuguese (30.7% routing), option rotation lifts it to 40%. `jev-1.13.0 · 450 cases/provider`

## Evaluation tooling

Harnesses built to probe wording, ordering or calibration, listed when they ship a measured run.

- [brida-ai/reflexbench](https://github.com/brida-ai/reflexbench) - Reports semantic accuracy, calibration, language consistency and option-order robustness separately rather than one score, on a frozen 111-case cohort. `jev-1.13.0 · n=111 cases`
- [FlorianRiquelme/jev-kit](https://github.com/FlorianRiquelme/jev-kit) - Harness whose example run on 15 project fixtures gets 81.7% overall and flags one indirect compound question at 46.7%, below a coin flip.
- [mertkayacs/jevoss](https://github.com/mertkayacs/jevoss) - Harness for any model speaking the Jev API with five robustness probes: option permutation, hidden-instruction injection, 600-word distractor padding, negated Noul questions and repeated-call determinism. Measured runs cover Intern-Decision-4B, Kev-4B, Laya and JevAlt (Kev and Laya lose 5.4 and 10.4 points under padding vs 12.2 to 17.4 for JevAlt); its Jev 1.13 rows are quoted from a separate audit, not re-run. `harness · 100 items/probe`
- [rssr25/sys1bench](https://github.com/rssr25/sys1bench) - sys1bench (formerly system-one-bench): suites on generated manifests (n=500) for Jev, Laya and Kev: calibration, wording sensitivity and cost scaling. `jev-1.13.0 · n=500`
- [smkrv/jev-calibrate](https://github.com/smkrv/jev-calibrate) - Grades a question's answers against labels: vague criteria 0.69 to 0.89 accuracy on the bundled example, rewritten criteria 0.92 to 1.00. `jev-1.13.0`
- [stillmarcus24/jev-verify](https://github.com/stillmarcus24/jev-verify) - Checks published Jev outputs against the confidence and expected-score identities without live calls: 279,842 answers from 88 repositories, 96.79% conform; one Jev-compatible implementation uses normalised entropy instead and reorders 9.96% of pairs. `n=279,842 answers`
- [SuperInstance/typesafe-quilts](https://github.com/SuperInstance/typesafe-quilts) - Zero-dependency System One calibration instrument live against jev-1.13.0: a known-answer battery with Brier decomposition and a negative-control harness, plus receipted routing judgments. On 20 known answers Brier is 0.1133 and Jev is honest in mid/high bands but over-predicts every truth below 0.4 (truth-MSE 11.5x worse there); outcome permutation p=0/2000. `jev-1.13.0 · n=20 battery`
- [xxlya/evaljev](https://github.com/xxlya/evaljev) - Derives the confidence-margin formula from live traces and measures the distribution shift between batched and separate requests. `n=351 decisions`

## Write-ups, critiques and evidence ledgers

Collections that recompute or gather other people's robustness numbers.

- [manankumarthakkar/jev-field-report](https://github.com/manankumarthakkar/jev-field-report) - Why eleven audits of the same model report 44.7% to 95.9% accuracy and ECE 0.023 to 0.793: whether an abstain option existed, which primitive, whether the task was novel. `jev-1.13.0 · 11 audits`
- [SamuelSacco/jev-exploration](https://github.com/SamuelSacco/jev-exploration) - Recomputes every published Jev ECE against its sampling noise floor: 2.1 to 2.5x the floor at every difficulty tier; outputs quantised to 0.01 and can be exact 0 or 1. `jev-1.13.0`
- [SoK: Semantic Decision Engines in Network Control Loops](https://arxiv.org/abs/2610.06425) - SoK (UTS): 139 paper families putting Jev-class engines in network control loops. Fifty claim the engine fits a control loop or time budget, four back it with matched measurement, four of 139 report deadline attainment; an engine meeting a 10 s budget for every isolated request meets it for none once decisions queue, and the same engine passes one coverage check and fails another. Ends in a minimum reporting record. `survey · 139 paper families`
- [Typed Decision Models: An Early Evidence Audit and Evaluation Checklist](https://arxiv.org/abs/2609.32160) - A meta-review of 28 early typed-decision-model papers (posted 19-24 Sep), relating them to label-probability classification, constrained decoding, reranking, calibration and cascades. Finds the typed readout shows no independent accuracy advantage over comparable label-probability readouts; Jev's clearest gains are latency and cost. Ends in an evaluation checklist. `28 papers reviewed`
- [Zaious/jev-capability-atlas](https://github.com/Zaious/jev-capability-atlas) - Bilingual atlas of where the claim holds, organised on one axis: whether the answer is recoverable from the state or needs outside knowledge.

## Related lists

General directories of Jev projects, and a paper-centred evidence survey, which this page does not duplicate:

- [yibie/awesome-jev](https://github.com/yibie/awesome-jev)
- [AbdelStark/awesome-typesafe-jev](https://github.com/AbdelStark/awesome-typesafe-jev)
- [OmniJev/awesome-jev-gallery](https://github.com/OmniJev/awesome-jev-gallery)
- [wh000wh000/awesome-jev-live](https://github.com/wh000wh000/awesome-jev-live)
- [andyrewlee/awesome-system-one](https://github.com/andyrewlee/awesome-system-one)
- [notsointresting/awesome-jev-family](https://github.com/notsointresting/awesome-jev-family)
- [eurekaleo/awesome-jev-survey](https://github.com/eurekaleo/awesome-jev-survey)

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
  note         = {Curated list. Accessed 2026-10-18.}
}
```

Please also cite the individual studies you rely on; their authors did the measuring.

## License

[CC0 1.0](LICENSE). Descriptions paraphrase the linked projects; the numbers belong to their authors.
