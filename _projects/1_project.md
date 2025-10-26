---
layout: page
title: Few-Shot Learning for Indication Classification
description: A natural language processing system to classify breast MRI radiology reports into clinical indication categories in a true few-shot learning setting
img: /assets/img/projects/clinical_indication.png
importance: 1
category: research
scholar:
  group_by: none
  group_order: none
---

## Abstract

Patient data exist in structured and unstructured formats. Structured data typically contains high level information. In cases where more detailed information is needed, unstructured data retrieval is required. Due to their simplicity, heuristic approaches are typically used to extract insights from the unstructured data sources. In this paper, we limit our scope to MRI radiology reports for breast cancer diagnostic and screening patients. We compare the heuristic approach to deep learning based approaches in their ability to classify the patient's clinical indications from 500 labeled examples. The deep learning approaches include finetuning of BERT-like models and few-shot learning approaches like prototypical networks. Our results show a 69% improvement in the validation set F1 score when using the finetuning approach over the heuristic baseline.[^code-link]

---

## 1 Introduction

AI systems developed for breast cancer detection are evaluated at the population level and subgroup level. Subgroup analysis is used to understand the potential limitations of the AI systems and highlight unintended biases present in them. Patient subgroups can be categorized by age, breast tissue density, and clinical indication. While some subgroup categories like age are easy to extract from the electronic health record system, others like clinical indication are only available in unstructured formats and hence difficult to extract.

In this project, we develop a natural language processing system that classifies breast magnetic resonance imaging (MRI) radiology reports into five clinical indication categories in a true few-shot learning setting (Perez et al., 2021). During a patient breast cancer screening or diagnostic visit, radiology exams are conducted with one or more modalities. Radiologist review the medical images and record their findings in a radiology report. This report should include a description of the reason for the patient’s visit, referred to as the clinical indication. We have categorized the clinical indications into following classes:

1. **High-risk screening** – visit associated with patients that have high risk of cancer, usually because of genetic mutation associated with breast cancer (e.g. BRCA) or history of cancer in patient or patient’s family.
2. **Pre-operative planning** – visit that occurs following a positive biopsy.
3. **Additional workup** – visit that occurs after suspicious findings were found on a mammography or ultrasound exam, and the doctor would like to see the suspicious area in a different modality.
4. **Short-term follow-up** – visit done shortly (usually within 6 months) after imaging exam or invasive procedure to evaluate stability of findings.
5. **Treatment monitoring** – visit done for patients undergoing chemotherapy for breast cancer to monitor how the tumor responds to the treatment.

---

## 2 Related Work

Extensive work has been published on the application of NLP on clinical data. A detailed literature survey of application of NLP to radiology reports specifically was conducted in {% cite casey2021systematic --file references %} and {% cite pons2016nlpradiology %}. In {% cite casey2021systematic --file references %}, papers reviewed are grouped into six categories associated with the specific application: Diagnostic Surveillance, Disease information and classification, Quality Compliance, Cohort/Epidemiology, Language Discovery and Knowledge Structure, and Technical NLP. Extraction of clinical indications explored in this paper falls under the Diagnostic Surveillance category. Surprisingly, none of the 164 papers reviewed have leveraged a transformer architecture. The majority of the papers reviewed use either rule-based approaches or traditional machine learning approaches like SVM and logistic regression. In {% cite deshmukh2019semi %}, a BERT-based model is pretrained with a unsupervised learning approach on 218,159 radiology reports. The BERT model is then finetuned with a smaller dataset of radiology reports labeled into fine-grained disease classes. In {% cite wood2020alarm --file references %}, a pre-trained BioBERT model {% cite lee2019biobert --file references %} is finetuned on 3,000 MRI neuroradiology reports to determine the presence or absence of any abnormality. Note that BioBERT consists of a BERT model trained over a corpus of biomedical research articles sourced from PubMed[^pubmed] article abstracts and PubMed Central[^pmc] article full texts. {% cite alsentzer2019clinicalbert --file references %} present a variation of BioBERT, called Clinical BioBERT, in which the pretrained BioBERT model is trained on approximately 2 million notes from the MIMIC-III dataset {% cite johnson2016mimic --file references %}. {% cite li2022clinlongformer --file references %} also leverage MIMIC-III dataset to train Longformer yielding Clinical Longformer.

---

## 3 Data

The radiology report dataset is provided by NYU Langone. There are 47,966 MRI reports, 565,504 ultrasound reports, and 1,168,384 mammography reports in our dataset. Although the scope of this paper is limited to MRI, we use radiology reports from other modalities in an intermediate task for pretraining before training on the target task. Note that none of the radiology reports have been labeled by clinical indication. We have manually labeled 500 radiology reports. The labeled dataset is split to train, validation, and test set by a 40% / 20% / 40% split respectively. Due to limited number of examples in the training and validation set associated with the “treatment monitoring” class, we have discarded the “treatment monitoring” class for this work. We have also added an “exclude” class label to capture reports with no relevant clinical indication information. In total, we have 5 classes given by high-risk screening, pre-operative planning, additional workup, short-term follow-up, and exclude.

---

## 4 Models and Methods

We start with a heuristic approach which uses regular expressions to classify labels based on patterns present in the reports. This is a baseline we use to compare against other approaches. We then finetune a pretrained Clinical BioBERT network with a classification head {% cite alsentzer2019clinicalbert --file references %}. Given that the max length for a sequence that Clinical BioBERT can process is 512 tokens, we implement several strategies to handle documents with lengths greater than 512. One approach is to simply truncate the sequence. Another method uses a sliding window approach, in which each window is processed separately by Clinical BioBERT and the intermediate representations or the class predictions are aggregated through an aggregation function. We have implemented four different aggregation functions for sliding window: mean, max, mean/max, and attention.

