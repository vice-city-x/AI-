# 반려동물 피부질환 이미지 분류 프로젝트

## 1. 프로젝트 개요

본 프로젝트는 반려견과 반려묘의 피부질환 이미지를 기반으로 질환 클래스를 자동 분류하는 딥러닝 모델을 개발하는 것을 목표로 한다.

AI Hub에서 제공하는 반려동물 피부질환 데이터를 활용하였으며, 이미지 전처리, 데이터 정제, 데이터셋 확장 과정을 거쳐 모델 학습에 적합한 데이터셋을 구축하였다.

최종 모델은 총 9개 클래스를 분류한다.

* 반려견 피부질환 6개 클래스
* 반려묘 피부질환 3개 클래스

---

## 2. 사용 데이터셋

### 데이터 출처

* 데이터명 : AI Hub 반려동물 피부질환 데이터
* 데이터셋 번호 : 561
* 제공기관 : AI Hub
* 데이터 유형 : 반려견 및 반려묘 피부질환 이미지

데이터셋은 용량 및 라이선스 문제로 저장소에 포함하지 않는다.
재현을 원하는 경우 AI Hub에서 동일 데이터셋을 다운로드한 뒤, 아래 폴더 구조에 맞게 배치해야 한다.

---

## 3. 클래스 구성

### 반려묘 클래스

| 클래스  | 질환명              |
| ---- | ---------------- |
| C_A2 | 비듬 / 각질 / 상피성잔고리 |
| C_A4 | 농포 / 여드름         |
| C_A6 | 결절 / 종괴          |

### 반려견 클래스

| 클래스  | 질환명              |
| ---- | ---------------- |
| D_A1 | 구진 / 플라크         |
| D_A2 | 비듬 / 각질 / 상피성잔고리 |
| D_A3 | 태선화 / 과다색소침착     |
| D_A4 | 농포 / 여드름         |
| D_A5 | 미란 / 궤양          |
| D_A6 | 결절 / 종괴          |

총 9개 클래스

---

## 4. 데이터셋 구성

### 기존 데이터셋

| 구분         |     수량 |
| ---------- | -----: |
| 반려견 6개 클래스 | 6,000장 |
| 반려묘 3개 클래스 | 2,499장 |
| 전체         | 8,499장 |

| Split      | Images |
| ---------- | -----: |
| Train      |  6,792 |
| Validation |    843 |
| Test       |    864 |

---

### 최종 데이터셋

| Class | Train | Validation | Test |
| ----- | ----: | ---------: | ---: |
| C_A2  |  1776 |        221 |  224 |
| C_A4  |  1440 |        175 |  170 |
| C_A6  |  1535 |        193 |  198 |
| D_A1  |   799 |         99 |  102 |
| D_A2  |  1766 |        218 |  225 |
| D_A3  |  1774 |        219 |  227 |
| D_A4  |  1769 |        218 |  223 |
| D_A5  |  1764 |        216 |  223 |
| D_A6  |  1770 |        218 |  226 |

| Split      | Images |
| ---------- | -----: |
| Train      | 14,393 |
| Validation |  1,777 |
| Test       |  1,818 |
| Total      | 17,988 |

---

## 5. 데이터 전처리

본 프로젝트에서는 원본 이미지를 학습에 적합한 형태로 변환하기 위해 별도의 전처리 코드를 사용하였다.

### 전처리 입력 및 출력 경로

```python
INPUT_DIR = r"C:\Users\Konyang\Desktop\cropped_dataset_more"
OUTPUT_DIR = r"C:\Users\Konyang\Desktop\cropped_preprocessed_dataset"
```

### 적용한 전처리 과정

1. Resize

   * 모든 이미지를 320 × 320 크기로 변환

2. Gaussian Blur

   * 커널 크기 3 × 3 적용

3. CLAHE

   * LAB 색공간으로 변환한 뒤 L 채널에 CLAHE 적용
   * clipLimit = 2.0
   * tileGridSize = (8, 8)

4. Gamma Correction

   * gamma = 1.1 적용

5. 폴더 구조 유지 저장

   * 입력 데이터셋의 train / val / test 및 클래스 폴더 구조를 유지한 상태로 저장

---

## 6. 데이터 전처리 흐름

```text
cropped_dataset_more
        ↓
이미지 파일 탐색
        ↓
Resize (320 × 320)
        ↓
Gaussian Blur (3 × 3)
        ↓
CLAHE
        ↓
Gamma Correction (γ = 1.1)
        ↓
cropped_preprocessed_dataset 저장
```

---

## 7. 데이터 증강

학습 코드에서는 학습 데이터에 대해서만 다음 증강을 적용하였다.

| Augmentation           | 설정                  |
| ---------------------- | ------------------- |
| Resize                 | 320 × 320           |
| Random Horizontal Flip | p = 0.5             |
| Random Rotation        | 10도                 |
| Color Jitter           | brightness = 0.15   |
| Color Jitter           | contrast = 0.15     |
| Color Jitter           | saturation = 0.15   |
| Normalize              | ImageNet mean / std |

