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
    - [Reading Comprehension](#reading-comprehension)
    - [Logit Swap](#logit-swap)
- [Results](#results)
    - [Data Perspective](#data-perspective)
    - [Training Perspective](#training-perspective)
- [Discussion](#discussion)


<br>

## Summary

Open chat models and closed-source LLMs are primarily designed for general-purpose conversations. However, these models often lack sufficient knowledge in specific industrial domains and demonstrate significantly lower performance compared to their general conversational abilities. Consequently, there is a growing need to develop domain-specialized language models that possess knowledge tailored to specific industries—an endeavor that remains a highly challenging task.

One of the most critical issues in domain adaptation is *`catastrophic forgetting`*, where a model loses previously acquired knowledge during additional training phases. Moreover, it struggles to integrate new domain-specific knowledge with existing general knowledge. Unlike retrieval-augmented generation (RAG) approaches, which improve performance by referencing external documents, domain-specialized models offer advantages in inference efficiency and reduced hallucination. To support this, various methods based on continual pre-training have been proposed.

Related studies span multiple dimensions, including `data composition` \[[1](https://arxiv.org/abs/2309.09530), [2](https://arxiv.org/abs/2406.14491), [3](https://arxiv.org/abs/2311.08545)], `model architecture` \[[4](https://arxiv.org/abs/2401.02415)], and `training strategies` \[[5](https://arxiv.org/abs/2403.08763), [6](https://arxiv.org/abs/2406.17245), [7](https://arxiv.org/abs/2406.14833)].

In our work, we focus on building a domain-specific language model through the lens of *data composition* and *training schemes*. Specifically, we adopt data composition methodologies originally designed for English to be effective for Korean. Through this adaptation, we achieve up to a ***10% performance improvement*** on domain-specific benchmarks while limiting performance degradation on general benchmarks to ***less than 1%***.

Furthermore, by employing a training technique leveraging *logit space manipulation*, we attain ***up to a 5% performance gain*** on domain-specific benchmarks.


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

<br>

Building on previous research that demonstrated performance improvements by formatting raw domain-specific documents into a [*reading comprehension*](https://arxiv.org/abs/2309.09530) style according to predefined rules, we devised and applied a rule-based framework tailored for Korean-language documents.

This approach utilizes conjunctions or specific phrases to identify patterns using regular expressions and reformats the documents to enhance their interpretability. For instance, in sentences containing the phrase *`"is defined"`*, we extract the term preceding the phrase along with the subsequent explanation, forming a ***term-definition pair***. Similarly, when encountering conjunctions such as *`"but"`* or *`"however"`*, we infer that the preceding and following sentences express ***contrasting meanings***.

By applying such rules, we concatenate the reformulated information to the end of the original document. This enables the model to extract and learn key information more efficiently, even from a limited number of training samples.

The *`reading comprehension`* framework can encompass various task types, including:

* **Summarization**: defines the relationship between the title (usually the first sentence) and the main body of the text.
* **Word-to-Text**: generates question-answer pairs based on the definition of a word.
* **Natural Language Inference**: bridges an answer by identifying the logical relationship between two sentences.
* **Commonsense Reasoning**: explains causal relationships by generating question-answer pairs based on cause-and-effect pairs.
* **Paragraph Detection**: identifies the relationship between two sentences, such as whether they convey the same or different meanings.
* **Text Completion**: converts a sentence into a question-answer pair by arbitrarily cutting out the middle of the sentence to allow for completion.

We extract a total of 825,773 instruction-response pairs from 383,580 legal documents.

Detailed information related to the extracted tasks is provided in the table below.

| Category       | Subcategory     | Count    |
|----------------|-----------------|----------|
| Summarize      | Summarization   | 200,093  |
| Word2text      | word2text       | 98,941   |
|                | definition      | 857      |
| NLI            | True            | 132,809  |
|                | False           | 54,389   |
|                | Possibly true   | 37,245   |
| Common reason  | Cause and Effect| 134,075  |
|                | Effect and Cause| 14,913   |
| Paraphrase     | Different       | 44,409   |
|                | Similar         | 27,769   |
| Text Completion| Text Completion | 80,273   |
| Total          | -               | 383,580  |


Example:

```python
본문: 그런데 A에게는 대전에서 근무하는 아들 乙이 있고 , 乙이 위 차량을 대전에서 운전하고 주말에는 서울에 올라오는 등 차량의 주운전...
2 위 법률 도메인 본문에 대해 몇 가지 질문에 답해주세요:

문맥에서 다음 가설을 도출할 수 있나요?
문맥: 이에 甲은 乙에게 흠이 없는 새 승용차로 교환하여 줄 것을 요구하였다. ...
가설: 乙은 甲과 매매계약을 체결할 당시에 사용한 자동차매매약관에 다음과 같은 조항이 있음을 이유로 甲의 요구를 거절하였다 . " ( 자동...
답변: 두 문장은 모순의 관계를 갖고 있습니다.

2. 약관의 법적 규제 (1) 전술한 약관의 부정적인 면을 주로 규제하기 위해 일찍이 외국의 입법례는 법률로써 이를 규율하는 방식을 취..
위 문장의 나머지 부분을 제공하세요: 대표적인 것으로 , 독일의 「보통거래약관에 관한 법률」 (Gesetz zur Regelung des Rechts de...
"이에 甲은 乙에게 흠이 없는 새 승용차로 교환하여 줄 것을 요구하였다 . "의 의미를 반박하는 문장을 작성할 수 있나요? 乙은 甲과 매매계...
이 법률 단락에 대한 제목을 생성하세요.

제목: 제3절 約款에 의한 契約의 成立 사례
```

<br>

### Logit Swap

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/mix-cpt.png" title="mix-cpt" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Training framework of logit swap method.
</div>

<br>

Inspired by prior research on [domain adaptation](https://openreview.net/forum?id=h1XoHOd19I) using `knowledge distillation`, we explore domain adaptation for Korean language models. In particular, we aim to ***maintain a balance between existing and newly acquired knowledge*** to mitigate `catastrophic forgetting`.

The proposed method is motivated by knowledge distillation from a teacher model. Notably, the approach allows flexibility in selecting or modifying the teacher model. When a high-capability teacher is used, the model benefits not only from domain-specific knowledge but also from general knowledge because it is trained on logit distribution. Furthermore, our method is applicable to ***both the continual pre-training stage and the supervised fine-tuning stage***, offering additional versatility.

We consider a high-performing general-domain model as the teacher and compute the logit distribution over the training data. If the real target token does not match the token with the maximum logit value, we apply a simple trick by swapping them. `This trick encourages the teacher model to produce a logit distribution more similar with domain expert.` Proposed approach assumes that the teacher model’s output distribution is not significantly different from the optimal one.

Through this strategy, the student model benefits from a distillation-like process, as if guided by a virtual domain expert, while also learning from ***soft supervision signals, thereby alleviating the problem of forgetting***.

<br>

## Results 

### Data perspective

| Models                                                   | EN - general | Ko - general | Ko - legal | 
|----------------------------------------------------------|----------|----------|----------|
| Basemodel                                                | 61.44%   | 39.69%   | 30.67% |
| Continual Pre-training on raw document                   | <strong> 70.60% </strong>  | 45.39%   | 31.29% |
| Continual Pre-training on reading comprehension data     | 70.42%   | <strong> 48.13% </strong>  | <strong> 33.07% </strong> |

<div class="caption">
    Overall performance of pretrained model
</div>

<br>

| Models                                                   | EN - general | Ko - general | En - legal |  Ko - legal | 
|----------------------------------------------------------|----------|----------|----------|----------|
| General Model                                            | 76.59%   | 55.92%   | 34.26% | 39.16% |
| Ours                                                     | <strong> 77.39% </strong> | <strong> 58.00% </strong> | <strong> 52.73% </strong> | <strong> 58.16% </strong> |

<div class="caption">
    Performance comparison between General chat model and our domain-specific model
</div>

<br>

According to the experimental results, the model trained on legal documents outperforms the base model on domain-specific tasks. Furthermore, the model that undergoes continual pre-training demonstrates superior performance even in general-domain settings, ***suggesting that the nature of legal texts—which often require complex reasoning—contributes to this enhancement.*** Notably, documents augmented through reading comprehension tasks lead to greater performance gains compared to model trained on raw corpora. These findings indicate that the reading `comprehension approach effectively extracts informative signals from limited data`, emphasizing its worth in improving performance from a data composition perspective.

<br>

### Training perspective

| Models        | General| Legal  | 
|---------------|--------|--------|
| Baseline  	| 62.47% | 43.19% |
| Ours	        | 61.88% | 45.73% | 

<div class="caption">
    Overall performance of our approach compared to the baseline model trained on domain data without logit swap
</div>

<br>

We evaluate the efficiency and effectiveness of our proposed ***logit swap*** approach by comparing it with a baseline model trained using standard cross-entropy loss.

While our model underperforms the baseline in the general domain, it achieves superior performance in the legal domain. Through experiments, we observe that using a stronger teacher model leads to improved performance on domain-specific benchmarks, with only a marginal decrease in general-domain performance. This suggests that greater performance gains may be achievable when more computational resources are available.

However, our approach still exhibits limitations, including `relatively modest performance improvements` and `minimal, yet non-negligible, degradation` in general-domain performance.

<br>

## Discussion

During the course of this project, we developed simple yet effective domain adaptation approaches from both the perspective of data composition and training algorithms. Our primary focus was on training domain-specific large language models (LLMs), aiming to efficiently acquire domain knowledge from constrained environments while minimizing catastrophic forgetting.

This project was conducted with a specific focus on the legal domain. Due to the reliance on internal and benchmark datasets, the work faces limitations regarding public accessibility. Additionally, both the proposed methods and source code are not publicly available.

- We observed that modifying data composition can significantly enhance the efficiency of domain adaptation.
  - In this context, our rule-based approach, though simple, proved to be effective. We found that overly strict rules can hinder the generation of meaningful learning signals, whereas overly lenient rules tend to promote duplication of documents.
  - Similar to prior research, structuring data in the format of reading comprehension tasks contributed to improvements in prompting capabilities, which in turn enhanced instruction-following performance after supervised fine-tuning.

- When conducting continual pre-training on datasets composed of legal reasoning data, we observed that the model's performance on general domain tasks also improved.

- The logit swap technique, despite its simplicity, contributed meaningfully to improving domain adaptation performance.
  - The effectiveness of this method scales with the capabilities of the teacher model; stronger teacher models lead to greater performance gains, and the inverse also holds.
  - However, this approach still presents limitations, as it does not always deliver outstanding adaptation performance, particularly when considering the modest performance degradation it may introduce.