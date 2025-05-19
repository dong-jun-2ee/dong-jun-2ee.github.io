---
layout: page
title: Knowledge Distillation
description: Transferring the capabilities of a large language model to a smaller one.
img: assets/img/kd.png
importance: 2
category: work
---

## Contents

- [Summary](#summary)
- [Methodology](#methodology)
    - [Overview](#overview)
    - [Learning from teacher](#learning-from-teacher)
    - [Training the classmate](#training-the-classmate)
    - [Learning from strong and weak models](#learning-from-strong-and-weak-models)
- [Performance Comparsions](#performance-comparisons)
- [Discussion](#discussion)

<br><br>

## Summary

Recent efforts have focused on training foundation models more **efficiently and effectively**. To this end, both classical approaches such as *knowledge distillation* and more sophisticated techniques have been explored. For example, [Gemma 3](https://arxiv.org/abs/2503.19786) was distilled from [Gemini](https://gemini.google.com/), and the [LLaMA series](https://arxiv.org/abs/2407.21783) also maximized training efficiency through distillation from larger models. Particularly, as the importance of *reasoning models* has grown, this approach has been adopted in models like [Qwen3](https://arxiv.org/abs/2505.09388) and [DeepSeek](https://arxiv.org/abs/2501.12948), demonstrating its effectiveness.

[Knowledge Distillation](https://arxiv.org/abs/1503.02531), initially developed for vision models, has proven effective for language models as well. In the era of large language models (LLMs), two main types of distillation have emerged: **white-box** and **black-box**. Among these, white-box distillation is applicable when the teacher model's logits are observable. This method has recently evolved in close connection with reinforcement learning \[[1](https://arxiv.org/abs/2306.13649), [2](https://arxiv.org/abs/2306.08543), [3](https://arxiv.org/abs/2402.03898)].

Despite these advances, existing methods still operate under the **strong-to-weak distillation paradigm**. We observe that there remains significant room for improvement. Inspired by the concept of [Weak-to-Strong Generalization](https://arxiv.org/abs/2406.14629), we propose a new approach that leverages *additional signals* from weak models to further enhance performance.

Our method introduces a framework in which feedback is generated from a **weaker model of the same size**. Rather than treating this feedback as reliable supervision, we treat it as **noisy or incorrect information** and train the model accordingly. This allows the learning process to benefit from diverse and challenging signals.

Our approach achieves **over 10% improvement** compared to existing state-of-the-art models and shows **dominant performance** on 4 out of 5 benchmarks.


<br><br>

## Methodology

- Overview

- Learning from teacher

- Training the classmate

- Learning from strong and weak model

<br>

### Overview

<br><br>

### Learning from teacher

<br><br>

### Training the classmate

<br><br>

### Learning from strong and weak models

<br><br>


## Performance Comparisons

| Models                                                   | Dolly  | Self-inst  | Vicuna  | Super-Natural  | Unnatural  | AVG |
|----------------------------------------------------------|----------|----------|----------|----------|----------|--------------|
| GKD                                                      | -        | -        | -        | -        | -        | -            |
| MiniLLM                                                  | -        | -        | -        | -        | -        | -            |
| Distillm                                                 | -        | -        | -        | -        | -        | -            |
| Ours                                                     | -        | -        | -        | -        | -        | -            |


## Discussion

