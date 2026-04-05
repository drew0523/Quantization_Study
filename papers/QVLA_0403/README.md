# 📄 QVLA: Not All Channels Are Equal in Vision-Language-Action Model’s Quantization

## 🔗 Paper
- https://arxiv.org/pdf/2602.03782

---

## 🧠 Problem
- VLA 모델은 크기가 매우 커서 **로봇에 직접 탑재하기 어려움**
  - 클라우드 의존 → latency, memory 문제 발생
- 기존 LLM/MLLM quantization 방식은 VLA에 그대로 적용 시 성능 저하 발생
- VLA는 action이 누적되며 **오차가 점점 증폭되는 구조**
- 또한, VLA는 다음 4개 모듈로 구성:
  - Vision Encoder
  - Language Model (Decoder)
  - Multi-modal Projection
  - Action Decoder
- 각 모듈, 나아가 **channel마다 action에 대한 민감도가 다름**
→ uniform precision으로는 최적 성능 달성 어려움

---

## 💡 Key Idea
- **Channel-wise mixed precision quantization** 적용
- 각 channel의 **action sensitivity 기반으로 bit-width를 다르게 할당**
- sensitivity가 낮은 channel부터 quantization 진행
- 일부 channel은 **0-bit (pruning)**까지 적용
- weight는 channel-wise mixed precision, activation은 uniform 8-bit 유지

---

## 🏗️ Method

1. **Sensitivity Analysis**
   - 모듈별 민감도:
     - Action Decoder / Projection > Language Model > Vision Encoder
   - channel 단위로 sensitivity 측정
   - sensitivity = action space에서의 quantization error
   - full episode 대신 **single timestep 기준으로 계산 (효율성 확보)**

2. **Efficient Sensitivity Estimation**
   - naive 방식: full forward pass 기반 MSE → 매우 높은 계산 비용
   - 해결:
     - 1차 Taylor expansion으로 sensitivity 근사
     - Jacobian 기반으로 계산:
       - sensitivity ≈ (quantization error) × (Jacobian)
   - coarse → fine 전략:
     - 1차 근사로 후보 선별
     - 일부 channel만 full forward pass로 정밀 계산

3. **Greedy Bit Allocation**
   - 초기: 모든 channel을 16-bit로 설정
   - 각 channel의 bit 감소 시 sensitivity 변화량 \( \rho_{l,c} \) 계산
   - min-heap 기반 greedy:
     - sensitivity 증가가 가장 작은 channel부터 bit 감소
   - 목표 memory budget까지 반복
   - 시간 복잡도: \( O(C \log C) \)

4. **Quantization Strategy**
   - Weight:
     - channel-wise mixed precision (16, 8, 4, 2, 0-bit)
   - Activation:
     - uniform 8-bit (hardware efficiency 고려)
   - Projector & Action head:
     - full precision 유지 (BF16)

5. **Calibration Dataset**
   - trajectory 데이터 (simulation + real-world)
   - (image, text, action) 쌍
   - random sampling으로 512개 구성
   - timestep 기반이라 서로 다른 episode 혼합 가능

---

## 📊 Result
- Weight-only / Weight-Activation quantization 모두에서
  - **최소 성능 저하 + 최소 메모리**
- Channel-wise > Layer-wise quantization 성능 우수
- 일부 channel을 **0-bit (pruning)** 하는 것이 2-bit보다 더 효과적인 경우 존재
- Mixed Precision > Uniform Precision
- 속도는 SOTA는 아니지만, **효율-성능 trade-off 우수**

---

## 🔥 Insight (중요)
- VLA에서는 단순 weight error가 아니라 **action-level sensitivity가 핵심**
- Quantization granularity는 **layer가 아니라 channel 수준이 적절**
- 일부 channel은 거의 중요하지 않기 때문에 **pruning이 효과적**
- Activation은 dynamic 특성 때문에 uniform bit가 hardware-friendly
- Quantization을 **model 구조 + task 관점에서 jointly 고려해야 함**
- 기존 PTQ/LLM quantization 방식은 VLA에 그대로 적용 불가능

---

## ❓ Question
- channel 간 dependency는 무시해도 충분한가?
- timestep 기반 sensitivity가 장기 task에서도 항상 유효한가?
- activation도 channel-wise로 하면 성능이 더 좋아질까? (hardware trade-off 제외)
- VLA 구조 자체를 quantization-friendly하게 바꿀 수 있을까?