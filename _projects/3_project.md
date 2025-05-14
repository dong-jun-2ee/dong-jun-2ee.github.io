---
layout: page
title: Korean Reasonging Model
description: Training an o1-like large language model capable of reasoning and responding in Korean.
img: assets/img/reasoning.png
importance: 2
category: work
---

## Summary

Recently, models equipped with reasoning capabilities have demonstrated remarkable performance, such as [ChatGPT-o1](https://arxiv.org/abs/2412.16720).

In the open research community, [Deepseek-AI](https://arxiv.org/abs/2501.12948) provides recipes for building o1-like models, paving the way to equip models with reasoning capabilities.

Similarly, using the [verl](https://github.com/volcengine/verl) repository, we trained a large language model that generates its own reasoning process to improve performance. In particular, unlike the open o1-like models that support only English responses, we aligned our model to generate both reasoning traces and final summaries (i.e., primarily tokens without mathematical notation or code) in Korean.

We achieved performance improvements of up to 15% points over the original model, despite constraints on language consistency.

## Training Pipeline

- Building Datasets

- Knowledge Distillation

- Reward Modeling

- Reinforcement Learning


### Building Datasets

Following previous research, we generated prompt-response pairs for math and general conversation from a high-capability reasoning model for the knowledge distillation stage.

We generated responses to the same prompts from multiple models. In addition, we improved the quality of the data through outcome-based filtering and generative rewards, collecting approximately 200k data samples for knowledge distillation process.


### Knowledge Distillation

We trained the model to generate reasoning trajectories using [Qwen2.5-1.5B-Instruct](https://huggingface.co/Qwen/Qwen2.5-1.5B-Instruct) for this work. This model was chosen due to its strong performance in both Korean and English, as well as its permissive commercial use.

Using the prompt-response pairs generated in the previous stage, we performed black-box distillation, which enabled the model to learn how to generate thoughts from cold-start data.


### Reward Modeling

To assign rewards for general conversation, we built a reward model that supports Korean. This process mainly followed the pipeline of [Skyword-Reward](https://arxiv.org/abs/2410.18451), and we used publicly available models that support Korean to generate Korean response (chosen, rejected) pairs (e.g., [Mistral](https://huggingface.co/mistralai/Mistral-Small-24B-Instruct-2501), [Qwen](https://huggingface.co/Qwen/Qwen2.5-32B-Instruct)). 

Based on the generated responses, we assigned rewards using three open reward models. After performing re-centering based on the performance of each model, we constructed three types of preference datasets (chosen, rejected pairs).

We trained the reward model using each type of dataset, and selected the final model by evaluating its performance on [RewardBench](https://arxiv.org/abs/2403.13787) and [Multilingual Reward Bench](https://arxiv.org/abs/2410.15522).


### Reinforcement Learning

At this stage, our goal was to endow the chat model obtained from the knowledge distillation phase with reasoning capability. Specifically, we trained the model on math data using outcome-based rewards, which have been shown in previous studies to contribute significantly to performance improvement. Similar to Deepseekai-R1, we applied the reward model to summaries only.

Unlike previous successful reproductions, we additionally applied a language consistency reward, focusing on aligning the language used between the input prompt and the generated thought. One of the key insights from this process was that removing code snippets, mathematical equations, and markdown elements was crucial for preventing reward hacking.

We were able to accomplish this task by applying the [GRPO](https://arxiv.org/abs/2402.03300) reinforcement learning method.


## Results

### Reward Model

### Math

### Examples
    ---
    ### English
    Prompt: 
    
    Response: 
    ---
    ---
    ### Korean
    Prompt: 
    
    Response: 
    ---

## Discussion

<!-- Every project has a beautiful feature showcase page.
It's easy to include images in a flexible 3-column grid format.
Make your photos 1/3, 2/3, or full width.

To give your project a background in the portfolio page, just add the img tag to the front matter like so:

    ---
    layout: page
    title: project
    description: a project with a background image
    img: /assets/img/12.jpg
    ---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Caption photos easily. On the left, a road goes through a tunnel. Middle, leaves artistically fall in a hipster photoshoot. Right, in another hipster photoshoot, a lumberjack grasps a handful of pine needles.
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    This image can also have a caption. It's like magic.
</div>

You can also put regular text between your rows of images.
Say you wanted to write a little bit about your project before you posted the rest of the images.
You describe how you toiled, sweated, _bled_ for your project, and then... you reveal its glory in the next row of images.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>

The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:

{% raw %}

```html
<div class="row justify-content-sm-center">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
```

{% endraw %} -->
