# Dacon_car_crash_Competition
제1회 코스포 x 데이콘 자동차 충돌 분석 AI경진대회(채용 연계형) - DACON  
- ipynb파일 랜더링 오류 발생시 [Nbviewer Dacon_car_crash_Competition.ipynb](https://nbviewer.org/github/wocns1457/DACON-Competition/blob/main/car_crash/Dacon_car_crash_Competition.ipynb)


### 배경
최근 블랙박스를 활용해 도로 위에서의 사고를 예방하려는 연구가 다양하게 진행되고 있습니다.
근래 블랙박스는 위험 상황을 스스로 판단하고, 실시간으로 대처하는 인공지능 기능이 추가되는 방향으로 발전하고 있는데요. 교통사고의 발생과 규모를 큰 폭으로 감소시키고, 향상된 교통 안전 기술을 개발하는 데 유용하게 활용될 수 있을 것으로 기대됩니다!

### 주제
블랙박스 영상의 자동차 충돌 분석 AI 알고리즘 개발

### 설명
10프레임, 5초 분량의 블랙박스 영상(mp4)들을 입력으로 받아 자동차 충돌 상황을 분석하여,
이를 13가지 상황(Class)으로 분류하는 AI 모델 개발

### 주최/주관   
- 주최 : 코리아스타트업포럼(사)
- 주관 : 데이콘


## 데이터 설명
train [폴더]
- 학습용 차량 블랙박스 영상 TRAIN_0000.mp4 ~ TRAIN_2697.mp4

test [폴더]
- 평가용 차량 블랙박스 영상 TEST_0000.mp4 ~ TEST_1799.mp4

train.csv [파일]
- sample_id : 영상 샘플 고유 id
- video_path : 학습용 차량 블랙박스 영상 경로
- label : 13가지의 차량 충돌 상황

test.csv [파일]
- sample_id : 영상 샘플 고유 id
- video_path : 학습용 차량 블랙박스 영상 경로

13가지의 차량 충돌 상황 Class의 세부 정보

- crash : 차량 충돌 여부 (No/Yes)

- ego-Involve : 본인 차량의 충돌 사고 연류 여부 (No/Yes)

- weather : 날씨 상황 (Normal/Snowy/Rainy)

- timing : 낮과 밤 (Day/Night)

- ego-Involve, weather, timing의 정보는 '차량 충돌 사고'가 일어난 경우에만 분석합니다.

|crash |ego-Involve|weather |timing|label|
|:------:|:---:|:---:|:---:|:---:|
|NO|-|-|-|0|
|Yes|Yes|Normal|Day|1|
|Yes|Yes|Normal|Night|2|
|Yes|Yes|Snowy|Day|3|
|Yes|Yes|Snowy|Night|4|
|Yes|Yes|Rainy|Day|5|
|Yes|Yes|Rainy|Night|6|
|Yes|No|Normal|Day|7|
|Yes|No|Normal|Night|8|
|Yes|No|Snowy|Day|9|
|Yes|No|Snowy|Night|10|
|Yes|No|Rainy|Day|11|
|Yes|No|Rainy|Night|12|

## Environment  
Google Colab pro+를 사용하여 대회를 진행하였습니다.
- OS : Ubuntu 20.04.5 LTS
- GPU : NVIDIA A100 40GB

- python == 3.9.16
- torch == 1.13.1+cu116
- torchvision == 0.14.1+cu116
- albumentations == 1.2.1
- slowfast 

## 대회 전략
13가지의 차량 충돌 상황을 crash + ego-Involve, weather, timing으로 label을 분해하여 각각의 상황을 분류하는 3개 모델을 훈련  
추론 시 각각의 예측 결과를 합하는 방법으로 진행

Crash + ego-Involve &nbsp; - &nbsp; 0 : No Crash, 1 : ego-Non Involve, 2 : ego-Involve

&emsp;&emsp; Timing &emsp;&emsp;&emsp;&ensp;&nbsp; - &nbsp; 0 : Day, 1 : Night

&emsp;&emsp; Weather &emsp;&emsp;&ensp;&nbsp;&nbsp; - &nbsp; 0 : Normal, 1 : Snowy, 2 : Rainy  
  
<br>

**-공통-**  
- train_test_split : 0.2
- class imbalance 문제 해결 
     - Weighted random sampler
     - Data argumentation
          - HorizontalFlip
          - RandomRoatate90
          - GaussNoise
          - RGBShift
          - Normalize  

**-Crash + ego-Involve-**  
- Slowfast_r101(pre-trained KINETICS400)  
- Label smoothing 

**-Timing-**  
- DACON측에서 공개한 베이스라인 코드 기반으로 R2plus1d_18(pre-trained KINETICS400)으로 모델만 변경하여 학습  

**-Weather-**  
- Noisy label data drop
    - 제공된 학습 데이터를 확인해 보았을 때 Crash, ego-Involve, Timing보다 비교적 잘못 라벨링된 데이터가 많은 것으로 확인되어 196개의 데이터 제거  
- MVITv2_B_32x3(pre-trained KINETICS400)  
- Mixup, CutMix 추가  
- Label smoothing  
- Custom voting
  - 50frame 입력영상을 특정구간 32frame씩 5개의 데이터를 생성 후 예측 수행. Normal, Snow, Rain이 모두 예측 결과에 포함된다면 Snow, Rain중 더 많이 예측 된 클래스를 선택하는 방식. validation, test에 모두 적용



**Summary**
| Crash + ego-Involve | Timing | Weather |
| :---: | :---: | :---: | 
| Slowfast_r101 <br> Label smoothing <br> Soft Target Cross Entropy Loss <br> AdamW <br> OneCycleLR| R2plus1d_18 <br> BCEWithLogitsLoss <br> Adam <br> ReduceLROnPlateau| MVITv2_B_32x3 <br> Noisy label data drop <br> Add argumentation(Mixup, CutMix) <br> Label smoothing <br> Soft Target Cross Entropy Loss <br> AdamW <br> OneCycleLR <br> Custom voting| 

**그 외 시도들**
- ego, crash, timing, weather 4 가지 주제로 나누기
- Weighting in Loss Function - Class Weight
- DivideMix






