Validation 및 Test 데이터는 Resize, Tensor 변환, Normalize만 적용하였다.

---

## 8. 폴더 구조

```bash
project/
│
├── cropped_dataset_more/
│   ├── train/
│   ├── val/
│   └── test/
│
├── cropped_preprocessed_dataset/
│   ├── train/
│   │   ├── C_A2/
│   │   ├── C_A4/
│   │   ├── C_A6/
│   │   ├── D_A1/
│   │   ├── D_A2/
│   │   ├── D_A3/
│   │   ├── D_A4/
│   │   ├── D_A5/
│   │   └── D_A6/
│   │
│   ├── val/
│   │   ├── C_A2/
│   │   ├── C_A4/
│   │   ├── C_A6/
│   │   ├── D_A1/
│   │   ├── D_A2/
│   │   ├── D_A3/
│   │   ├── D_A4/
│   │   ├── D_A5/
│   │   └── D_A6/
│   │
│   └── test/
│       ├── C_A2/
│       ├── C_A4/
│       ├── C_A6/
│       ├── D_A1/
│       ├── D_A2/
│       ├── D_A3/
│       ├── D_A4/
│       ├── D_A5/
│       └── D_A6/
│
├── new_conv/
│   ├── epoch_models/
│   ├── history.json
│   ├── test_report.txt
│   ├── class_mapping.json
│   ├── loss_curve.png
│   └── acc_curve.png
│
├── preprocess.py
├── train.py
├── test.py
├── predict.py
├── requirements.txt
└── README.md
```

---

## 9. 실험 환경

| 항목          | 내용                            |
| ----------- | ----------------------------- |
| OS          | Windows 11 Home 24H2          |
| CPU         | Intel Core i7-10700 @ 2.90GHz |
| RAM         | 16GB                          |
| GPU         | NVIDIA GeForce RTX 5060 8GB   |
| Python      | 3.10                          |
| CUDA        | 12.8                          |
| PyTorch     | 2.11.0+cu128                  |
| Framework   | PyTorch                       |
| IDE         | Visual Studio Code / Anaconda |
| Random Seed | 42                            |

---

## 10. 모델 학습 설정

본 프로젝트에서는 ImageNet으로 사전학습된 ConvNeXt-Tiny 모델을 사용하였다.
기존 ConvNeXt-Tiny의 마지막 classifier layer를 9개 클래스에 맞게 수정한 뒤 fine-tuning을 수행하였다.

| 항목                 | 설정                  |
| ------------------ | ------------------- |
| Model              | ConvNeXt-Tiny       |
| Pretrained Weight  | ImageNet pretrained |
| Number of Classes  | 9                   |
| Input Size         | 320 × 320           |
| Batch Size         | 8                   |
| Epoch              | 25                  |
| Learning Rate      | 1e-4                |
| Optimizer          | AdamW               |
| Loss Function      | CrossEntropyLoss    |
| Scheduler          | ReduceLROnPlateau   |
| Scheduler Mode     | min                 |
| Scheduler Factor   | 0.5                 |
| Scheduler Patience | 2                   |
| Num Workers        | 0                   |
| Device             | CUDA 사용 가능 시 GPU 사용 |

---

## 11. 학습 흐름

```text
AI Hub 데이터 다운로드
        ↓
클래스별 데이터 정리
        ↓
병변 중심 Crop 데이터셋 구성
        ↓
전처리 수행
Resize → Gaussian Blur → CLAHE → Gamma Correction
        ↓
Train / Validation / Test 데이터 구성
        ↓
ImageFolder를 이용한 데이터 로드
        ↓
Train 데이터 증강 적용
        ↓
ConvNeXt-Tiny 모델 불러오기
        ↓
Classifier를 9개 클래스에 맞게 수정
        ↓
AdamW + CrossEntropyLoss로 학습
        ↓
Validation Accuracy 기준 Best Model 선정
        ↓
Best Model로 Test Dataset 평가
        ↓
Classification Report 및 Confusion Matrix 저장
```

---

## 12. 저장 파일

학습 코드 실행 후 다음 파일들이 저장된다.

```bash
new_conv/
├── epoch_models/
│   ├── epoch_01_trainLoss_..._valAcc_....pth
│   ├── epoch_02_trainLoss_..._valAcc_....pth
│   └── ...
│
├── history.json
├── test_report.txt
├── class_mapping.json
├── loss_curve.png
└── acc_curve.png
```

### 저장 파일 설명

| 파일                 | 설명                                   |
| ------------------ | ------------------------------------ |
| epoch_models/      | Epoch별 모델 가중치 저장                     |
| history.json       | Train / Validation loss, accuracy 기록 |
| test_report.txt    | 최종 테스트 결과 저장                         |
| class_mapping.json | 클래스명과 인덱스 매핑 저장                      |
| loss_curve.png     | Loss 변화 그래프                          |
| acc_curve.png      | Accuracy 변화 그래프                      |

---

