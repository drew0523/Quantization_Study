# 📄 QDrop: Randomly Dropping Quantization for Extremely Low-Bit Post-Training Quantization

## 🔗 Paper
- https://arxiv.org/abs/2203.05740

---

## 🧠 Problem
- 기존 PTQ는 weight quantization과 activation quantization을 **분리해서 처리**
- 즉, weight quantization 시 **activation quantization으로 인한 error를 고려하지 않음**
- 하지만 실제 inference에서는:
  - activation도 low-bit로 quantization됨
  - ⇒ weight는 activation noise까지 고려해서 최적화되어야 함
- 특히 저비트(2~4bit)에서 이 mismatch가 **성능 저하의 주요 원인**

---

## 💡 Key Idea
- Activation quantization error를 **weight optimization에 반영**
- Activation noise를 **weight perturbation으로 변환하여 해석**
- 이를 통해 **flat local minima (robust solution)**를 찾도록 유도
- 단, 항상 적용하면 overfitting 발생 →  
  **확률적으로 activation quantization을 적용하는 QDrop 제안**

---

## 🏗️ Method

1. **Activation Noise Modeling**
   - activation error를 \( e = a \cdot u \) 형태로 표현
   - quantized activation:
     - \( \hat{a} = a(1 + u) \)
   - activation noise를 명시적으로 objective에 포함

2. **Activation Noise → Weight Perturbation**
   - activation noise를 weight perturbation으로 변환
   - ⇒ activation quantization을 고려한 학습 =  
     **noise에 강건한 weight 학습**

3. **Flatness Optimization**
   - 기존 loss:
     - calibration set에 맞춘 sharp minimum
   - 제안 방식:
     - perturbation에도 안정적인 **flat minimum** 유도
   - ⇒ generalization 향상

4. **QDrop (Randomized Activation Quantization)**
   - activation quantization을 확률 \( p \)로 적용
     - \( \tilde{a} = a \) 또는 \( \hat{a} \)
   - 항상 적용 (Case2) → overfitting  
   - 일부만 적용 (QDrop) → robustness 확보

---

## 📊 Result
- 성능 비교:
  - Case1 (no act quant) < Case2 (full) < Case3 (partial) < **QDrop**
- 최적 확률:
  - \( p = 0.5 \)에서 가장 좋은 성능
- 적은 calibration data에서도 높은 성능 유지
- cross-domain calibration에서도 robust
- 특히 **extremely low-bit (2~4bit)**에서 큰 성능 향상

---

## 🔥 Insight (중요)
- PTQ에서 중요한 건 단순 quantization error가 아니라  
  **quantized inference 환경과의 일치**
- Activation noise를 고려하지 않으면 **train-test mismatch 발생**
- Quantization 문제를 **robust optimization / flat minima 문제**로 해석 가능
- QDrop은 quantization에 **dropout 개념을 도입한 것**
- 저비트 quantization에서는 **noise robustness가 핵심**

---

## ❓ Question
- optimal drop probability \( p \)는 모델/데이터마다 어떻게 달라지는가?
- activation noise modeling이 더 복잡한 구조에서도 유효한가?
- QDrop을 QAT에 적용하면 추가적인 성능 향상이 가능한가?
- weight perturbation 관점에서 다른 regularization과의 관계는?