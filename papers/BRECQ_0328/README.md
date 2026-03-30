# 📄 BRECQ: Pushing the Limit of Post-Training Quantization by Block Reconstruction

## 🔗 Paper
- https://arxiv.org/abs/2102.05426

---

## 🧠 Problem
- 기존 PTQ는 parameter 공간에서의 근사 (quantization error 최소화)에 집중하여, 실제 **task 성능을 보장하지 못함**
- Task loss 기반 최적화는 QAT에서는 가능하지만, PTQ에서는 계산 비용 문제로 직접 적용이 어려움
- AdaRound는 Hessian을 단순 \( C \times I \)로 근사하여 **cross-layer dependency를 무시**
- 이로 인해 저비트(특히 4bit 이하)에서 **layer 간 누적 오차가 커지는 문제 (Second-order problem)** 발생

---

## 💡 Key Idea
- Task loss를 직접 최소화하는 대신, **loss 증가를 최소화하는 방향으로 PTQ 수행**
- 기존의 \( w^T H w \) (weight space) 대신, 이를 **출력 공간의 \( z^T H z \)**로 변환하여 효율적으로 최적화
- **Block-wise reconstruction**을 통해 layer 간 dependency와 generalization 간의 trade-off 해결
- Hessian을 Fisher Information Matrix(FIM) 기반으로 근사하여 **각 weight의 중요도 반영**
- Quantization을 **network distillation 관점 (FP → LP)**으로 해석

---

## 🏗️ Method

1. **Cross-Layer Dependency Modeling**
   - Hessian을 weight 기준이 아닌 **output 기준으로 재정의**
   - \( w^T H w \rightarrow z^T H z \) 변환
   - 계산량 감소 + task-relevant 정보 반영

2. **Block Reconstruction**
   - layer-wise: generalization ↑, dependency ↓  
   - full-network: dependency ↑, overfitting ↑  
   - ⇒ **block-wise로 trade-off 해결**
   - intra-block dependency는 유지, inter-block dependency는 무시

3. **Hessian Approximation (FIM 기반)**
   - 기존 AdaRound: \( C \times I \) (모든 weight 동일 취급)
   - BRECQ: **FIM 기반 diagonal matrix** 사용
   - → weight별 민감도 반영 가능

4. **Mixed Precision Optimization**
   - 각 block별 bit-width에 따른 loss를 미리 계산 (Look-Up Table)
   - 유전 알고리즘으로 latency/size constraint 하에서 최적 조합 탐색
   - 기존 \( O(3^n) \) → 매우 빠른 탐색 가능

---

## 📊 Result
- 특히 **저비트 (2-bit)**에서 기존 방법 대비 큰 성능 향상
- Block-wise reconstruction이 layer-wise / full reconstruction보다 우수
- 다양한 setting에서 기존 PTQ 방법들 (AdaRound 등)을 모두 outperform
- **Mixed Precision > Uniform Precision**

---

## 🔥 Insight (중요)
- PTQ의 핵심 문제는 **“weight space 최적화 vs task 성능 불일치”**
- 이를 해결하기 위해 **output space 기반 최적화 (zHz)**가 중요
- **Layer 독립 가정은 저비트에서 무너진다 → dependency modeling 필수**
- Block-wise 구조는 **regularization vs dependency trade-off의 핵심 해법**
- Quantization은 단순 approximation이 아니라 **distillation 문제로 볼 수 있음**

---

## ❓ Question
- block size 선택 기준은 어떻게 정해지는가?
- activation quantization에서도 동일한 Hessian approximation이 항상 유효한가?
- FIM 기반 근사가 매우 작은 calibration set에서도 안정적인가?
- block 간 dependency를 완전히 무시하는 것이 최적일까?