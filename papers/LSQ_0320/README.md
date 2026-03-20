# 📄 LSQ (Learned Step Size Quantization)

## 🔗 Paper
- https://arxiv.org/abs/1902.08153

---

## 📂 Files
- `LSQ.pdf`: 논문 정리 자료

---

## 기존 방식
- step size를 데이터의 분포나 경험적인 규칙에 의존
- 이는 quantization error를 줄이는데에만 주목할 뿐, 과제 성능과 꼭 일치하지는 않는다.

---

## 🧠 핵심 요약
- Quantization에서 scale(step size)을 **고정하지 않고 학습**
- 기존 방식보다 성능 저하를 줄임
- QAT(Quantization Aware Training) 기반 방법

---

## 💡 Key Idea
- Transition point에서 민감한 Straight Through Estimator(STE) 사용
- gradient scale parameter를 통해 weight와 step을 균형 있게 gradient로 업데이트
- Quantization Error가 아닌 Task Loss에 집중

---

## 🔥 Insight
- quantization 성능의 핵심은 scale
- 좋은 scale은 manual하게 정하지 말고 학습하자
- 학습을 위한 gradient approximation 설계가 중요

---

## ❓ Question
 - ...