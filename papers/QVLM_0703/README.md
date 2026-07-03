# 📄 QVLM: Mining Cross-layer Dependency for Large Vision-Language Model Quantization

## 🔗 Paper
- NeurIPS 2024
- (논문 링크 추가)

---

## 🧠 Problem

- 기존 LVLM PTQ는 layer-wise rounding optimization을 수행
- Layer별 독립적으로 최적화하기 때문에 **Cross-layer dependency**를 고려하지 못함
- 모든 layer를 동시에 최적화하는 것은 NP-hard(2^L) 문제로 계산량이 매우 큼
- Layer-wise와 Global optimization 사이의 trade-off를 해결할 필요가 있음

---

## 💡 Key Idea

- Layer 간 dependency를 고려하는 **Block-wise Reconstruction** 제안
- Cross-layer dependency를 직접 계산하지 않고 **Activation Information Entropy**를 proxy로 사용
- Visual Encoder를 Calibration data만으로 Quantization-friendly하도록 Finetuning하여 activation distribution을 개선
- Dependency 기반 Block partition과 Visual Encoder Optimization(VEO)을 함께 수행

---

## 🏗️ Method

### 1. Mining Cross-layer Dependency (CDM)

- Activation Information Entropy를 이용하여 layer 간 dependency를 추정
- Information Entropy와 실제 discretization error difference가 높은 상관관계를 가짐
- Layer 간 dependency가 큰 layer들을 하나의 Block으로 구성
- Block 내부는 Joint Rounding Search 수행

---

### 2. Block-wise Reconstruction

- 기존 Layer-wise Reconstruction 대신 Block의 최종 output reconstruction 수행
- Block 내부 layer들의 dependency를 함께 고려하여 rounding function 최적화
- Average dependency가 threshold 이상일 경우 block 생성
- Maximum block depth를 제한하여 계산량 제어

---

### 3. Visual Encoder Optimization (VEO)

Calibration set만을 이용하여 Visual Encoder를 미세조정

Optimization Loss

- Auto-regressive Loss
- Entropy Loss
  - Global sensitivity
  - Local dependency
- Quantization Reconstruction Loss

즉,

- Visual Encoder가 Quantization-friendly한 activation distribution을 생성하도록 학습

---

## 📊 Result

- 기존 AWQ, QLoRA보다 높은 정확도 달성
- W6A6, W4A4 환경 모두에서 SOTA 성능
- Block depth는 3일 때 가장 좋은 Accuracy-Search Cost trade-off
- Cross-layer Dependency Mining(CDM)과 Visual Encoder Optimization(VEO)을 함께 사용할 때 가장 높은 성능 달성

---

## 🔥 Insight

- LVLM에서는 Layer들이 서로 강하게 연결되어 있어 Layer-wise PTQ만으로는 한계가 존재
- Activation Information Entropy가 Cross-layer Dependency를 매우 잘 근사하는 proxy로 사용 가능
- Visual Encoder의 activation distribution을 calibration data만으로 수정해도 양자화 성능이 크게 향상됨
- 단순 Weight Reconstruction보다 **Dependency-aware Block Reconstruction**이 훨씬 효과적임

---

## ❓ Question

- Information Entropy 외에 더 정확한 Dependency proxy는 존재할까?
- Block partition을 학습 기반(Graph, Clustering)으로 구성하면 더 좋아질까?
- Vision Encoder뿐 아니라 LLM Backbone도 함께 Optimization하면 추가 성능 향상이 가능할까?
- Block 구성 시 modality(vision/language)를 함께 고려하면 LVLM에 더 적합한 PTQ가 가능하지 않을까?