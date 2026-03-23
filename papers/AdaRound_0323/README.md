# 📄 Up or Down? Adaptive Rounding for Post-Training Quantization

## 🔗 Paper
- https://arxiv.org/abs/2004.10568

---

## 🧠 Problem
- 기존 Post-Training Quantization(PTQ)에서는 **round-to-nearest (가장 가까운 값으로 반올림)**을 사용
- 하지만 이 방식은 **항상 최적의 선택이 아닐 수 있음**
- 특히, weight 간의 상호작용(비대각 성분, off-diagonal term)을 고려하지 못함
- 결과적으로, 단순 rounding은 전체 quantization error를 최소화하지 못함

---

## 💡 Key Idea
- 각 weight를 단순히 반올림하지 않고,  
  👉 **올릴지(up) 내릴지(down)를 adaptive하게 결정**
- 이를 통해 weight 간 상호작용까지 고려한 **전체 loss 최소화**
- discrete optimization 문제를 **continuous optimization**으로 변환하여 해결

---

## 🏗️ Method
1. **문제 정의 (Adaptive Rounding)**
   - Quantization error를 최소화하기 위해  
     각 weight에 대해 **올림/내림 선택 (binary decision)** 수행
   - 이를 통해 최적의 rounding 조합 탐색

2. **Hessian 기반 최적화 → MSE 근사**
   - 원래 objective: \( w^T H w \)
   - 하지만 Hessian 계산은 매우 expensive
   - → **Diagonal approximation** 적용
   - → 문제를 **MSE 형태로 단순화**
   - → layer-wise optimization 가능

3. **QUBO → Continuous Optimization**
   - binary rounding 문제는 QUBO (NP-hard)
   - → 해결하기 위해  
     discrete 변수 대신 **continuous variable \( V \)** 도입
   - \( h(V) \)를 통해 rounding 방향 결정 (soft decision)

4. **Regularization**
   - \( h(V) \)가 결국 0 또는 1로 수렴하도록 유도
   - temperature-like parameter \( \beta \) 사용
     - 초기: 큰 값 → 탐색 자유롭게
     - 후반: 작은 값 → binary 결정 유도

5. **Asymmetric Reconstruction**
   - 현재 layer만 최적화하지만,
   - 입력은 이미 quantized된 이전 layer의 output 사용
   - → 실제 inference 환경을 반영한 최적화 수행

---

## 📊 Result
- 기존 round-to-nearest 대비 **quantization error 감소**
- 특히 low-bit (e.g., 4-bit) 환경에서 성능 향상
- 추가 데이터 없이 **calibration set만으로도 효과적인 PTQ 가능**

---

## 🔥 Insight (중요)
- 단순히 각 weight를 독립적으로 rounding하는 것은 최적이 아님
- **weight 간 상호작용 (off-diagonal term)**이 중요
- rounding 자체를 optimization 문제로 바라보는 관점이 핵심
- NP-hard 문제를 continuous relaxation으로 푼 것이 매우 practical
- “quantization = discretization”이 아니라  
  👉 “quantization = optimization”으로 재해석한 논문

---

## ❓ Question
- Diagonal Hessian approximation이 실제로 얼마나 정확한가?
- layer-wise optimization이 global optimum과 얼마나 차이가 나는가?
- calibration set의 선택이 AdaRound 성능에 미치는 영향은?
- QAT와 결합하면 얼마나 추가적인 이득이 있을까?