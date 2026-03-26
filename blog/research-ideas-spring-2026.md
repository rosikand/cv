---
bibliography: references.bib
csl: https://www.zotero.org/styles/ieee
---

[← back](../index.html){.back}

# Research Ideas and Problems -- Spring 2026 

::: {.date}
March 2026
:::

\> *To be updated...* 

*Note: see my (informal) [research notebook](https://rosikand.github.io/research-notebook/) for more*. 

My research sits at the intersection of reinforcement learning, large language models, and robot learning. Below is a more detailed account of the specific directions I am pursuing and the questions that drive my work.

## RL Post-Training for Language Models

A central theme of my research is understanding how reinforcement learning can be used *after* supervised pre-training to unlock new capabilities in large language models. Recent work has shown that RL-based post-training---such as RLHF [@ouyang2022training] and RLVR [@deepseekmath2024]---can substantially improve reasoning, instruction following, and alignment. I am particularly interested in:

- **Self-distillation and self-play**: can models improve by training on their own outputs, bootstrapping capability without new human data? This connects to ideas from expert iteration [@anthony2017thinking] and self-play in games [@silver2017mastering].
- **Reward modeling and reward hacking**: how do we design reward signals that remain faithful proxies for human intent as models become more capable? The problem of reward hacking [@skalse2022defining] is a key bottleneck.
- **Continual RL post-training**: rather than a single RL fine-tuning phase, can we develop curricula that iteratively refine models over time, akin to continual learning [@kirkpatrick2017overcoming]?

## Robot Learning and Visuomotor Policies

The second pillar of my work concerns learning sensorimotor policies for robotic manipulation. Foundation models pre-trained on internet-scale data provide rich representations, but bridging the gap to physical action remains an open challenge. I am exploring:

- **Vision-language-action models (VLAs)**: architectures that map visual observations and language instructions directly to robot actions [@brohan2023rt2], and how to fine-tune them efficiently for new tasks.
- **On-policy RL for robotics**: methods like PPO [@schulman2017proximal] and GRPO [@deepseekmath2024] applied in simulation and on real hardware, with a focus on sample efficiency and sim-to-real transfer.
- **World models and planning**: learning predictive models of environment dynamics [@hafner2023mastering] that enable model-based planning and reduce the need for costly real-world interaction.

## Unifying Theme

RL is the connective tissue across these interests. Whether the "backbone" is a language model or a visuomotor policy, the core algorithmic questions are shared: how to define good reward signals, how to explore efficiently, and how to leverage pre-trained representations for downstream adaptation. I believe progress on these fronts will be mutually reinforcing---advances in RL post-training for LLMs will inform robot learning, and vice versa.

## References

::: {#refs}
:::

---

# More 

# Research Ideas and Problems – Spring 2026

*March 2026 · Ro Sikand · Stanford CS*

---

This post is a snapshot of what I'm thinking about right now — the problems that keep me up at night, the ideas I'm actively building toward, and the open questions I don't yet have clean answers to. Some of this is close to submission-ready; some of it is barely past the napkin-sketch stage. I'm writing this partly to organize my own thinking and partly because I've found that putting half-formed ideas in public tends to attract exactly the right collaborators.

The throughline across everything below is a single question: **how do we get more out of reinforcement learning for language models without requiring stronger external supervision?** The current RLVR (Reinforcement Learning with Verifiable Rewards) paradigm works, but it's bottlenecked in ways that are becoming increasingly clear — and the solutions I find most exciting all involve making the policy itself do more of the work.

---

## 1. GLARE: Where It Stands

For the past several months, I've been developing **GLARE** (Grounded Latent Reward from the Environment), a method that attaches a lightweight reward head to the policy backbone during GRPO-style training. The idea is simple: when a code-generation model executes its rollouts against test cases, the environment returns rich feedback — stack traces, per-test pass/fail, runtime errors — that standard RLVR compresses into a single bit. GLARE recovers that discarded signal by having the policy re-encode each rollout *conditioned on the execution feedback* (a privileged-information teacher pass), then mapping the resulting hidden state through a small MLP to produce a continuous reward score.

The reward head is trained with a Bradley-Terry pairwise ranking loss across the rollout group, plus two auxiliary objectives: a calibration loss (ℒ_cal) that anchors predictions to the binary verifier, and an execution-prediction loss (ℒ_pred) that forces the backbone to learn code-to-execution structure even without feedback in context.

The CS 229 pilot (Qwen3-4B on MBPP+) showed that this resolves the advantage collapse problem — GRPO stalls on >50% of prompts at convergence due to zero-gradient groups, while GLARE stays below 5%. The gains come not from more parameters (the MLP head adds <0.1% of total parameter count) but from structurally eliminating the dead zones where GRPO wastes compute.

The NeurIPS 2026 submission is in active development: full runs with controlled baselines (GRPO, SDPO, GLARE across 3 seeds) on LiveCodeBench v6, with temporal cutoff for clean train/eval separation.

---

## 2. The Ideas I'm Most Excited About

### 2a. Non-Verifiable Domain Transfer (The Big Bet)

This is the idea I think matters most for the NeurIPS story, and the one I'm most nervous about.

The competitive landscape is crowded. SDPO (Hübotter et al.) is the closest concurrent work, and it's strong — it uses the same self-distillation intuition but implements it as token-level credit assignment through a retrospective teacher pass. Positioning GLARE as "SDPO but slightly better on code benchmarks" is a losing strategy. There are too many methods fighting over the same leaderboard points.

The real thesis is that GLARE can go where SDPO cannot. SDPO requires successful rollouts to distill from — the teacher re-evaluates *the existing rollout* conditioned on feedback. When there are no correct solutions in the group (which happens frequently on hard problems), SDPO's mechanism degenerates. GLARE's reward head, by contrast, still produces continuous scores grounded in execution feedback, providing learning signal even in all-fail groups.

But the deeper version of this argument is about non-verifiable domains. In creative writing, open-ended QA, or medical consultation, there is no binary verifier. The reward signal comes from LLM-as-judge evaluations or adversarial feedback. The question is whether GLARE's execution-grounded representations — trained to encode the causal relationship between code and its runtime behavior — carry *hack-resistant* evaluation capacity into domains where ground truth is unavailable. If we pre-train the reward head on code (where we can validate it) and then transfer to open-ended tasks (where we can't), does ℒ_cal's correlation with ground truth persist even when ground truth disappears?

This is the result SDPO literally cannot replicate, because SDPO has no mechanism that operates without successful rollouts. If I can show this transfer convincingly, that's the paper. If I can't — well, I need to figure that out before June.

### 2b. Auto-PRM: Sequence-Level to Token-Level

GLARE currently produces one reward score per rollout. But the reward head sits on a transformer backbone that processes the full sequence — there's nothing stopping us from extracting scores at every token position, creating a process reward model (PRM) from outcome-only supervision.

The idea: apply the Bradley-Terry loss not just at the final hidden state but at every prefix, using the execution outcome of the full rollout as the label for all its prefixes. This gives you O(G² × T) comparison pairs from the same rollouts you're already generating, where T is the average sequence length. Add TD-style regularization (adjacent prefix scores shouldn't jump wildly) and you get per-token advantage estimates that directly replace GRPO's flat per-sequence advantage.

