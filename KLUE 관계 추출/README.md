# KLUE 관계 추출

- 카메라로 촬영한 사람 얼굴 이미지의 마스크 착용 여부를 판단하는 Task

- 학습 데이터 개요
    
    마스크를 착용하는 건 COIVD-19의 확산을 방지하는데 중요한 역할을 합니다. 제공되는 이 데이터셋은 사람이 마스크를 착용하였는지 판별하는 모델을 학습할 수 있게 해줍니다. 모든 데이터셋은 아시아인 남녀로 구성되어 있고 나이는 20대부터 70대까지 다양하게 분포하고 있습니다. 간략한 통계는 다음과 같습니다.
    
    - 전체 사람 명 수 : 4,500
    - 한 사람당 사진의 개수: 7 [마스크 착용 5장, 이상하게 착용(코스크, 턱스크) 1장, 미착용 1장]
    - 이미지 크기: (384, 512)
    
    전체 데이터셋 중에서 60%는 학습 데이터셋으로 활용됩니다.
    
    **입력값.** 마스크 착용 사진, 미착용 사진, 혹은 이상하게 착용한 사진(코스크, 턱스크)
    
    예시:
    
    <img src="https://user-images.githubusercontent.com/62659407/158011779-a3912770-8f48-475d-8f05-214a89b0350b.png" width="200" height="250">

    
    **결과값.** 총 18개의 class를 예측해야합니다. 결과값으로 0~17에 해당되는 숫자가 각 이미지 당 하나씩 나와야합니다.
    
    예시:
    
    7 (the class of cfe1268.jpg)
    
    2 (the class of 3a2662c.jpg)
    
    …

    **Class Description:**

    마스크 착용여부, 성별, 나이를 기준으로 총 18개의 클래스가 있습니다.
    <img src="https://user-images.githubusercontent.com/62659407/158011819-12bf9aac-5aec-42d7-b128-7c064e54d90e.png" width="600" height="600">

   
- EDA
    
    ![Untitled 1](https://user-images.githubusercontent.com/62659407/158011834-6bfc5ca5-8962-406c-b576-a9d114823709.png)

    
    → 60대 이상의 사람의 이미지 데이터 수가 현저히 적다
    
    → 또한, 전체적으로 Class Imbalance가 심하다
    
- Preprocess
    - Final
        - 나이의 경계를 [<30, ≥30and<55,≤55]로 변경함으로써 60대 이상의 사람의 이미지 데이터 수 증강 → Class Imbalance 완화
            
            ![Untitled 2](https://user-images.githubusercontent.com/62659407/158011843-722affd8-6cdd-4ae5-bfc1-b3def7e9e4db.png)
    
    
    - Trial
        - RandomCrop을 이용해 [0, 1, 3, 4]를 제외한 클래스의 이미지 데이터 5배 증강 → Class Imbalance 완화
        
- DataSet
    - Final
        - CentorCrop을 이용해 이미지의 배경을 부분 제거 → 모델이 사람에 집중하게 함
        - RandomHorizontalFlip을 이용해 이미지가 로드될 때마다 0.5 확률로 좌우 반전 → 이미지 다양성 증가
    
    - Trial
        - Grabcut을 이용해 이미지의 배경을 제거 → 모델이 사람에 집중하게 함
        
- DataLoader
    - 먼저, 사람을 기준으로 4:1 비율로 Train, Test Set 나누고, 같은 사람의 다른 데이터 셋은 같은 Set으로 불러옴  → 같은 사람이 다른 Set으로 들어가게 되면 Cheating 현상이 발생할 수 있으므로
        
        ![Untitled 3](https://user-images.githubusercontent.com/62659407/158011851-0b9aa2dc-9ea0-431d-a3dc-22f5bd19b23a.png)
    
- Setting
    - Model
        - Final
            - resnet18
        
        - Trial
            - resnet50
            - resnet152
                
                → 모두 resnet 18 보다 깊어 높은 성능을 기대했지만, 학습 시간만 오래 걸리고, 최종 성능은 비슷했다.
                
        
    - Loss
        - Final
            - Focal Loss : 데이터가 Class Imbalance하기 때문에, 데이터가 적은 클래스는 그만큼 Loss를 크게 부여하는 Focal Loss 사용
        
        - Trial
            - CE
            - Weighted CE
        
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
