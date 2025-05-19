---
layout: page
title: Domain Adaptation
description: Training a large language model capable of understanding domain-specific task.
img: assets/img/da.png
importance: 3
category: work
---

## Contents

- [Summary](#summary)
- [Approches](#approches)
- [Results](#results)
- [Discussion](#discussion)


<br>

## Summary

Open chat models and closed-source LLMs are primarily designed for general-purpose conversations. However, these models often lack sufficient knowledge in specific industrial domains and demonstrate significantly lower performance compared to their general conversational abilities. Consequently, there is a growing need to develop domain-specialized language models that possess knowledge tailored to specific industries—an endeavor that remains a highly challenging task.

One of the most critical issues in domain adaptation is *`catastrophic forgetting`*, where a model loses previously acquired knowledge during additional training phases. Moreover, it struggles to integrate new domain-specific knowledge with existing general knowledge. Unlike retrieval-augmented generation (RAG) approaches, which improve performance by referencing external documents, domain-specialized models offer advantages in inference efficiency and reduced hallucination. To support this, various methods based on continual pre-training have been proposed.

Related studies span multiple dimensions, including `data composition` \[[1](https://arxiv.org/abs/2309.09530), [2](https://arxiv.org/abs/2406.14491), [3](https://arxiv.org/abs/2311.08545)], `model architecture` \[[4](https://arxiv.org/abs/2401.02415)], and `training strategies` \[[5](https://arxiv.org/abs/2403.08763), [6](https://arxiv.org/abs/2406.17245), [7](https://arxiv.org/abs/2406.14833)].

In our work, we focus on building a domain-specific language model through the lens of *data composition* and *training schemes*. Specifically, we adapted data composition methodologies originally designed for English to be effective for Korean. Through this adaptation, we achieved up to a ***10% performance improvement*** on domain-specific benchmarks while limiting performance degradation on general benchmarks to ***less than 1%***.

Furthermore, by employing a training technique leveraging *logit space manipulation*, we attained ***up to a 3% performance gain*** on domain-specific benchmarks.


<br><br>

## Approaches

- Reconstructing the dataset into reading comprehension format

- Accelerating the training through swapped-logit from teacher model

<br>

### Reading Comprehension

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/reading-comprehension.png" title="reading-comprehension" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    An overview of training documents formatted in a reading comprehension style
</div>

Building on previous research that demonstrated performance improvements by formatting raw domain-specific documents into a *reading comprehension* style according to predefined rules, we devised and applied a rule-based framework tailored for Korean-language documents.

This approach utilizes conjunctions or specific phrases to identify patterns using regular expressions and reformats the documents to enhance their interpretability. For instance, in sentences containing the phrase *`"is defined"`*, we extract the term preceding the phrase along with the subsequent explanation, forming a ***term-definition pair***. Similarly, when encountering conjunctions such as *`"but"`* or *`"however"`*, we infer that the preceding and following sentences express ***contrasting meanings***.

By applying such rules, we concatenate the reformulated information to the end of the original document. This enables the model to extract and learn key information more efficiently, even from a limited number of training samples.

The *`reading comprehension`* framework can encompass various task types, including:

* **Summarization**
* **Word-to-Text**
* **Natural Language Inference**
* **Commonsense Reasoning**
* **Paragraph Detection**
* **Text Completion**
