# 📄 MBQ: Modality-Balanced Quantization for Large Vision-Language Models

## 🔗 Paper

* (추가 예정)

---

## 🧠 Problem

* 기존 PTQ 기법은 모든 토큰을 동일하게 취급
* 하지만 VLM에서는:

  * Vision Token
  * Language Token
    의 중요도가 다름

### 핵심 관찰

* Language token의 gradient가 Vision token보다 약 10배 이상 큼
* 즉,

  * Language token은 최종 loss에 민감
  * Vision token은 상대적으로 둔감

---

### 기존 PTQ의 문제

기존 Reconstruction Loss:

[
L = |Y - \hat{Y}|
]

* 모든 token에 동일한 가중치 부여
* 결과적으로:

  * 중요하지 않은 vision token의 error를 줄이는 데 과도한 자원 사용
  * 정작 중요한 language token의 quantization precision이 감소

즉,

> insensitive한 vision token을 over-emphasize 하는 문제가 발생

---

## 💡 Key Idea

### Modality-Balanced Quantization

* Vision token과 Language token의 중요도를 다르게 반영
* Reconstruction Loss를 gradient 기반으로 재가중

즉,

> "모든 token이 동일하지 않다"

---

### 핵심 아이디어

기존:

[
\text{Vision Error}
+
\text{Language Error}
]

MBQ:

[
G_v \cdot \text{Vision Error}
+
G_l \cdot \text{Language Error}
]

* (G_v): Vision gradient
* (G_l): Language gradient

Language token의 gradient가 크므로

* 중요한 language token error를 더 강하게 최적화
* 덜 중요한 vision token error는 상대적으로 완화

---

## 🏗️ Method

### 1. Modality Sensitivity Analysis

관측 결과:

* Language token gradient

  * 매우 큼
* Vision token gradient

  * 상대적으로 작음

이는

* Vision 데이터는 높은 redundancy
* 작은 변화에 robust

하기 때문

---

### 2. Channel-Wise Equalization (CWE)

SmoothQuant와 동일하게

[
Y = XW
]

를

[
Y = (X/E)(EW)
]

로 변환

---

#### 목적

Activation quantization difficulty를 Weight로 이동

---

기존 CWE:

* modality 구분 없음

MBQ:

* modality importance 반영

---

### 3. Modality-Balanced CWE

기존:

[
\min_E \text{Recon Error}
]

MBQ:

[
\min_E
\Big(
G_v \cdot L_{vision}
+
G_l \cdot L_{language}
\Big)
]

---

#### 특징

* 임의의 balancing coefficient 사용 X
* calibration 데이터에서 얻은 gradient 사용
* 자동으로 modality importance 반영

---

### 4. Weight-Activation Quantization (Prefill)

적용 단계:

* 이미지 인코딩
* Prompt 인코딩

특징:

* Matrix-Matrix 연산
* 연산량 매우 큼

따라서

* W4A4
* W8A8

등 Weight-Activation Quantization 적용

---

### 5. Weight-Only Quantization (Decode)

적용 단계:

* Token generation

특징:

* 한 번에 1 token 생성
* Matrix-Vector 연산

이 경우

* activation quantization overhead가 더 큼

따라서

* Weight-Only Quantization 적용

---

## 📊 Result

### Benchmarks

#### Models

* LLaVA-OneVision
* InternVL2
* Qwen2-VL

#### Tasks

* Image Captioning

---

### Weight-Only Quantization

Baseline:

* RTN
* AWQ
* GPTQ

MBQ가 우수한 성능 달성

---

### Weight-Activation Quantization

Baseline:

* RTN
* SmoothQuant

MBQ가 더 높은 정확도 유지

---

### Additional Findings

#### MAE vs MSE

* Gradient-aware balancing 적용 시
* MSE 기반 reconstruction이 효과적

---

#### Rotation-based Quantization

* Hadamard
* QuaRot 계열

과 결합 가능

* 추가 성능 향상 확인

---

## 🔥 Insight (중요)

* VLM에서는 modality마다 quantization sensitivity가 다름
* Vision token과 Language token을 동일하게 취급하면 비효율적
* Quantization은 단순히:

  * Weight
  * Activation
    을 맞추는 문제가 아니라

> "어떤 정보가 중요한가?"를 반영해야 하는 문제

---

### PTQ 흐름 관점

SmoothQuant:

* Channel imbalance 해결

OmniQuant:

* Learnable calibration

MBQ:

* Modality imbalance 해결

즉,

> PTQ가 단순 reconstruction에서 semantic importance-aware optimization으로 진화

---

## ❓ Question

* Gradient 외에 attention score를 importance로 사용할 수 있을까?
* Vision token 내부에서도 object token과 background token의 중요도가 다르지 않을까?
* VLA 환경에서는 Action token까지 포함한 modality balancing이 가능할까?
* MBQ와 QVLA의 action sensitivity를 결합할 수 있을까?
* Dynamic modality balancing은 가능할까?

---
