# 📄 VLA Quantization Papers (Survey)

## 🔗 Papers
- SQAP-VLA
https://arxiv.org/pdf/2509.09090
- QVLA
https://arxiv.org/pdf/2602.03782
- QuantVLA
https://arxiv.org/pdf/2602.20309
- HBVLA
https://arxiv.org/pdf/2602.13710
- DyQ-VLA
https://arxiv.org/pdf/2603.07904
- DA-PTQ
https://arxiv.org/pdf/2604.11572

---

## 🧠 Problem
- VLA 모델은 **closed-loop 구조**로 인해 작은 quantization error가 시간에 따라 누적됨
- 기존 LLM/MLLM quantization 방식은:
  - **uniform precision**
  - **static assumption**
  - **internal feature reconstruction 중심**
- 하지만 VLA에서는:
  - error → action → next observation → error 증폭 (physical butterfly effect)
- 또한,
  - attention distortion
  - scale drift
  - kinematic drift
  - modality imbalance (vision vs language)
  등의 문제가 존재

---

## 💡 Key Idea
- VLA quantization은 기존과 다르게 다음 관점이 필요:
  1. **Action-space 중심 최적화**
  2. **Temporal / physical error propagation 고려**
  3. **Module / Channel / Time 단위의 non-uniform quantization**
  4. **Quantization + Pruning + Control dynamics 공동 설계**

---

## 🏗️ Method

### 1. SQAP-VLA (Quantization + Pruning Co-design)
- Hadamard transform → activation outlier smoothing
- Quantization-aware token pruning
- 로봇 좌표 기반 중요한 토큰 보호

---

### 2. QVLA (Action-aware Quantization)
- Jacobian 기반 action sensitivity 계산
- Channel-wise mixed precision + 0-bit pruning
- Greedy bit allocation

---

### 3. QuantVLA (Scale Calibration)
- DiT action head에서 발생하는 **scale drift 해결**
- ATM (Attention Temperature Matching)
- OHB (Output Head Balancing)
- selective quantization (DiT attention 제외)

---

### 4. HBVLA (1-bit Quantization)
- Policy-aware Hessian
- Haar transform + sparse orthogonal transform
- 중요한 weight vs 비중요 weight 분리

---

### 5. DyQ-VLA (Dynamic Quantization)
- 시간에 따라 activation bit dynamically 변경
- Motion fineness / angular jerk 기반 switching
- CPU-GPU 비동기 파이프라인

---

### 6. DA-PTQ (Drift-aware Quantization)
- Structural Jacobian으로 **trajectory drift modeling**
- DA-MPA: drift-aware bit allocation
- CSRC: cross-channel distribution 보정

---

## 📊 Result
- Memory: 최대 ~70% 이상 감소
- Speed: ~1.4~1.9x 가속
- Performance:
  - 대부분 **95~99% 성능 유지**
  - 일부는 baseline 초과

---

## 🔥 Insight (중요)
- ❗ VLA quantization의 본질:
  → "internal feature reconstruction" 문제가 아님  
  → **"physical action error control 문제"**

- 핵심 흐름:
  1. Layer → Channel → Action-space (QVLA)
  2. Static → Dynamic (DyQ-VLA)
  3. Numeric → Physical (DA-PTQ)
  4. Independent → Co-design (SQAP-VLA)

- 특히 중요한 포인트:
  - Action head는 매우 민감 (QuantVLA)
  - Vision / Language는 상대적으로 robust
  - Error는 시간 + 물리 구조를 따라 증폭됨

---

## ❓ Question
- Action-space sensitivity + Temporal drift를 동시에 고려한 unified framework 가능할까?
- Quantization + Control theory (MPC, dynamics) 결합 가능성?
- Sim2Real gap에서 quantization error는 어떻게 달라질까?
- Dynamic quantization + hardware scheduling 최적화 가능할까?

---