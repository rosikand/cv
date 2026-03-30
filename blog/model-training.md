[← back](../index.html){.back}

# Model Training Experience

::: {.date}
March 2026
:::

\> *To be updated soon!...* 

<!-- ::: {.callout-note}
In this post, I list most of the model training experience I have. It is mainly here to show potential employers of my experience. I have been training machine learning models since ~2020, starting off with basic convolutional neural nets for medical image classification. Then, I scaled up my modeling experience at insitro, where I learned to train big models on big clusters, in a distributed fashion, with industry standard infrastructure. I continued my modeling experience in my *research* and *projects*, training hundreds of models across experiments. Along the way, I've created some nice workflows to make my life easier; most notably, orchestrating with [wandb](https:// wandb.ai/profile/ rosikand), [serverless] (https://github.com/ rosikand/ parameter-golf/blob/ ro-dev-1/ infrastructure/ HOW_IT_WORKS.md) GPU run launches, and building out [my own training package] (https://github.com/ rosikand/torchplate/ tree/main).
::: -->

In this post, I list the model training experience I have built over the last ~5 years since 2020. This is mainly here for potential employers and collaborators who want a concise view of my modeling experience.

I started in ~2020 with small CNNs for medical image classification, then moved into large-scale distributed training at insitro. Since then, I have continued training models through research and side projects, running hundreds of experiments and building systems to make iteration fast and reliable. Along the way, I've created some nice workflows on [wandb](https://wandb.ai/profile/rosikand), [serverless GPU launches](https://github.com/rosikand/parameter-golf/blob/ro-dev-1/infrastructure/HOW_IT_WORKS.md), and [my own training package](https://github.com/rosikand/torchplate/tree/main).

## Model training table

| Project | Timeframe | Organization / Context | Model types trained | Task / objective patterns | Typical scale | Infrastructure and tooling |
|---|---|---|---|---|---|---|
| Medical image classification (early work) | 2020-2021 | Early independent project work | CNNs, transfer learning backbones | Supervised classification, baseline optimization | Single GPU, many smaller experiments | PyTorch, notebook-to-script workflows, manual sweep scripts |
<!-- | insitro model training programs | 2021-2023 | Industry research environment | Large vision and sequence models, production training jobs | Distributed training, robust evaluation pipelines, reproducible research | Multi-GPU and multi-node cluster training | DDP-style training, cluster schedulers, artifact tracking, experiment dashboards |
| Research experiment suites (independent) | 2023-present | Personal research and project work | CNNs, ViT-style models, encoders, transformer-based models | Representation learning, multi-task prediction, ablation studies | Single to multi-GPU; dozens of runs per study | PyTorch, structured configs, checkpointing/resume, wandb logging |
| Serverless GPU training workflows | 2024-present | Infrastructure supporting project runs | Model-agnostic (used across vision and sequence experiments) | Fast iteration loops, objective/loss refinement, scalable remote launches | Burst scaling for parallel experiment batches | Serverless launches ([parameter-golf infra](https://github.com/rosikand/parameter-golf/blob/ro-dev-1/infrastructure/HOW_IT_WORKS.md)), run orchestration, wandb |
| Torchplate training package | 2024-present | Reusable internal training framework | Model-agnostic (plugged into multiple architectures) | Standardized training loops, reproducibility defaults, experiment portability | Used from local debugging to distributed runs | Modular package design ([torchplate](https://github.com/rosikand/torchplate/tree/main)), config-driven pipelines, mixed precision, seed control |

## Typical workflow

1. Define a clear baseline and success metric.
2. Build a minimal reproducible pipeline end-to-end.
3. Run targeted ablations (not random tuning) to isolate useful variables.
4. Scale only after correctness is confirmed at small size.
5. Track all experiments and compare against baseline before shipping changes.

## Representative outcomes

- Trained and compared hundreds of model variants across research and product-style settings.
- Improved iteration speed by standardizing reusable training loops and launch tooling.
- Reduced failed/irreproducible runs by enforcing config discipline and better logging defaults.
- Built reusable components so new projects start from a proven foundation instead of from scratch.

## Lessons learned

- Good training is mostly systems engineering: data quality, evaluation design, and experiment hygiene.
- Faster feedback loops are often more valuable than marginal model complexity.
- Most "model problems" are actually pipeline or objective problems.
- Clean infra compounds over time: every hour invested in tooling pays back across future projects.

## Links

- wandb profile: [wandb.ai/profile/rosikand](https://wandb.ai/profile/rosikand)
- serverless launch system: [parameter-golf infrastructure notes](https://github.com/rosikand/parameter-golf/blob/ro-dev-1/infrastructure/HOW_IT_WORKS.md)
- training framework: [torchplate](https://github.com/rosikand/torchplate/tree/main)

If you are hiring for ML research or engineering work, I am happy to share more concrete project examples, metrics, and code samples. -->
