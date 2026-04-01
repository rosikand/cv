[← back](../index.html){.back}

# Desirable Ingredients of an RLVR algorithm

::: {.date}
April 1, 2026
:::


When thinking about designing a new RLVR algorithm to learn from verifiable rewards, there are certain properties and ingredients that we would ideally want to have satisfied. In this particular write-up, we list those ingredients. 



## 1. Dense Reward Signal

**The problem:** Binary pass/fail is maximally sparse. A solution that fails one edge case gets the same reward as total nonsense. When all rollouts pass or all fail, the gradient is exactly zero.


## 2. On-Policy Training

- **The problem:** Off-policy methods (SFT, offline distillation) suffer from exposure bias — the student encounters different partial sequences at inference than it saw during training, causing compounding errors.
- **What you want:** The policy trains on its own generations, so the training distribution matches the deployment distribution by construction.
- **Why it matters:** On-policy training eliminates distributional drift and prevents the policy from learning to "evade" a static reward signal. It's why GRPO and PPO outperform offline alternatives for reasoning.

## 3. Credit Assignment Beyond Sequence-Level

- **The problem:** In GRPO, every token in a response shares the same advantage. The model can't distinguish which reasoning step caused success or failure.
- **What you want:** Token-level or step-level credit assignment that tells the model *where* in the chain of thought things went right or wrong.
- **The tradeoff:** Finer granularity = noisier supervision. Sequence-level is clean but coarse; token-level is dense but requires careful regularization.

## 4. Resistance to Reward Hacking

- **The problem:** Any learned or proxy reward is a target for Goodhart's law. The policy will find and exploit the gap between what the reward measures and what you actually want.
- **What you want:** Either (a) a reward that co-trains with the policy so it can't be statically exploited, or (b) diagnostic signals that let you *detect* overoptimization before it corrupts the policy.

## 5. Exploration / Support Expansion

- **The problem:** The "Invisible Leash" (Wu et al., 2026). Standard RLVR is a support-constrained optimizer — it concentrates mass on solutions the base model already knows, but rarely discovers new reasoning paths. Support shrinkage consistently outweighs expansion. Pass@1 goes up, but pass@k goes *down*.
- **What you want:** Mechanisms that seed probability mass into underrepresented solution regions, not just sharpen existing modes.
- **The key insight:** Pure on-policy policy gradient cannot escape the support ceiling at the *sequence* level. 

## 6. No (or Minimal) External Dependencies

- **The problem:** External teachers, external reward models, and human-labeled process supervision are expensive, introduce distributional gaps, and create static targets.
- **What you want:** A self-contained training loop where the policy generates, evaluates, and improves using only the compute and signals already inside the pipeline. Zero inference-cost overhead at deployment.
- **The gold standard:** The reward mechanism is discarded at deployment. The model reduces to a standard causal LM with no auxiliary heads, no teacher, no external judge.

## 7. Efficient Use of Environment Feedback

- **The problem:** RLVR environments (code execution, tool use, agentic tasks) produce rich structured feedback — stack traces, error messages, partial outputs, execution states. All of this is discarded after computing binary pass/fail.
- **What you want:** A mechanism to ingest and learn from that structured feedback. The ingredients for dense reward already exist inside the training loop — you just need a way to use them.
- **Why this is underexplored:** Most RLVR work treats the environment as a black box that emits {0, 1}. But execution feedback is *objective*, *external*, and *free* — the ideal grounding signal for a reward model.


## 8. Generalization Beyond Verifiable Domains

- **The problem:** RLVR only works where you have a deterministic verifier. Math and code have one. Creative writing, open-ended reasoning, and most real-world tasks don't.
