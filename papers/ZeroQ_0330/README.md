# 📄 ZeroQ: A Novel Zero Shot Quantization Framework

## 🔗 Paper
- https://arxiv.org/abs/2001.00281

---

## 🧠 Problem
- 기존 PTQ는 calibration을 위해 **데이터셋이 필요**
- 하지만 실제 환경에서는:
  - 데이터가 private이거나 접근 불가능한 경우 존재
- ⇒ **데이터 없이 quantization을 수행하는 방법 필요**

---

## 💡 Key Idea
- 실제 데이터 대신, **Batch Normalization 통계(mean, variance)**를 활용하여  
  **synthetic input (distilled data)** 생성
- 이 데이터가 **원래 데이터의 sensitivity를 근사**하도록 학습
- 이를 통해 **zero-shot PTQ (데이터 없이 quantization)** 수행
- Mixed Precision은 **Pareto Frontier 기반 탐색**으로 효율적으로 해결

---

## 🏗️ Method

1. **Distilled Data Generation**
   - 각 layer의 activation 분포를 맞추기 위해 synthetic input \( X^r \) 생성
   - 모든 BN layer의 mean, variance를 기준으로 학습
   - ⇒ 모델 내부 통계와 일치하도록 데이터 생성
   - 약 32개의 synthetic sample 생성

2. **Activation Range Estimation**
   - 생성된 데이터로 각 layer의 activation range 추정
   - quantization 시 발생하는 layer별 error 분석 가능

3. **Mixed Precision via Pareto Frontier**
   - 전체 bit 조합 탐색 시 경우의 수가 매우 큼 (\( \sim 7.2 \times 10^{23} \))
   - Pareto Frontier를 활용하여:
     - model size constraint 만족
     - sensitivity 최소화
   - ⇒ \( O(mL) \) 시간으로 효율적 탐색

---

## 📊 Result
- 실제 데이터 없이도 기존 PTQ 대비 경쟁력 있는 성능 달성
- synthetic data 기반 quantization 가능성 입증
- Mixed Precision 적용 시 성능 추가 향상

---

## 🔥 Insight (중요)
- BN statistics만으로도 **데이터 분포를 근사할 수 있음**
- Quantization에서 중요한 것은 **실제 데이터가 아니라 activation distribution**
- Distilled data는 일종의 **model-driven data generation**
- ZeroQ는 quantization을 **data-dependent → model-dependent 문제로 전환**

---

## ❓ Question
- BN이 없는 구조 (LayerNorm, Transformer)에서는 어떻게 적용할 수 있는가?
- 생성된 데이터가 실제 데이터 distribution을 얼마나 잘 근사하는가?
- synthetic data의 개수 (32개)가 충분한가?
- 더 복잡한 task에서도 동일하게 동작할까?