## 13. 실험 결과

### Best Epoch Model

```text
C:\Users\Konyang\Desktop\new_conv\epoch_models\epoch_22_trainLoss_0.0058_trainAcc_0.9990_valLoss_1.6490_valAcc_0.7113.pth
```

### 전체 성능

| Metric                   |  Score |
| ------------------------ | -----: |
| Best Validation Accuracy | 71.13% |
| Test Loss                | 1.4354 |
| Test Accuracy            | 74.26% |
| Macro Precision          | 73.46% |
| Macro Recall             | 73.37% |
| Macro F1-Score           | 73.34% |
| Weighted Precision       | 73.86% |
| Weighted Recall          | 74.26% |
| Weighted F1-Score        | 73.99% |

---

## 14. 클래스별 성능

| Class | Precision | Recall | F1-Score | Support |
| ----- | --------: | -----: | -------: | ------: |
| C_A2  |    87.83% | 90.18% |   88.99% |     224 |
| C_A4  |    82.32% | 79.41% |   80.84% |     170 |
| C_A6  |    90.00% | 95.45% |   92.65% |     198 |
| D_A1  |    60.00% | 52.94% |   56.25% |     102 |
| D_A2  |    66.39% | 70.22% |   68.25% |     225 |
| D_A3  |    64.65% | 61.23% |   62.90% |     227 |
| D_A4  |    59.20% | 53.36% |   56.13% |     223 |
| D_A5  |    66.10% | 69.96% |   67.97% |     223 |
| D_A6  |    84.62% | 87.61% |   86.09% |     226 |

---

## 15. 결과 분석

전체 Test Accuracy는 74.26%로 나타났다.

반려묘 클래스인 C_A2, C_A4, C_A6는 상대적으로 높은 성능을 보였으며, 특히 C_A6 클래스는 F1-Score 92.65%로 가장 높은 성능을 기록하였다.

반려견 클래스 중에서는 D_A6이 F1-Score 86.09%로 가장 높은 성능을 보였다.

반면 D_A1과 D_A4 클래스는 상대적으로 낮은 성능을 보였다.
D_A1은 다른 클래스에 비해 데이터 수가 적고, D_A4는 D_A2, D_A3, D_A5 등 다른 반려견 질환 클래스와 혼동되는 경우가 많았다.

Confusion Matrix 분석 결과, 반려묘 클래스보다 반려견 클래스 간의 오분류가 더 많이 발생하였다. 이는 반려견 피부질환 클래스들이 시각적으로 유사한 병변 형태를 공유하기 때문으로 판단된다.

---

## 16. requirements.txt

```txt
torch==2.11.0+cu128
torchvision==0.26.0+cu128
timm==1.0.27

numpy==2.2.5
opencv-python==4.13.0
scikit-learn==1.7.1
matplotlib==3.10.8
pandas==2.3.3
tqdm==4.67.3
Pillow==12.1.1
```

CUDA 12.8 환경에서 PyTorch를 설치하는 경우 다음 명령어를 사용할 수 있다.

```bash
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu128
```

---

## 17. 실행 방법

### 1) 가상환경 생성

```bash
conda create -n pet_skin python=3.10
conda activate pet_skin
```

### 2) 라이브러리 설치

```bash
pip install -r requirements.txt
```

### 3) 이미지 전처리

```bash
python preprocess.py
```

### 4) 모델 학습

```bash
python train.py
```

### 5) 모델 평가

```bash
python test.py
```

### 6) 단일 이미지 예측

```bash
python predict.py
```

---

## 18. 재현 시 주의사항

1. 데이터셋은 AI Hub에서 직접 다운로드해야 한다.
2. 입력 데이터는 train / val / test 폴더 구조를 유지해야 한다.
3. 각 클래스 폴더명은 C_A2, C_A4, C_A6, D_A1, D_A2, D_A3, D_A4, D_A5, D_A6 형식을 따라야 한다.
4. 전처리 후 데이터는 cropped_preprocessed_dataset 폴더에 저장된다.
5. PyTorch, CUDA, torchvision 버전에 따라 실행 결과가 달라질 수 있다.
6. 모델 파일(.pth)은 용량 문제로 GitHub에 직접 업로드하지 않고 별도 링크로 제공할 수 있다.
7. Random Seed를 고정하였지만, GPU 연산 환경에 따라 완전히 동일한 결과가 나오지 않을 수 있다.

---

## 19. GitHub 업로드 제외 항목

```gitignore
cropped_dataset_more/
cropped_preprocessed_dataset/
*.pth
*.pt
*.onnx
__pycache__/
.ipynb_checkpoints/
```

모델 재현을 위해 필요한 `.pth` 파일은 Google Drive, OneDrive, Hugging Face Hub 등 외부 저장소에 업로드한 후 README에 링크를 첨부한다.

---

## 20. 참고 자료

* AI Hub 반려동물 피부질환 데이터
* PyTorch Documentation
* TorchVision Documentation
* Scikit-Learn Documentation
* OpenCV Documentation
