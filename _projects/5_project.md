---
layout: page
title: Vocabulary Expansion
description: Expanding vocabulary of large language model to include Korean language capabilities.
img: assets/img/ve.png
importance: 2
category: work
---

## Contents

- [Summary](#summary)
- [Training Pipeline](#training-pipeline)
    - [Processing a Dataset](#processing-a-dataset)
    - [Training a Model](#training-a-model)
        - [Stage 1](#stage-1)
        - [Stage 2](#stage-2)
        - [Stage 3](#stage-3)
        - [Stage 4 & 5](#stage-4--5)
        - [Stage 6 & 7](#stage-6--7)
        - [Supervised Fine-tuning](#supervised-fine-tuning)
- [Results](#results)
- [Discussion](#discussion)


<br>

## Summary

Most foundation models are primarily optimized for English, often overlooking performance in low-resource languages. This issue is particularly pronounced at the tokenizer level, where inefficiencies can degrade both training and inference performance.

Recent studies in the research community have begun to address this gap, focusing on model performance and tokenizer efficiency for low-resource languages. Notable works include *[Efficient and Effective Vocabulary Expansion Towards Multilingual Large Language Models](https://arxiv.org/abs/2402.14714v1)* and *[Tokenizer Choice for LLM Training: Negligible or Crucial?](https://arxiv.org/abs/2310.08754)*.

In particular, `EEVE` (Efficient and Effective Vocabulary Expansion) presents a detailed technical report on tokenizer expansion, gaining significant attention for its improvements in Korean tokenizer efficiency and downstream performance.

Motivated by these findings, we developed a process focused on tokenizer optimization and model enhancement specifically for Korean. Our approach is largely inspired by EEVE's methodology and incorporates the concept of `architecture expansion`—as proposed in [LLaMA Pro](https://arxiv.org/abs/2401.02415)—to mitigate `catastrophic forgetting` during model adaptation.

Compared to the original tokenizer, our method achieves a `30% improvement in tokenizer efficiency`, which contributes to faster inference. Moreover, our internal benchmarking shows performance improvements ranging from ***12% to 34%*** across various tasks.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/vocab-expansion.png" title="vocab-expansion" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Overall pipeline for Vocabulary Expansion
</div>

<br><br>

## Training Pipeline

- Processing a Dataset

- Training a Tokenizer

- Training a Model

<br>

### Processing a Dataset

Following previous studies, we first construct a training dataset for tokenizer and model training. The dataset consists of raw corpora (raw documents), all of which are in Korean. The corpus is sourced from in-house datasets—including crawled data and open documents—as well as samples extracted from the Common Crawl dataset.

As a first step, we assess the `perplexity` of each document using the [KenLM](https://huggingface.co/edugp/kenlm) language model trained on Wikipedia. Since the KenLM model is trained on Wikipedia, documents with excessively **high perplexity** are likely to contain syntactic or semantic noise (e.g., ungrammatical sentences), whereas documents with **extremely low perplexity** are often characterized by excessive repetition.

KenLM is an n-gram language model that applies **Kneser-Ney smoothing**, allowing low-order n-gram terms to be discounted, thus improving robustness in perplexity estimation.

To filter documents, we use the `trimmed mean` to derive an empirical threshold. Documents falling outside this threshold—either too high or too low in perplexity—are removed. Examples of filtered documents are provided below.

* **Documents with excessively high perplexity:**

    '징징거리지마라쫌. 꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕꾀갱꾕꾕꾕
    ...
    '

* **Documents with excessively low perplexity:**

    '... 86 87 88 89 90 91 92 93 94 95 96 97 98 99 100 101 102 103 104 105 ...'

In addition, we remove semantically uninformative numerical sequences (e.g., 국도는 1호선, 2호선, 4호선, 13호선, 15호선, 18호선, 21호선, 22호...) from the dataset. We also apply a **deduplication process** based on **MinHash** to eliminate redundant content.

From the total corpus of 50 billion tokens, we select and utilize 10 billion tokens for model training.



<br><br>

### Training a Tokenizer

To mitigate distributional mismatch between the tokenizer and the model training data, we randomly sample 1 billion tokens from the dataset prepared during the [Processing a Dataset](#processing-a-dataset) stage and use them for tokenizer training.

We begin by constructing a base tokenizer with a vocabulary size of 40,000. New vocabulary items are added only if they appear at least 25,000 times in the sampled data.

Through this process, approximately 10,000 new tokens are added (based on LLaMA-3), with careful attention to avoid the over-inclusion of named entities. To this end, we apply **rule-based filtering** and perform **manual curation** to refine the final vocabulary.

<br><br>

### Training a Model

<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/eeve.png" title="eeve" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="caption">
    Training Stage of Efficient and Effective Vocabulary Expansion (EEVE)
</div>


<br><br>

Our model aims to train an expanded embedding layer and prediction head corresponding to an extended tokenizer. In addition, the model itself is jointly optimized to integrate previously acquired knowledge with newly learned Korean-specific knowledge. This process incorporates techniques designed to mitigate catastrophic forgetting, such as [architecture expansion](https://arxiv.org/abs/2401.02415) and [QLoRA](https://arxiv.org/abs/2305.14314).

The training procedure is composed of two main stages: `Continual Pre-training` and `Supervised Fine-tuning`.

The expanded embedding layer and prediction head are **not** randomly initialized. Instead, they are initialized using the [mean embedding](https://www.cs.columbia.edu/~johnhew/vocab-expansion.html) of existing tokens and sub-token, respectively, following techniques proposed in prior work.

<br><br>

#### Stage 1

In our training pipeline, progression to the next stage is determined by the convergence of training loss. This approach enhances training efficiency and mitigates potential mismatches between new embeddings and existing ones.

In particular, Stage 1 exclusively involves the learning of new embeddings. As such, it comprises only 20% of the total training steps.

At the end of each stage, we evaluate both training loss and benchmark performance to select the most appropriate model for the subsequent stage.

<br>

#### Stage 2

Stage 2 focuses solely on the training of the prediction head. Faster convergence of the loss was observed in this phase compared to Stage 1.

This stage demonstrated the most significant drop in loss, and consistent with the previous stage, model selection for the next phase was based on performance and convergence metrics.

<br>

#### Stage 3

In this stage, alignment is performed between the newly initialized embeddings and the prediction head, both of which were warmed up during the previous stages. This helps refine the internal representations to ensure coherence across components.

<br>

#### Stage 4 & 5

From Stage 4 onward, the parameters of the base model are also fine-tuned. Unlike earlier stages, we do not apply early stopping based on training loss, opting instead to train on the full token set to allow meaningful adjustments in the model’s parameter space.

A notable observation during this phase is a substantial drop in English benchmark performance, accompanied by a significant improvement in Korean benchmark results.

In Stage 5, although a temporary increase in training loss was observed, it eventually resumed its decline. No further degradation in English performance was noted, while improvements in Korean benchmark scores continued.

<br>

#### Stage 6 & 7


<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/llama-pro.png" title="llama-pro" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="caption">
    Block expansion method proposed in LLaMA Pro
</div>

Stages 6 and 7 involve full-parameter tuning, with the primary objective being the mitigation of `catastrophic forgetting`.

To address this, we adopt two complementary strategies: `QLoRA` and `LLaMA Pro`. QLoRA is implemented using 4-bit precision and reflects insights from prior research suggesting that applying LoRA in low-rank adaptation helps achieve a [less learn, less forget](https://arxiv.org/abs/2405.09673) dynamic.

<br><br>

#### Supervised Fine-tuning

During the supervised fine-tuning stage, we utilized only internal data, with English and Korean datasets incorporated in equal proportions.

In contrast to prior studies, we introduce a curriculum learning strategy in which the model is first trained on a large-scale, low-quality dataset, followed by fine-tuning on a smaller, high-quality dataset.

This approach yielded meaningful performance improvements, particularly in Korean language tasks. We posit that this effect offers a promising direction for enhancing the performance of low-resource languages that are underrepresented in the pre-training corpus.


<br><br>


## Results

| Models                                           | En    | KoBest    | Haerae    | Avg   |
|--------------------------------------------------|-------|-------|-------|-------|
| Mistral-7B-v0.1                                  | 0.7612| 0.5108| 0.3468| 0.5055|
| Mistral-7B-v0.1 (Continual Pre-training Only)    | 0.3835| 0.5017| 0.1858| 0.3529|
| Ours (QLoRA)                                     | <strong> 0.7635 </strong>| 0.5745| 0.4797| 0.5816|
| Ours (LLama pro)                                 | 0.7540| <strong> 0.5881 </strong>| <strong> 0.5354 </strong>| <strong> 0.6061 </strong>|

<br><br>

| Models                                           | BQ    | CP    | HS    | BQ    | CP    | HS    | SN    | WIC   | En AVG | Ko AVG | AVG    |
|--------------------------------------------------|-------|-------|-------|-------|-------|-------|-------|-------|--------|--------|--------|
| beomi/Llama-3-Open-Ko-8B-Instruct                | 0.4339| 0.8300| 0.5429| 0.6254| 0.7110| 0.3840| 0.8388| 0.5738| 0.6023 | 0.6266 | 0.6175 |
| maywell/Llama-3-Ko-8B-Instruct                   | 0.6260| 0.7500| 0.4279| 0.7208| 0.7650| 0.4440| 0.9194| 0.6040| 0.6013 | 0.6906 | 0.6571 |
| Llama-3-Alpha-Ko-Evo                             | 0.6688| 0.9300| 0.5352| 0.8547| 0.7420| 0.4220| 0.9471| 0.6095| 0.7113 | 0.7151 | 0.7137 |
| Llama-3-Alpha-Alpha-Ko-Instruct                  | 0.7532| 0.9300| 0.5291| 0.8369| 0.7420| 0.4240| 0.9244| 0.5730| 0.7374 | 0.7001 | 0.7141 |
| Ours                                             | 0.8040| 0.9100| <strong> 0.5894 </strong>| 0.8590| <strong> 0.7650 </strong>| 0.4220| 0.9421| 0.5873| 0.7678 | 0.7151 | 0.7349 |
| Ours (curriculum learning)                       | <strong> 0.8330 </strong>| 0.9000|  0.5863 | <strong> 0.8654 </strong>| 0.7590| 0.4260| <strong> 0.9622 </strong>| 0.6071| <strong> 0.7731 </strong> | <strong> 0.7239 </strong> | <strong> 0.7424 </strong> |

<br><br>

The upper table presents results based on a model utilizing Mistral as its backbone.

The model labeled `Continual Pre-training only` refers to one trained on the same dataset using a next-token prediction objective. The experimental findings suggest that even when trained on an identical dataset, performance can improve through staged learning. Notably, by incorporating vocabulary expansion, the results closely replicate the technical report of eeve, which emphasizes inference efficiency.

Furthermore, while the model fine-tuned with Llama Pro underperformed in terms of forgetting compared to the one using QLoRA, it demonstrated superior performance in terms of adaptation.

The table below displays Korean-tuned open-source models built upon the [Llama-3-8B-Instruct](https://huggingface.co/meta-llama/Meta-Llama-3-8B-Instruct) backbone.

Our approach clearly outperforms these open-source models in Korean benchmark tasks, indicating a significant performance advantage.

In particular, when examining English benchmark performance, our method appears to exhibit the least forgetting, with a substantial gap in performance observed.

These results underscore the effectiveness of our reproduction method, which demonstrates strong capabilities in both adaptation and mitigation of forgetting.

<br><br>


## Discussion

In this project, we develop a Korean-specialized large language model (LLM) and achieve the following results. The techniques and contributions I made during this process have all been internalized as proprietary technologies within the company.

- We successfully enhance the Korean language capabilities of the LLM by integrating the concept of model expansion.
  - Through this result, we find that guiding the model to handle specific knowledge in an expanded parameter space can lead to performance improvements.

- For low-resource languages, training on documents with relatively few tokens followed by supervised fine-tuning yields meaningful performance gains when curriculum learning is applied.
  - We observe that the model trained with curriculum learning outperformed the one trained on the entire dataset simultaneously.
  - This performance gain is more pronounced not only on static benchmark datasets but also under LLM-as-a-judge evaluation settings.

- A method for domain adaptation (LLaMA Pro) demonstrate effectiveness even in multilingual adaptation scenarios.
  - This suggests that, conversely, techniques developed for language extension can also be applied to domain adaptation.
  - Based on insights from this project, we observe in a separate project that ideas similar to [Chat Vector](https://arxiv.org/abs/2310.04799) can be effectively applied to domain adaptation tasks.