The mean/max aggregation (from {% cite huang2019 --file references %}) is:

$$
S \;=\; \frac{S_{\max}^{(K)} \;+\; S_{\text{mean}}^{(K)} \,\frac{K}{c}}{1 + \frac{K}{c}}
\tag{1}
$$

The attention-based sliding window aggregation approach leverages the gated attention mechanism from {% cite ilse2018attention --file references %}:

$$
\alpha_k \;=\;
\frac{\exp\!\left\{\, W\!\left(\tanh(V v_k)\;\odot\;\sigma(U v_k)\right) \right\}}
{\sum_{j=1}^{K} \exp\!\left\{\, W\!\left(\tanh(V v_j)\;\odot\;\sigma(U v_j)\right) \right\}}
,\qquad \sum_{k=1}^{K}\alpha_k = 1
\tag{2}
$$

To allow for processing longer sequences, we also finetune a pretrained Clinical Longformer network with a classification head {% cite li2022clinlongformer --file references %}. Although the max length of a sequence that Clinical Longformer can process is 4096, we truncate our reports to 1024 due to memory constraints. Per {% cite dopierre2021realitycheck --file references %}, in addition to using the off-the-shelf pretrained Clinical Longformer, we train it on the an intermediate masked language model (MLM) task to improve the quality of the output representation before finetuning on the target task.

We also implement a Prototypical Networks {% cite snell2017protonet --file references %} for few-shot learning with the off-the-shelf pretrained Clinical BioBERT, off-the-shelf pretrained Clinical Longformer, and MLM pretrained Clinical Longformer as the encoder base network. Prototypical networks use a nearest neighbor like strategy to classify examples from the output representation of the encoder base network. F1 score will be used as the main evaluation metric. Given that some of the few-shot learning approaches and the heuristic baseline model do not output class probabilities AUROC is not an applicable evaluation metric.

---

## 5 Results

Our heuristic baseline model uses a rule-based approach that relies on regular expressions to identify patterns and classify the reports into one of the five indication classes. As shown in Table 1, the resulting F1 score on the validation set for the heuristic baseline is **0.540**.

To finetune the Clinical BioBERT and Clinical Longformer models, a cross entropy loss function is used. Hyperparameter search is performed using a grid search strategy. Hyperparameters include learning rate, weight decay, warm-up steps, and total steps. A total of 1738 hyperparameter search experiments were conducted. The models were trained with an Adam optimizer with β₁ = 0.9 and β₂ = 0.999 and a linear warm-up and linear decay learning rate schedule. Gradient clipping was applied for training stability. Gradient accumulation was also applied when finetuning Clinical Longformer.

<figure class="table">
  <figcaption><strong>Table 1:</strong> Validation set results.</figcaption>
  <table>
    <thead>
      <tr><th>Model</th><th>F1 score</th></tr>
    </thead>
    <tbody>
      <tr><td><strong>Heuristic baseline</strong></td><td><strong>0.540</strong></td></tr>
      <tr><td>Clinical BioBERT (truncate)</td><td>0.716</td></tr>
      <tr><td>Clinical BioBERT (SW: mean)</td><td>0.699</td></tr>
      <tr><td>Clinical BioBERT (SW: max)</td><td>0.724</td></tr>
      <tr><td>Clinical BioBERT (SW: mean/max)</td><td>0.735</td></tr>
      <tr><td>Clinical BioBERT (SW: attention)</td><td>0.738</td></tr>
      <tr><td><strong>Clinical Longformer (no MLM)</strong></td><td><strong>0.887</strong></td></tr>
      <tr><td><strong>Clinical Longformer (+ MLM)</strong></td><td><strong>0.914</strong></td></tr>
      <tr><td>Prototypical Net (BioBERT)</td><td>0.384</td></tr>
      <tr><td>Prototypical Net (Longformer)</td><td>0.247</td></tr>
      <tr><td>Prototypical Net (Longformer + MLM)</td><td>0.218</td></tr>
    </tbody>
  </table>
</figure>

---

## 7 Conclusion

We have implemented and trained clinical indication classifiers using a heuristic approach and deep learning-based approaches. The best performing method leverages a semi-supervised approach in which a pretrained Clinical Longformer classifier is finetuned on the labeled dataset after being trained on the unlabeled dataset with masked language modeling. The resulting validation F1 score of this approach is **0.914**, beating the baseline by **0.374**. For future work, we suggest training Clinical Longformer without truncating the sequence to 1024 (up to 4096 tokens possible) and extending MLM pretraining duration.

---

### Acknowledgments

We would like to thank **Jan Witowski** for providing a clinical perspective to the problem, developing the code for the heuristic baseline, and labeling the data.

---

### Collaboration Statement

Given that we are working with private patient data, it is not possible to have members in this team without authorized access to the data. The process for gaining access to this data is long; hence this team has one team member. A medical postdoc from NYU Grossman School of Medicine, Jan Witowski, provided data labeling support. Note that Sam Bowman has already been notified about the circumstance and has approved it.

---

### References

{% bibliography --file references --cited %}

---

[^code-link]: Code for our work is available on GitHub: [https://github.com/erasromani/indication_classification](https://github.com/erasromani/indication_classification)

[^pubmed]: https://www.ncbi.nlm.nih.gov/pubmed

[^pmc]: https://www.ncbi.nlm.nih.gov/pmc
