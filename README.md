# 📚 Quantization Study

> 가벼운녀석들 Quantization 스터디 기록 저장소

---

## 🎯 Goal
- Quantization 이론 이해
- PTQ / QAT 구조 파악
- 최신 연구 (LSQ, GPTQ, AWQ) 이해

---

## 🗂️ Structure
papers/

├── [paper_name]/

│ ├── note.md # 논문 정리

│ └── summary.pdf # (선택) 개인 정리 자료


---

## 📖 Paper List

### 🔹 Basics
- [ ] PTQ Basics  
- [ ] QAT Basics  

### 🔹 Advanced
- [ ] LSQ  
- [ ] GPTQ  
- [ ] AWQ  

---

## 🧠 Study Notes

| Paper | Idea | Link |
|------|------|------|
| LSQ | learnable scale | [note](papers/LSQ/note.md) |
| GPTQ | post-training compression | [note](papers/GPTQ/note.md) |

---

## 🔗 Paper Links

| Paper | Link |
|------|------|
| LSQ | [arXiv 링크] |
| GPTQ | [arXiv 링크] |

---

## 🔥 Key Concepts

- **PTQ**: 학습 없이 quantization
- **QAT**: quantization-aware training
- **LSQ**: scale을 학습

---
## 📝 Convention

### 📌 디렉토리 구조
- 논문 1개 = 폴더 1개

### 📌 파일 구성
- `note.md`: 필수
- `summary.pdf`: 선택

---

## 🧾 Note Template

각 논문 폴더의 `note.md`는 아래 형식을 따름:

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
---

## 🚀 Progress

- [x] repo 세팅
- [ ] PTQ 이해
- [ ] QAT 이해
- [ ] LSQ 분석

---