This attacks the biggest bottleneck in RLVR. GRPO assigns the same scalar advantage to every token in a response — the opening `def` gets the same gradient signal as the critical `if` check on line 47. Token-level credit assignment via a co-trained head, without needing an external reward model or human annotations, would be a meaningful contribution even if the gains are modest.

The practical concern is compute. Prefix-level BT on long sequences generates a lot of comparisons. I'm thinking about subsampling strategies — score at newline-delimited "steps" rather than every token, and use the TD regularizer to interpolate.

### 2c. Reward Hacking Diagnostics (GLARE-RH)

This one fell out of an ablation and I think it's underappreciated.

Without ℒ_cal, the reward head's correlation with the binary verifier degrades from 0.82 to 0.54 over 200 training steps. The head learns to exploit the *feedback string* directly rather than the solution quality — a textbook reward hacking failure mode. With ℒ_cal, calibration stays above 0.78 throughout.

The interesting observation is that you can define a "hacking gap" Δ_hack = r_head − r_exec and track it as a time series during training. When the gap spikes, the head is being optimized faster than it's being grounded. This gives you a continuous, online diagnostic for reward overoptimization that doesn't require held-out evaluation. HERO (FAIR/Meta) recently showed reward scores climbing while accuracy collapses (their Figure 5) — exactly the failure mode Δ_hack is designed to detect.

