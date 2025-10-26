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

***

## 1 Introduction

AI systems developed for breast cancer detection are evaluated at the population level and subgroup level. Subgroup analysis is used to understand the potential limitations of the AI systems and highlight unintended biases present in them. Patient subgroups can be categorized by age, breast tissue density, and clinical indication. While some subgroup categories like age are easy to extract from the electronic health record system, others like clinical indication are only available in unstructured formats and hence difficult to extract.

In this project, we develop a natural language processing system that classifies breast magnetic resonance imaging (MRI) radiology reports into five clinical indication categories in a true few-shot learning setting (Perez et al., 2021). During a patient breast cancer screening or diagnostic visit, radiology exams are conducted with one or more modalities. Radiologist review the medical images and record their findings in a radiology report. This report should include a description of the reason for the patient’s visit, referred to as the clinical indication. We have categorized the clinical indications into following classes:

1. **High-risk screening** – visit associated with patients that have high risk of cancer, usually because of genetic mutation associated with breast cancer (e.g. BRCA) or history of cancer in patient or patient’s family.  
2. **Pre-operative planning** – visit that occurs following a positive biopsy.  
3. **Additional workup** – visit that occurs after suspicious findings were found on a mammography or ultrasound exam, and the doctor would like to see the suspicious area in a different modality.  
4. **Short-term follow-up** – visit done shortly (usually within 6 months) after imaging exam or invasive procedure to evaluate stability of findings.  
5. **Treatment monitoring** – visit done for patients undergoing chemotherapy for breast cancer to monitor how the tumor responds to the treatment.  

***

## 2 Related Work

Extensive work has been published on the application of NLP on clinical data. A detailed literature survey of application of NLP to radiology reports specifically was conducted in Casey et al. (2021) and Pons et al. (2016). In Casey et al. (2021), papers reviewed are grouped into six categories associated with the specific application: Diagnostic Surveillance, Disease information and classification, Quality Compliance, Cohort/Epidemiology, Language Discovery and Knowledge Structure, and Technical NLP. Extraction of clinical indications explored in this paper falls under the Diagnostic Surveillance category. Surprisingly, none of the 164 papers reviewed have leveraged a transformer architecture. The majority of the papers reviewed use either rule-based approaches or traditional machine learning approaches like SVM and logistic regression. In Deshmukh et al. (2019), a BERT-based model is pretrained with a unsupervised learning approach on 218,159 radiology reports. The BERT model is then finetuned with a smaller dataset of radiology reports labeled into fine-grained disease classes. In Wood et al. (2020), a pre-trained BioBERT model (Lee et al., 2019) is finetuned on 3,000 MRI neuroradiology reports to determine the presence or absence of any abnormality. Note that BioBERT consists of a BERT model trained over a corpus of biomedical research articles sourced from PubMed article abstracts and PubMed Central article full texts. Alsentzer et al. (2019) present a variation of BioBERT, called Clinical BioBERT, in which the pretrained BioBERT model is trained on approximately 2 million notes from the MIMIC-III dataset (Johnson et al., 2016). Li et al. (2022) also leverage MIMIC-III dataset to train Longformer yielding Clinical Longformer.
