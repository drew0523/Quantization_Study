# 📄 OmniQuant: Omnidirectionally Calibrated Quantization for Large Language Models

## 🔗 Paper

* https://arxiv.org/abs/2308.13137

---

## 🧠 Problem

* 기존 PTQ는:

  1. Scaling factor, zero-point 등을 heuristic / handcrafted 방식으로 계산
  2. Low-bit(W2, W3, W4) 환경에서 성능 급락
* 반면 AdaRound, BRECQ 같은 optimization 기반 PTQ는:

  * 모든 weight에 대해 gradient optimization 수행
  * 계산 비용 및 시간이 매우 큼

### 핵심 Trade-off

* PTQ:

  * 빠르고 효율적
  * 하지만 저비트 성능 한계
* QAT:

  * 높은 성능
  * 하지만 매우 비싼 재학습 비용

⇒ **PTQ 효율성과 QAT 수준 성능을 동시에 달성할 수 있을까?**

---

## 💡 Key Idea

* 모든 weight를 최적화하지 말고,
  **Quantization-friendly 하도록 만드는 소수의 파라미터만 학습**
* 두 가지 핵심 모듈:

  1. **LWC (Learnable Weight Clipping)**
  2. **LET (Learnable Equivalent Transformation)**

즉,

* Weight clipping 범위
* Activation smoothing / shifting

만 gradient optimization으로 학습하여,
전체 weight 재학습 없이 저비트 성능을 크게 향상

---

## 🏗️ Method

### 1. Learnable Weight Clipping (LWC)

#### 목적

* Weight quantization error 감소

#### 방법

* Weight clipping threshold를 학습
* 기존:

  * manual clipping
* OmniQuant:

  * clipping range를 gradient 기반으로 optimization

#### 학습 파라미터

* ( \gamma, \beta )

#### 특징

* Low-bit quantization에서 효과적
* Dynamic range를 adaptive하게 조정

---

### 2. Learnable Equivalent Transformation (LET)

#### 목적

* Activation outlier 문제 해결

#### 핵심 아이디어

SmoothQuant와 유사하게:

* Activation difficulty를 Weight로 이동

기존:
[
Y = XW
]

변환:
[
Y = (X/s)(Ws)
]

하지만 OmniQuant는:

* 단순 smoothing factor가 아니라
* learnable scaling + shifting 적용

#### 학습 파라미터

* ( s, s_a, \delta )

#### 특징

* Activation distribution을 quantization-friendly하게 변환
* Weight와 Activation 간 quantization difficulty를 균형 분배

---

### 3. Block-wise Error Minimization

#### 목적

* 전체 모델 optimization cost 감소

#### 방법

* Transformer block 단위로 순차 최적화
* Calibration data 기반 reconstruction loss 최소화

#### 특징

* 전체 weight optimization 불필요
* PTQ 수준 효율 유지

---

### 4. Attention-aware Equivalent Transformation

#### 목적

* Attention Q/K/V 연산까지 LET 적용

#### 특징

* KV-cache quantization 가능
* Large-scale LLM inference 최적화

---

### 5. Parameter Fusion

학습된:

* scaling
* shifting
* clipping

파라미터들을 inference 전에 기존 weight에 fold/fusion

⇒ runtime overhead 없음

---

## 📊 Result

### Weight-only Quantization

* GPTQ, AWQ 대비 우수
* 특히 W2A16, W3A16에서 큰 성능 향상

예시:

* LLaMA-13B W2A16:

  * GPTQ: 5500+
  * OmniQuant: 13.21 perplexity

---

### Weight-Activation Quantization

* W4A4 환경에서도 SOTA 성능

예시:

* LLaMA-7B W4A4:

  * SmoothQuant: 38.41%
  * OmniQuant: 52.65%

---

### Hardware Efficiency

* Weight memory 약 70% 감소
* 추론 속도 약 2배 향상
* FP16으로 불가능한 대형 모델도 단일 GPU 실행 가능

---

## 🔥 Insight (중요)

* Quantization의 핵심은:
  → weight를 직접 최적화하는 것이 아니라
  → **distribution을 quantization-friendly하게 변환하는 것**

* SmoothQuant:

  * heuristic smoothing

* OmniQuant:

  * smoothing 자체를 learnable parameter로 optimization

* QAT 수준 성능을 달성하면서:

  * PTQ 효율 유지
  * Runtime overhead 없음

* 즉,
  **“Learnable calibration”이 PTQ의 핵심 방향임을 제시**

---

## ❓ Question

* LET가 activation outlier를 완전히 해결할 수 있는가?
* Dynamic activation distribution에서도 안정적인가?
* VLA / diffusion policy 같은 physical AI 모델에도 동일하게 적용 가능할까?
* Channel-wise / token-wise LET로 확장 가능할까?
* OmniQuant와 QDrop(flat minima)을 결합하면 어떨까?

---