The research program here is three experiments. First, the diagnostic itself: train GLARE past early stopping and show that Δ_hack predicts validation degradation before it happens. Second, intervention: implement threshold-triggered corrective steps (increase ℒ_cal weight, reset the head, or pause training) and show that they prevent the collapse. Third, transfer: show that Δ_hack remains a useful signal even in domains without ground-truth verifiers, demonstrating that execution-grounded representations carry hack-resistance.

---

## 3. The Open Questions (Things I Don't Know)

### 3a. Does the Invisible Leash Bind Dense Reward Methods Differently?

Wu et al.'s "Invisible Leash" paper makes a strong empirical case that RLVR operates as a support-constrained optimization mechanism — it sharpens the base model's distribution toward high-reward outputs but rarely discovers genuinely novel solution strategies. The support shrinks; pass@1 goes up but pass@k eventually degrades.

The proof is clean: any RLVR update satisfying the exponential tilting form πθ(y|x) ∝ q(y|x)exp(βR(x,y)) decreases entropy relative to the base distribution. The only escape is if R is constant on the support of q (in which case there's nothing to learn).

My question: does dense reward shaping (as in GLARE) change this picture? The Invisible Leash analysis assumes a scalar reward that applies uniformly to the entire rollout. When the reward is continuous and grounded in execution feedback, the effective reweighting landscape is smoother — you're not doing binary selection, you're applying a continuous gradient that should maintain probability mass on partial-credit solutions that pure RLVR zeros out.

I've been building an interactive blog post — tentatively titled "The Invisible Leash" — with a distribution sharpening playground that lets you visualize how different weight functions (REINFORCE, GRPO, MaxRL) transform the policy distribution. The first component is built. But the theoretical question of whether dense rewards can genuinely expand the support, rather than just sharpening more gracefully, remains open.

### 3b. Interference Across Problems

The IsoCompute Playbook (Pimpalkhute, Liang et al.) surfaces an important open challenge: in multi-problem RL training, progress on some tasks can interfere with learning on others. This population-level interference alters scaling behavior in ways that are difficult to predict.

I don't have a proposal here — just an observation that this matters a lot for practical RLVR and nobody has a clean solution. The IsoCompute authors suggest tracking changes in the pass@1 distribution over training as a diagnostic for interference, which feels related to GLARE's reward head calibration as a diagnostic for hacking. Both are trying to detect when the training process is going off the rails before it's too late. Whether these diagnostics can be unified into a single framework is a question I'd love to explore.

### 3c. Scaling Self-Distillation

SDPO's paper notes that performance depends on the model's in-context learning ability — SDPO primarily works for stronger base models and can underperform GRPO on weaker ones. GLARE's reward head is architecturally simpler (an MLP on hidden states rather than full-sequence re-evaluation), so it's plausible that it degrades less at small scale. But I don't have evidence for this yet.

The scaling experiments I'm planning — Qwen3-4B, 8B, 14B, and OLMo3-7B — should answer whether GLARE's gains compound with model size (as SDPO's do) or saturate. If they saturate, that tells us something about the information capacity of a 2-layer MLP reward head. If they compound, it suggests the backbone representations get richer faster than the head can exploit them, which would motivate a deeper head or attention-based pooling.

---

## 4. Broader Directions

A few things I'm interested in that don't fit neatly into the GLARE narrative:

**Scaling environment-time compute.** Most RLVR work treats the environment as a black box that returns pass/fail. But the execution environment is itself a computational resource. What if you gave the model more test cases, or richer feedback, or allowed iterative refinement against the environment? The analogy to test-time compute scaling is natural — we have inference-time scaling, why not environment-time scaling?

**Pure self-play RL for code.** Can a model improve at programming through self-play alone, without any human-curated problem set? Generate your own problems, generate solutions, execute them, learn from the outcomes. The bootstrapping problem is obvious (who grades the problem generator?) but the analogy to AlphaGo-style self-improvement is tantalizing.

**Connecting reward hacking to deception detection.** FAR.AI's Obfuscation Atlas work showed that RLVR degrades off-domain linear probes for deception detection while on-domain probes survive. GLARE's co-trained reward head should be more robust than a frozen probe because it co-evolves with the policy. This connects reward hacking resistance to AI safety in a way that I think deserves more attention.

---

## What I'm Looking For

I'm actively looking for research collaborations on RL post-training and reward modeling, compute partnerships for scaling GLARE experiments to 8B–14B models, and feedback on the ideas above. If any of this resonates — especially the non-verifiable domain transfer story or the Auto-PRM extension — I'd love to talk.

📧 rsikand@stanford.edu