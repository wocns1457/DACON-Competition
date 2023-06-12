# Dacon_car_crash_Competition

제1회 코스포 x 데이콘 자동차 충돌 분석 AI경진대회(채용 연계형) - DACON

1. 배경

    최근 블랙박스를 활용해 도로 위에서의 사고를 예방하려는 연구가 다양하게 진행되고 있습니다.
    근래 블랙박스는 위험 상황을 스스로 판단하고, 실시간으로 대처하는 인공지능 기능이 추가되는 방향으로 발전하고 있는데요. 교통사고의 발생과 규모를 큰 폭으로 감소시키고, 향상된 교통 안전 기술을 개발하는 데 유용하게 활용될 수 있을 것으로 기대됩니다!

2. 주제

    블랙박스 영상의 자동차 충돌 분석 AI 알고리즘 개발

3. 설명

    10프레임, 5초 분량의 블랙박스 영상(mp4)들을 입력으로 받아 자동차 충돌 상황을 분석하여,
    이를 13가지 상황(Class)으로 분류하는 AI 모델 개발

4. 주최/주관

    주최: 코리아스타트업포럼(사), 데이콘
    주관: 데이콘

## 데이터 설명
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

## 대회 전략






