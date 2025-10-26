---
layout: page
title: Few-Shot Learning for Indication Classification
description: A natural language processing system to classify breast MRI radiology reports into clinical indication categories in a true few-shot learning setting
img: /assets/img/12.jpg
importance: 1
category: work
---

## Abstract

Patient data exist in structured and unstructured formats. Structured data typically contains high level information. In cases where more detailed information is needed, unstructured data retrieval is required. Due to their simplicity, heuristic approaches are typically used to extract insights from the unstructured data sources. In this paper, we limit our scope to MRI radiology reports for breast cancer diagnostic and screening patients. We compare the heuristic approach to deep learning based approaches in their ability to classify the patient’s clinical indications from 500 labeled examples. The deep learning approaches include finetuning of BERT-like models and few-shot learning approaches like prototypical networks. Our results show a 69% improvement in the validation set F1 score when using the finetuning approach over the heuristic baseline.

---

## 1 Introduction

AI systems developed for breast cancer detection are evaluated at the population level and subgroup level. Subgroup analysis is used to understand the potential limitations of the AI systems and highlight unintended biases present in them. Patient subgroups can be categorized by age, breast tissue density, and clinical indication. While some subgroup categories like age are easy to extract from the electronic health record system, others like clinical indication are only available in unstructured formats and hence difficult to extract.

In this project, we develop a natural language processing system that classifies breast magnetic resonance imaging (MRI) radiology reports into five clinical indication categories in a true few-shot learning setting (Perez et al., 2021). During a patient breast cancer screening or diagnostic visit, radiology exams are conducted with one or more modalities. Radiologist review the medical images and record their findings in a radiology report. This report should include a description of the reason for the patient’s visit, referred to as the clinical indication. We have categorized the clinical indications into following classes:

1. **High-risk screening** – visit associated with patients that have high risk of cancer, usually because of genetic mutation associated with breast cancer (e.g. BRCA) or history of cancer in patient or patient’s family.
2. **Pre-operative planning** – visit that occurs following a positive biopsy.
3. **Additional workup** – visit that occurs after suspicious findings were found on a mammography or ultrasound exam, and the doctor would like to see the suspicious area in a different modality.
4. **Short-term follow-up** – visit done shortly (usually within 6 months) after imaging exam or invasive procedure to evaluate stability of findings.
5. **Treatment monitoring** – visit done for patients undergoing chemotherapy for breast cancer to monitor how the tumor responds to the treatment.
