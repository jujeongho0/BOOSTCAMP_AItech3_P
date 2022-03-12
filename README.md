# P stage

## 1. 마스크 착용 상태 유무

- Preprocess
    - 나이의 경계를 [<30, ≥30and<55,≤55]로 변경함으로써 60대 이상의 사람의 이미지 데이터 수 증강 → Class Imbalance 완화

        ![Untitled 2](https://user-images.githubusercontent.com/62659407/158011843-722affd8-6cdd-4ae5-bfc1-b3def7e9e4db.png)

- DataSet
    - CenterCrop을 이용해 이미지의 배경을 부분 제거 → 모델이 사람에 집중하게 함
    - RandomHorizontalFlip을 이용해 이미지가 로드될 때마다 0.5 확률로 좌우 반전 → 이미지 다양성 증가
        
- DataLoader
    - 먼저, 사람을 기준으로 4:1 비율로 Train, Test Set 나누고, 같은 사람의 다른 데이터 셋은 같은 Set으로 불러옴  → 같은 사람이 다른 Set으로 들어가게 되면 Cheating 현상이 발생할 수 있으므로
        
        ![Untitled 3](https://user-images.githubusercontent.com/62659407/158011851-0b9aa2dc-9ea0-431d-a3dc-22f5bd19b23a.png)
    
- Setting
    - Model : resnet18               
    - Loss : Focal Loss : 데이터가 Class Imbalance하기 때문에, 데이터가 적은 클래스는 그만큼 Loss를 크게 부여하는 Focal Loss 사용
    - Optimizer : Adam
    - Hyper Parameter
        - LR : 0.00008 → 작게 할수록, 학습 시간은 오래 걸리지만 과적합이 일어나지 않고 학습이 잘 되었다.
        - Epoch : 10
    
- Train / Val
    
  ![Untitled 4](https://user-images.githubusercontent.com/62659407/158011854-9d0be8d6-dd61-4798-b888-b90bf62796ba.png)
    
- Test
    
    ![1](https://user-images.githubusercontent.com/62659407/158011856-40af0455-ace2-4647-bf89-14015e57f169.png)

    
    - F1 Score : 0.7068
    - Acc : 76.1587
