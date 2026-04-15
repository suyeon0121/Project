## 뇌종양 이미지 분류
- 캐글 데이터: https://www.kaggle.com/datasets/sartajbhuvaji/brain-tumor-classification-mri

<br/>

### 오류 
- 수정 전 코드

<br/>

```
full_dataset = ImageFolder(root='/content/drive/MyDrive/Colab Notebooks/뇌종양 분류/Training', transform=transforms)

train_size = int(0.8 * len(full_dataset))
val_size = len(full_dataset) - train_size
train_data, val_data = random_split(full_dataset, [train_size, val_size])

train_data.dataset.transform = train_transform
val_data.dataset.transform = val_transform
```

<br/>

위 코드의 흐름:
- full_dataset이라는 하나의 큰 덩어리를 만든다.
- random_split으로 train_data와 val_data로 만든다.
- 문제인 부분: `train_data.dataset.transform = train_transform`

<br/>

추가 설명 ⇒ 
- random_split이 반환하는 Subset 객체는 데이터를 새로 복사해서 만든게 아니다. 원본 full_dataset을 가리키는 인덱스 리스트만 가지고 있을 뿐이다.
- 즉, train_data.dataset과 val_data.dataset은 동일한 메모리 주소에 있는 full_dataset 객체를 보고 있다. 

- 따라서 `train_data.dataset.transform` 을 설정하면 원본이 바뀌게 된다.
- 그 다음 줄에서 `val_data.dataset.transform` 을 설정하면, 방금 설정한 Train용 변환이 Val용 변환으로 덮어씌워져 버린다.
- 결과적으로 Train와 Val 모두 똑같은 val_transform으로 작동하게 되어 학습데이터에 Augmentation(회전, 반전 등)이 하나도 안 들어간다.

<br/>

### 해결 (배운 점)
따라서 데이터셋 객체를 두 번 선언하거나, 인덱스를 먼저 나눈 뒤 각각 다른 transform이 적용된 객체에 주입하는 방식을 사용한다. 

<br/>

### 결과 

<br/>

<img width="943" height="663" alt="image" src="https://github.com/user-attachments/assets/5e1c7b4a-7e16-4e62-9d96-9bc98e39bbb8" />

<br/>

- Glioma: 교종(신경교종)
- Meningioma: 수막종
- Pituitary tumor: 뇌하수체 종양
- No Tumor: 정상(종양 없음)

<br/>

### 추가 
- Early Stopping 추가
  - 기존 코드에서 에포크 7 이후로 정확도가 오르지 않는 상황이 발생
  - 불필요한 연산을 방지하고자 Early Stopping 추가

<br/>

  ```
  Epoch 7/10
  Train Loss: 0.0022 Acc: 1.0000
  Val Loss: 0.0724 Acc: 0.9808
  ----------
  Epoch 8/10
  Train Loss: 0.0015 Acc: 1.0000
  Val Loss: 0.0745 Acc: 0.9756
  ----------
  Epoch 9/10
  Train Loss: 0.0019 Acc: 0.9996
  Val Loss: 0.0724 Acc: 0.9774
  ----------
  Epoch 10/10
  Train Loss: 0.0015 Acc: 1.0000
  Val Loss: 0.0777 Acc: 0.9739
  ```

<br/>

- 모델이 정답을 맞히지 못한 케이스들을 추출해서 검토

<br/>

<img width="1096" height="762" alt="image" src="https://github.com/user-attachments/assets/1fc68c2b-42dc-408f-8378-d86b9057b737" />

<br/>

<br/>

<br/>

### 피드백
- 전이학습과 ResNet을 책으로 배우고 프로젝트를 진행을 해서 그나마 흐름이라던지 구조는 이해할 수 있었지만 디테일 부분은 챙기지 못했다.
- Early stopping을 넣고 싶었지만 혼자 힘으로 코드를 추가하지 못해 도움 받아서 작성했다.
- 그래도 직접 데이터를 다루며 만들어보는게 재미도 있고 진행하면서 코드를 자세히 뜯어보는 게 있었다.
- 다음엔 보지 않고 다시 이 코드를 작성해봐야겠다. 
