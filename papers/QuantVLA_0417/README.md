# 📄 QuantVLA: Scale-Calibrated Post-Training Quantization for Vision-Language-Action Models

## 🔗 Paper
- (추가 예정)

---

## 🧠 Problem
- VLA 모델에서 PTQ 적용 시,  
  **상위 VLM → DiT action head로 오차가 전파되며 증폭**
- 특히:
  - **Attention logit의 temperature drift**
  - **Residual stream energy mismatch**
- ⇒ 결과적으로 **Scale drift 발생 → 로봇 정책 붕괴**

### 핵심 원인
- VLA는:
  - Vision Encoder + LLM + DiT Action Head 구조
- Quantization error가:
  - 토큰 → attention → diffusion → action으로  
  **누적 및 증폭되는 구조**

---

## 💡 Key Idea
- Quantization error를 직접 줄이기보다,  
  **scale mismatch를 보정 (calibration)**
- 두 가지 보정 도입:
  - **ATM (Attention Temperature Matching)**
  - **OHB (Output Head Balancing)**
- Calibration 단계에서 계산 후,  
  **추론 시 dequant scale에 folding (no runtime overhead)**

---

## 🏗️ Method

1. **Selective Quantization Layout**
   - LLM:
     - 모든 Linear layer quantization
   - DiT Action Head:
     - MLP만 quantization
     - Attention (Q, K, V, O)는 FP 유지
   - ⇒ action sensitivity 고려한 구조적 설계

---

2. **Error Propagation Analysis**
   - Quantized output error는 다음으로 구성:
     1. VLM → Logit error
     2. Value weight quantization error
     3. Output weight quantization error
     4. 최종 output error
   - ⇒ 특히 **logit scale mismatch가 주요 원인**

---

3. **Attention Temperature Matching (ATM)**
   - 목적:
     - quantized attention logit의 scale 보정
   - 방법:
     - calibration set에서
       - FP vs Quantized logit의 **표준편차 비교**
     - scaling factor \( \alpha \) 계산
     - quantized logit에 곱해 보정
   - granularity:
     - **per-head**

---

4. **Output Head Balancing (OHB)**
   - 목적:
     - residual stream energy mismatch 보정
   - 방법:
     - layer 단위로 scale factor 계산
     - output에 곱해 보정
   - granularity:
     - **per-layer**

---

5. **Calibration & Folding**
   - ATM / OHB scale을 calibration 단계에서 계산
   - inference 시:
     - dequant scale에 **merge (folding)**
   - ⇒ 추가 연산 없음 (hardware-friendly)

---

## 📊 Result
- pi0.5 모델 기준:
  - Success rate: **97.6% (baseline 97.1% 초과)**
  - Memory: **4.27GB → 1.28GB (~70% 감소)**
- W4A8 / W4A4 환경에서 안정적 성능 유지
- LLM 전체 + DiT MLP만 quantization이 최적 trade-off

---

## 🔥 Insight (중요)
- VLA에서는 단순 quantization error보다  
  **scale mismatch (distribution shift)**가 더 치명적
- Quantization 문제를  
  **“error minimization” → “scale calibration” 문제로 전환**
- Attention logit과 residual stream은  
  **극도로 민감한 인터페이스**
- 매우 단순한 scalar 보정만으로도  
  **복잡한 구조 수정 없이 안정성 확보 가능**
- VLA quantization은:
  - 구조-aware
  - module-aware
  - scale-aware 해야 함

---

## ❓ Question
- ATM/OHB가 더 복잡한 action space에서도 동일하게 동작하는가?
- DiT attention까지 quantization하려면 어떤 보정이 필요할까?
- scale calibration을 dynamic하게 적용하면 더 좋아질까?
- VLA 구조 자체를 scale-invariant하게 설계할 수 있을까?