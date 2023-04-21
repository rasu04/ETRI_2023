# ETRI_2023_라이프로그의 연관성 규칙에 기반한 스트레스 지수 추론 기술

## 과정 및 코드 설명

### 1. 사용 데이터

---

https://nanum.etri.re.kr/share/schung1/ETRILifelogDataset2020?lang=ko_KR

- 2018 info, survey, label 데이터
- 2019 info, survey, label 데이터
- 2020 info, survey, label 데이터

### 2. 데이터 전처리

---

- ETRI_count_and_merge.ipynb

  : label 데이터를 userid, timestamp에 맞게 count하고
  
  : 위 데이터와 info, survey 데이터를 병합
  
  - label 데이터 집계
  
    groupby('userId')
  
  - 'action'의 카테고리 데이터를 count 컬럼으로 변경
  
    ['sleep', 'personal_care', 'work', 'study', 'household', 'care_housemem', 'recreation_media', 'entertainment',
    'outdoor_act', 'hobby', 'recreation_etc', 'shop', 'communitiy_interaction', 'travel', 'meal', 'socialising']
                
  - 'condition'의 카테고리 데이터를 count 컬럼으로 변경
  
    ALONE, with
  
  - 'conditionSub1Option'의 카테고리 데이터를 count 컬럼으로 변경
  
    with_families
    with_friends
    with_colleagues
    acquaintances
    with_unknown
  
  - 'place'의 카테고리 데이터를 count 컬럼으로 변경
  
    ['home', 'workplace', 'restaurant', 'outdoor', 'other_indoor']
    
  - 'emotionPositive'의 카테고리 데이터를 컬럼으로 변경
  
  - 'emotionTension'의 카테고리 데이터를 컬럼으로 변경
                
  - 'activity'의 카테고리 데이터를 count 컬럼으로 변경
  
    in_vehicle, on_bicycle, on_foot, still, # unknown, tilting, walking, running

  - 'household' count 컬럼 변경
    
  - userId, date 컬럼 타입 변경

  - 결과 컬럼
  
    count_labels.columns = ['date', 'userId', 'sleep', 'personal_care', 'work', 'study', 'household',
                            'recreation_media', 'entertainment', 'outdoor_act',
                            'hobby', 'recreation_etc', 'shop', 'communitiy_interaction', 'travel',
                            'meal', 'socialising', 'ALONE', 'WITH', 'with_families', 'with_friends',
                            'with_colleagues', 'acquaintances', 'with_unknown', 'home', 'workplace',
                            'restaurant', 'outdoor', 'other_indoor',
                            'emotionPositive1', 'emotionPositive2', 'emotionPositive3', 'emotionPositive4',
                            'emotionPositive5', 'emotionPositive6', 'emotionPositive7',
                            'emotionTension1', 'emotionTension2', 'emotionTension3', 'emotionTension4',
                            'emotionTension5', 'emotionTension6', 'emotionTension7',
                            'in_vehicle', 'on_bicycle', 'on_foot', 'still'] # , 'unknown', 'tilting', 'walking', 'running']
 
  
### 3. 데이터 특성공학 및 모델 학습, 적용

---

- 공통

  - 성별 인코딩
    
  - 스트레스(target) : xgboost의 경우
  
    d['pmStress'] = d['pmStress'] - 1
    
  - 감정변화비율 = 오후감정/오전감정
    
  - 긍정변화평균 = emotion1~7의 평균
    
  - 긴장변화평균 = emotion1~7의 평균
    
  - 긍정비율 = sum(Positive5 ~ 7) / sum(1 ~ 3) > 4는 중앙값으로 진행하지 않음
    
  - 긴장비율 = sum(Tension5 ~ 7) / sum(1 ~ 3) > 4는 중앙값으로 진행하지 않음
    
  - 가장 높은 긍정감정의 숫자(1~7)
    
  - 가장 낮은 긍정감정의 숫자(1~7)
    
  - 긍정 수치 1~7 * count 수
  
  - 가장 높은 긴장감정의 숫자(1~7)
   
  - 가장 낮은 긴장감정의 숫자(1~7)
    
  - 긴장 수치 1~7 * count 수

  - 수치 반영 긴장 평균
  
  - 수치 반영 긍정 비율
    
  - 수치 반영 긴장 비율
  
  - 오전 컨디션에 긍정/긴장 반영 = 오전 컨디션 * (수치 반영 긍정 평균 + 수치 반영 긴장 평균) / 7 -> 수치가 1~7이므로 7로 나누기

  - 오전 감정에 긍정/긴장 반영 = 오전 감정 * (수치 반영 긍정 평균 + 수치 반영 긴장 평균) / 7 -> 수치가 1~7이므로 7로 나누기

  - 오전 컨디션/감정에 긍정 긴장 반영한 것의 평균을 냄
    
  - 오전 컨디션 긍정/긴장 반영에 오후 감정을 더한 뒤 평균을 냄
    
  - 오전 감정 긍정/긴장 반영에 오후 감정을 더한 뒤 평균을 냄
    
  - 오전 컨디션/감정 긍정/긴장 반영에 오후 감정을 더한 뒤 평균을 냄
    
  - 최고 수치를 반영한 오전 감정 = (오전 감정 * (최고 감정 + 최고 긴장)) / 14
    
  - 최고 수치를 반영한 오전 컨디션 = (오전 감정 * (최고 감정 + 최고 긴장)) / 14
    
  - 최고 수치를 반영한 오후 감정 = (오전 감정 * (최고 감정 + 최고 긴장)) / 14
   
   
  - (최고 수치를 반영한 오전 컨디션 + 최고 수치를 반영한 오전 감정 + 최고 수치를 반영한 오후 감정) / 3 혹은
    
  - (최고 수치를 반영한 오전 컨디션 + 최고 수치를 반영한 오전 감정 + 최고 수치를 반영한 오후 감정)
    
  - 긍정적인지 = 수치 반영 긍정 평균이 중앙값보다 크면 1 아니면 0
    
  - 부정적인지 = 수치 반영 긍정 평균이 중앙값보다 작으면 1 아니면 0
    
  - 긴장 상태인지 = 수치 반영 긴장 평균이 중앙값보다 크면 1 아니면 0
    
  - 편안한 상태인지 = 수치 반영 긴장 평균이 중앙값보다 작으면 1 아니면 0
    
  - 활동 비율 = 자전거, 도보의 합 / 운송수단, 가만히 있기의 합
    
  - 0으로 나누기된 것을 0으로 치환
  

- ETRI_RandomForest.ipynb

  : RandomForest 모델 생성 및 적용

- ETRI_SVC.ipynb

  : Support Vector Machine 모델 생성 및 적용

- ETRI_TabNet_v4.ipynb

  : TabNet 모델 생성 및 적용
  
- ETRI_xgboost.ipynb

  : XgBoost 모델 생성 및 적용
  
- stacking.ipynb

  : 앙상블 스태킹 모델 생성 및 적용
