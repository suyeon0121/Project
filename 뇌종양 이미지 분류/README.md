# 뇌종양 MRI 이미지 분류 및 Grad-CAM 시각화 프로젝트
PyTorch와 Pretrained ResNet18 모델을 활용하여 뇌 MRI 영상을 4개의 클래스로 분류하고, Grad-CAM을 통해 모델의 진단 근거를 시각적으로 확인하는 토이 프로젝트


## 프로젝트 목적 
- **의료 영상 분류**: MRI 사진을 보고 뇌종양의 유무 및 종류(Glioma, Meningioma, Pituitary, No Tumor)를 분류한다.
- **XAI 적용**: 모델이 이미지의 어느 부분을 집중해서 보고 판단했는지 Grad-CAM 히트맵을 통해 검증한다.

## 개발 환경 및 라이브러리 
- **언어 및 프레임워크**: Python, PyTorch (Google Colab GPU 환경)
- **주요 라이브러리**: torchvision, pytorch-grad-cam, matplotlib, tqdm

## 데이터셋 구조 
- **Training**: 2,870장
- **Testing**: 394장

## 학습 
- **모델**: ResNet18 (ImageNet 사전학습 가중치 사용)
- **손실함수**: CrossEntropyLoss
- **옵티마이저**: AdamW (과적합 방지를 위해 Weight Decay 설정)
- **Learning rate 스케줄러**: CosineAnnealingLR
- **Early Stopping**: 검증 정확도가 3에폭 동안 개선되지 않으면 학습을 중단

## 버그 수정
- **Data Leakage 문제 해결**
  - *기존*: 하나의 폴더에서 데이터를 불러와 쪼개는 과정에서 Train/Val 전처리가 꼬이는 현상 발견
  - *수정*: Training과 Testing 독립 폴더 경로로 ImageFolder를 각각 선언하여 데이터 누수 차단
- **Validation Loss 계산 오타 수정**
  - *기존*: 검증 단계에서 에폭 손실률을 구할 때 누적 손실값 대신 맞춘 정답 개수로 계산하던 오타 발견
  - *수정*: running_val_loss를 데이터 개수로 나누도록 수정하여 정상적인 학습 그래프과 조기 종료 조건을 설정

## 실험 결과 및 시각화 
### 학습 그래프
<img width="1107" height="352" alt="image" src="https://github.com/user-attachments/assets/1ca60f48-b59e-42f6-8f4e-1263ae20cb44" />

### Grad-CAM 결과 
ResNet18의 마지막 CNN 레이어 블록인 layer4[-1]을 타겟으로 잡아 모델이 집중한 영역을 확인 
<img width="788" height="1077" alt="image" src="https://github.com/user-attachments/assets/0060bf8d-a19d-4d01-9449-c898494b5f53" />

### 결과 해석 
모델이 정답을 맞춘 이미지들을 확인해 본 결과, MRI 영상 내에서 실제 종양이 결손되거나 이상이 있는 부위에 붉은색 히트맵 활성화가 집중되는 것을 확인 
