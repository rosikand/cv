[← back](../index.html){.back}

# Open problems in RL post-training

::: {.date}
March 31, 2026
:::

- **The Invisible Leash.** RLVR is a support-constrained optimizer — it sharpens what the base model already knows but rarely discovers new reasoning paths; no principled mechanism provably expands sequence-level support under on-policy policy gradient.
- **The GRPO Dead Zone.** When all rollouts in a group pass or all fail, the advantage is zero and no gradient flows — GRPO wastes 50–60% of training compute on non-informative updates, precisely on the hardest and easiest problems where signal matters most.
- **Credit Assignment.** Every token in a rollout shares a single scalar advantage; the model cannot distinguish which reasoning step caused success or failure, and no existing approach (PRMs, token-level heads, gradient attribution) solves this end-to-end without external supervision or frozen targets.
- **Reward Hacking in Non-Verifiable Domains.** Even deterministic verifiers can be gamed via clipping bias and proxy exploitation; in domains without verifiers, LLM-as-judge produces policies that deceive the judge rather than improve, and no hack-resistant dense reward signal exists for open-ended tasks.
- **Scaling Laws for RLVR.** Compute-optimal allocation rules exist for vanilla GRPO (IsoCompute Playbook), but scaling laws for self-distillation methods, dense reward variants, and heterogeneous task mixtures are completely unstudied.
- **The Non-Verifiable Frontier.** RLVR requires a deterministic verifier; extending it to creative writing, open-ended reasoning, and real-world tasks without collapsing to "train a reward model and hope" is arguably the single hardest open problem in post-training.
- **Curriculum and Data Mixing.** Training dynamics differ qualitatively between easy and hard problems (easy needs regularization, hard doesn't), progress on easy problems can degrade hard-problem performance in mixed batches, and no principled automatic curriculum exists for RLVR.
- **Multi-Turn and Agentic RLVR.** Current RLVR is single-turn; real-world tasks (software engineering, tool use, research) are multi-turn with intermediate feedback, and credit assignment over long horizons compounds the sparsity problem exponentially.
- **On-Policy vs. Off-Policy.** Distributed training makes RLVR inherently off-policy due to policy lag; self-distillation deliberately uses off-policy teacher targets; the optimal degree of off-policy-ness and whether principled hybrid methods can capture on-policy stability with off-policy sample efficiency remains unknown.