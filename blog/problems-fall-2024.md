[← back](../index.html){.back}


# Problems and Ideas, c. Fall 2024

::: {.date}
November 25th, 2024
:::


Here is (an ongoing) list of some problems and ideas I am currently thinking about, circa November 2024. If you have any thoughts, please reach out!


- **`Poor FLOP utilization of GPU cores`**: billions on top of billions are being spent on compute to power the recent AI revolution, propelling NVIDIA to be the most valuable company in the world. From well-funded labs to seed-stage startups, buying GPU's for training and performing inference with increasingly large models is necessary. However, what most people don't realize is that the physical cores on these GPU's are often underutilized, on training and inference jobs. The recent Llama 3 technical report (see page 10 [here](https://arxiv.org/pdf/2407.21783)) states 38-43% MFU (Model FLOPs Utilization) during the training of this suite of models. That's right: one of the world's most funded and talent-dense research labs can't even achieve half of complete FLOP utilization of its compute infrastructure.  With over [half a trillion](https://www.sequoiacap.com/article/ais-600b-question/) in spending on AI, you'd think we'd better be utilizing each individual GPU core to the best of its ability at every second--idle time per core effectively costs billions of dollars over time. How can we solve this problem? Perhaps a [generalizable](https://github.com/HazyResearch/ThunderKittens) [framework](https://github.com/triton-lang/triton) for writing CUDA kernels? Better GPU programmers? [Model-specific hardware](https://www.etched.com/announcing-etched)? Something you propose? I'm not sure, and I think this is a really difficult technical problem. But if you have any thoughts, please let me know.


- **`No AI-first horizontal robotics platform and infra`**: ROS is subpar, is old software, and is not designed to be integrated with the modern AI stack. New [robotics](https://x.com/watneyrobotics/status/1861170413482713180) [companies](https://maticrobots.com/blog/why-rust-its-the-safe-choice/) are building robotics platforms from the ground up, through forward-thinking systems languages like Rust. [Matic's robotic stack](https://x.com/jokrvivek/status/1858932651459309873) literally runs inside a single compiled rust process on-device ([apparently](https://x.com/jokrvivek/status/1859381977759220019) you can deploy your PyTorch model endpoint through a CUDA kernel ported to rust to run on-device)! In addition to building better platforms, we are going to need better infra. AI models are getting larger and larger. LLM's that OpenAI and Anthropic build can run on acre-scale datacenters without issue. But how are we going to deploy [billions-scale](https://www.physicalintelligence.company/blog/pi0) [ACT](https://github.com/tonyzhaozh/act)'s or [VLA](https://robotics-transformer2.github.io/)'s on-device for physical, robotic applications? Essential points of interest include low-latency, non-leaky abstractions, and specialized hardware. In 10 years, the world will look a lot more roboticized than it is today. Many are predicting GPT-like moments for robotic manipulation. If successful, industries that require manual labor are bound to be severely transformed. How are we going to build the infrastructure to support all of this?

<!-- - **`Clinical trial matching`**: ...  -->


<!-- - **`The security layer for AI agents**: ...  -->


- **`AI in medicine for diagnostic purposes`**: The potential for using AI for medical diagnostic purposes is actually what first got me interested in machine learning a half decade ago. "We can classify lung nodules as cancerous or not months before symptoms appear?!" I read. Yet, despite significant progress in academia, bureaucratic obstacles, resistance from traditionalist healthcare staff, and the lack of robustness in machine learning models continue to hinder the transformative potential of AI in healthcare. How will we fix this?