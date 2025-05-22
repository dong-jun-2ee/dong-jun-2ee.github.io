---
layout: page
title: Small Langauge Model
description: Training small language models for on-device deployment.
img: assets/img/slm.png
importance: 2
category: work
related_publications: false
---

## Contents

- [Summary](#summary)
- [Role of Project](#role-of-project)
- [Discussion](#discussion)

<br><br>

---

## Summary

With the recent growth in the scale of language models, there has been a significant improvement in their performance. However, deploying large-scale models (exceeding 3 billion parameters) on `edge devices` remains a substantial challenge due to limitations in computational resources, memory, and power consumption.

Consequently, there is a growing demand for `small language models` that are capable of ***domain-specific*** and ***agentic behaviors***. Despite the availability of numerous high-performing small models, their generation processes remain verbose, leading to increased battery usage and slower response times on edge devices.

In this project, we conducted research on fine-tuning language models to operate effectively under constrained environments, ***specifically targeting specialized tasks*** on edge devices. We further translated these research outcomes into the development of a product.

---

## Role of the Project

The scope of this project was confined to wellness-related functionalities and the core operations of air purifiers. The system was designed to recognize and respond to a variety of ***bilingual commands*** by executing appropriate actions and generating relevant responses.

Rather than focusing on general-purpose conversational capabilities, the project emphasized `task-oriented performance`. To this end, we explored strategies to constrain model output based on input context, thereby constructing a specialized language model optimized for specific functions. Additionally, we investigated techniques to enhance inference performance on edge devices.

A comprehensive training pipeline was developed, accounting for test case coverage and real-world usability. This pipeline enabled `continuous generation of training data`, `iterative model training,` and `evaluation`. 

My specific responsibilities in the project included `training small language models` and `curating, modifying, and generating domain-specific datasets`. As a result of these efforts, we achieved a performance improvement of over 20 percentage points compared to the baseline, culminating in a functional success rate exceeding 95%.

---

## Discussion

Although this project was primarily aligned with the development of an agentic system for [NAMUHX](https://www.namuhx.com), its scope is not limited to that particular implementation. Notably, the core technologies developed have been adopted in commercial products by client, and therefore, certain technical details remain confidential.

- I was directly responsible for `the creation of domain-specific datasets` and the `training and validating the small language model` tailored to product requirements.
  - In particular, we identified data balancing and typification as critical factors. Establishing a continuously evolving and ***iteratively refined data pipeline*** proved to be essential for model improvement.

- According to internal evaluation metrics, our optimized system demonstrated a `performance gain of over 20 percentage points` from the initial baseline.

- We successfully implemented an agentic LLM capable of handling diverse scenarios on edge devices, and demonstrated the practicality of small language models (sLLMs) under constrained computational environments.