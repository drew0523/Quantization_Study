# 📄 SmoothQuant: Accurate and Efficient Post-Training Quantization for Large Language Models

## 🔗 Paper
- https://arxiv.org/abs/2211.10438

---

## 🧠 Problem
- LLM은 매우 큰 메모리 요구량
  - 예: GPT-3 175B → 약 350GB VRAM 필요
- INT8 quantization으로 memory 절감 가능하지만:
  - **Activation quantization에서 심각한 성능 저하 발생**

### 핵심 원인
- Activation은 dynamic하며, 특정 channel에서 **outlier 값 존재**
- 특징:
  - Intra-channel variance ↓
  - Inter-channel variance ↑
- ⇒ 일부 channel의 큰 값 때문에 scaling 시  
  **다른 channel 정보가 collapse → quantization error 증가**

### 기존 해결 (LLM.int8)
- outlier channel만 FP16 처리
- ❌ 문제:
  - hardware 비효율 (branching 발생)
  - latency 증가

---

## 💡 Key Idea
- Activation quantization의 어려움을  
  **Weight quantization으로 분산 (shift)**

- 방법:
  - Activation을 줄이고 (divide)
  - Weight를 키워서 (multiply)
  - ⇒ **출력은 동일하게 유지하면서 outlier 제거**

- 즉,
  - Activation을 **smooth하게 만들고**
  - quantization difficulty를 **activation ↔ weight 간 분담**

---

## 🏗️ Method

1. **Smoothing Transformation**
   - 기존:
     - \( Y = XW \)
   - 변환:
     - \( Y = (X / s)(W \cdot s) \)
   - activation은 작아지고, weight는 커짐
   - ⇒ 전체 output은 동일

---

2. **Smoothing Factor (s)**
   - 단순히 activation max 기준:
     - ❌ weight에 부담 집중
   - 해결:
     - activation과 weight 모두 고려
   - 공식:
     - \( s_j = \frac{(\max |X_j|)^\alpha}{(\max |W_j|)^{1-\alpha}} \)
   - \( \alpha \): hyperparameter (default = 0.5)

---

3. **Offline Calibration**
   - smoothing factor는 calibration set으로 사전 계산
   - inference 시 추가 연산 없음
   - ⇒ hardware-friendly

---

4. **Effect**
   - Activation:
     - channel 간 outlier 감소
     - distribution 안정화
   - Weight:
     - variance 증가하지만 여전히 안정적
   - ⇒ **둘 다 quantization-friendly 상태로 변환**

---

## 📊 Result
- INT8 only로도 SOTA 수준 성능 달성
- LLM.int8() 대비:
  - 더 빠름 (no branching)
  - memory 효율 ↑
  - latency ↓
- Activation channel-wise quantization 수준의 성능을  
  **hardware-friendly하게 근사**

---

## 🔥 Insight (중요)
- LLM quantization의 본질적인 문제는  
  **activation outlier (channel imbalance)**
- Activation quantization 문제를 직접 해결하려 하지 말고  
  **weight로 분산시키는 것이 핵심 아이디어**
- Quantization은 단순히 값 줄이는 문제가 아니라  
  **분포를 재구성하는 문제**
- SmoothQuant는  
  → “Quantization difficulty redistribution” 접근
- Hardware efficiency까지 고려한 **practical PTQ 방법**

---

## ❓ Question
- optimal \( \alpha \)는 모델마다 어떻게 달라지는가?
- INT4 이하에서도 동일한 효과를 낼 수 있는가?
- Transformer 구조 외에도 일반 CNN/ViT에 동일하게 적용 가능한가?
- activation outlier 자체를 줄이는 방향 (training-level)과의 관계는?