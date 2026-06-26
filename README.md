# LG-aimers 6기 
주제 : 난임 환자 대상 임신 성공 여부 예측 AI 온라인 해커톤(https://dacon.io/competitions/official/236452/data)
> **LG AImers 6기 해커톤** — 이진 분류 | 평가 지표: **ROC-AUC** | 
1. 0.689/그냥 제공 베이스라인 해서 제출(baseline_submit.csv)
2. 0.689/(baseline_submit2.csv)
3. 0.728/ 상위 20개 주요 변수 선택, 결측값 iterative imputer 사용, randomforest classifier 모델(baseline_submit4.csv)
4. 0.725/ 상위 20개 주요 변수 선택, 결측값 iterative imputer 사용, decisionTreeclassifier 모델(baseline_submit5.csv)

   
5. 0.676/ 상위 20개 주요 변수 선택, 결측값 iterative imputer 사용, RandomForestClassifier 모델(baseline_submit6.csv)
6. 0.581/ 상위 20개 주요 변수 선택, 결측값 iterative imputer 사용, DecisionTreeClassifier 모델(baseline_submit7.csv)
7. 0.665/ 상위 20개 주요 변수 선택, 결측값 iterative imputer 사용, VotingClassifier - Soft Voting 모델(baseline_submit8.csv)
8. -
9. 0.740/ 상위 20개 주요 변수 선택, 결측값(범주형data-최빈값, 수치형data-중앙값)사용, catboost 모델(0226_cat.csv) - 최종 제출안
