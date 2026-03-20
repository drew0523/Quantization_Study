# 📚 Quantization Study

> 가벼운녀석들 Quantization 스터디 기록 저장소

---

## 🎯 Goal
- Quantization 전체 흐름 이해
- PTQ / QAT 기반 방법 정리
- 다양한 quantization 기법 비교 및 분석

---

## 🗂️ Structure
```
papers/
├── PTQ/
│ ├── [paper_name]/
│ │ ├── note.md
│ │ └── summary.pdf
│
├── QAT/
│ ├── [paper_name]/
│ │ ├── note.md
│ │ └── summary.pdf
```
---

## 📖 Study Map

### 🔹 PTQ (Post-Training Quantization)

| Paper | Idea | Note | PDF |
|------|------|------|-----|
| Quantization Survey | overview | [note](papers/PTQ/survey/note.md) | [pdf](papers/PTQ/survey/Quant_Survey_Paper.pdf) |
| PTQ Guide | calibration | [note](papers/PTQ/ptq_guide/note.md) | [pdf](papers/PTQ/ptq_guide/quantization_ptq_guide.pdf) |

---

### 🔹 QAT (Quantization-Aware Training)

| Paper | Idea | Note | PDF |
|------|------|------|-----|
| LSQ | learnable scale | [note](papers/QAT/LSQ_0320/README.md) | [pdf](papers/QAT/LSQ_0320/LSQ.pdf) |

---

## 🔥 Key Concepts

### 📌 PTQ
- 학습 없이 quantization 수행
- calibration을 통해 scale 결정
- 빠르지만 성능 저하 가능

### 📌 QAT
- quantization을 학습 과정에 포함
- 성능 유지에 유리
- 학습 비용 증가

---

## 📝 Convention

### 📌 디렉토리 구조
- PTQ / QAT 기준으로 분류
- 논문 1개 = 폴더 1개

### 📌 파일 구성
- `note.md` 또는 `README.md`: 필수
- `summary.pdf`: 선택

---

## 🧾 Note Template

각 논문 폴더의 정리는 아래 형식을 따름:

```md
# 📄 [Paper Title]

## 🔗 Paper
- [arXiv 링크]

---

## 🧠 Problem
- 

---

## 💡 Key Idea
- 

---

## 🏗️ Method
1. 
2. 
3. 

---

## 📊 Result
- 

---

## 🔥 Insight (중요)
- 

---

## ❓ Question
- 
