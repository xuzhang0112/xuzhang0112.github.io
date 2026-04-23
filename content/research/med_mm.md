---
Title: "Medical Multimodal"

showTableOfContents: true
---


## Dataset

### MIMIC-CXR

377,110 images, 227,835 reports

### NIH-CXR

112,120 X-ray images from 30,805 unique patients, with labels of 14 diseases

The labels are expected to be >90% accurate and suitable for weakly-supervised learning

### CheXpert

224,316 images of 65,240 patients, labels of 14 diseases: positive, negative, uncertain

Train: all labeled by CheXpert Labeler

Validation: 200, labeled by experts

Test: 500, labeled by experts

### PadChest

- 160,000+ images
  - from 67,000 patients, from 2009 to 2017
- reports 
  - labeled with 174 different radiographic findings, 19 differential diagnoses and 104 anatomic locations
  - 27% were manually annotated and the remaining set was labeled by RNN (0.93 Micro-F1 score)

### RadGraph

structure clinical information in a radiology report with 4 entities and 3 relations, with MIMIC-CXR and CheXpert

https://physionet.org/content/radgraph/1.0.0/

- Annotations: 500 for train, 100 for test
  - train.json: File containing annotations obtained by board-certified radiologists for 425 radiology reports (MIMIC-CXR)
  - dev.json: File containing annotations obtained by board-certified radiologists for 75 radiology reports (MIMIC-CXR)
  - test.json: File containing annotations obtained by 2 different board-certified radiologists for 100 radiology reports (50 MIMIC-CXR, 50 CheXpert)
- Graphs
  - MIMIC-CXR_graphs.json: File containing annotations obtained by our deep learning model for 220,763 radiology reports (MIMIC-CXR)
  - CheXpert_graphs.json: File containing annotations obtained by our deep learning model for 500 radiology reports (CheXpert)
- Models
  - models/model_checkpoint: Folder containing the saved model parameters used to automatically generate the annotations in MIMIC-CXR_graphs.json and CheXpert_graphs.json
  - models/README.txt: File containing instructions for performing inference using model checkpoint
  - models/inference.py: File containing code to extract entities and relations given a directory containing reports in txt format

### Chest ImaGenome

- scene graph to describe 242,072 images
- bounding boxes for 29 unique anatomical regions in the chest
- sentences describing each region if they exist in the corresponding radiology report


## Chest Disease

| 疾病               | 中文名   | 症状                                   | 归纳 |
| ------------------ | -------- | -------------------------------------- | ---- |
| Cardiomegaly       | 心脏肥大 |                                        | 大小 |
| Mass               | 肿块     |                                        |      |
| Nodule             | 结节     |                                        |      |
| Atelectasis        | 肺不张   | 一边没气，心脏也跟着挤过来             | 偏移 |
| Pneumothorax       | 气胸     | 有气，肺被挤压                         |      |
| Effusion           | 积液     | 膈角很容易被填满，可能把心脏挤到另一边 |      |
| Pneumonia          | 肺炎     |                                        |      |
| Infiltrastion      | 浸润     |                                        |      |
| Edema              | 水肿     |                                        |      |
| Emphysema          | 肺气肿   |                                        |      |
| Fibrosis           | 纤维化   |                                        |      |
| Pleural Thickening | 胸膜增厚 |                                        |      |
| Hernia             | 疝       |                                        |      |
| Consolidation      | 肺实变   |                                        |      |
