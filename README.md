# LG Aimers 6기 "난임 환자 임신 성공 여부 예측 AI 온라인 해커톤" 👶🏻

[대회 링크](https://dacon.io/competitions/official/236452/overview/description)

> ### Python / Pandas / Scikit-Learn / CatBoost
>
> Team **옹알이** | Public ROC-AUC **0.740** | Team Rank **248위** | Participants **1,568명**

난임 환자의 시술 데이터를 기반으로 **임신 성공 확률**을 예측하는 이진 분류 프로젝트입니다.
베이스라인 제출에서 시작해 변수 선택, 결측치 처리, 모델 변경을 단계적으로 실험하며 성능을 개선했습니다.

<br>

## Overview

| 항목              | 내용                    |
| --------------- | --------------------- |
| 팀명              | 옹알이                   |
| 문제 유형           | Binary Classification |
| 타겟 변수           | `임신 성공 여부`            |
| 평가 지표           | ROC-AUC               |
| 최종 모델           | CatBoostClassifier    |
| 최고 Public Score | **0.740**             |
| 최종 팀 순위         | **248위**              |
| 참가 인원           | **1,568명**            |

<br>

## Dataset

| 파일명                     | 설명       |     행 수 | 컬럼 구성                       |
| ----------------------- | -------- | ------: | --------------------------- |
| `train.csv`             | 학습 데이터   | 256,351 | `ID` + feature 67개 + target |
| `test.csv`              | 평가 데이터   |  90,067 | `ID` + feature 67개          |
| `sample_submission.csv` | 제출 양식    |  90,067 | `ID` + `probability`        |
| `데이터 명세.xlsx`           | 변수 설명 파일 |       - | 컬럼별 상세 설명                   |

타겟 변수 `임신 성공 여부`는 다음과 같습니다.

| 값 | 의미    |
| - | ----- |
| 0 | 임신 실패 |
| 1 | 임신 성공 |

<br>

## Evaluation

최종 제출은 `sample_submission.csv` 형식에 맞춰 각 샘플의 임신 성공 확률을 `probability` 컬럼에 입력하는 방식입니다.

| 항목            | 내용                       |
| ------------- | ------------------------ |
| 제출 파일 형식      | CSV                      |
| 제출 컬럼         | `ID`, `probability`      |
| 평가 산식         | ROC-AUC                  |
| Public Score  | 전체 테스트 데이터 중 사전 샘플링된 50% |
| Private Score | 전체 테스트 데이터 100%          |

<br>

## My Experiment Workflow

팀 **옹알이**의 활동 중, 제가 직접 진행한 전처리 및 모델링 실험 흐름을 중심으로 정리했습니다.

```text
Baseline 제출
   ↓
상위 20개 주요 변수 선택
   ↓
IterativeImputer 기반 결측치 처리
   ↓
RandomForest / DecisionTree / VotingClassifier 실험
   ↓
범주형·수치형 변수 분리 전처리
   ↓
CatBoostClassifier 적용
   ↓
CatBoost 하이퍼파라미터 수동 튜닝
   ↓
최종 제출
```

초기에는 주요 변수 20개를 선택해 트리 기반 모델을 비교했습니다. 이후 최종 실험에서는 전체 변수를 범주형과 수치형으로 나누어 전처리한 뒤 CatBoost 모델을 적용했습니다.

CatBoost 모델에서는 `iterations`, `learning_rate`, `depth`, `scale_pos_weight` 등을 직접 조정하며 검증 ROC-AUC 변화를 비교했습니다. 이 과정을 통해 검증 ROC-AUC를 **0.7294에서 0.7374까지 개선**했고, 최종 Public Score **0.740**을 기록했습니다.

<br>

## Preprocessing

주요 전처리 과정은 다음과 같습니다.

* `ID` 컬럼 제거
* 타겟 변수 `임신 성공 여부` 분리
* 주요 변수 20개를 선택한 초기 모델 실험
* 범주형 변수 `OrdinalEncoder` 적용
* `IterativeImputer` 기반 결측치 처리 실험
* 최종 CatBoost 실험에서 범주형·수치형 변수 분리
* 수치형 변수 결측치는 중앙값으로 대체

```python
ordinal_encoder = OrdinalEncoder(
    handle_unknown='use_encoded_value',
    unknown_value=-1
)
```

```python
imputer = SimpleImputer(strategy='median')
x_train_enc[numeric_columns] = imputer.fit_transform(x_train_enc[numeric_columns])
x_val_enc[numeric_columns] = imputer.transform(x_val_enc[numeric_columns])
```

<br>

## Experiments

| No. | 제출 파일                  | 주요 설정                          | 모델                     | Public Score |
| --: | ---------------------- | ------------------------------ | ---------------------- | -----------: |
|   1 | `baseline_submit.csv`  | 제공 베이스라인                       | Baseline               |        0.689 |
|   2 | `baseline_submit2.csv` | 베이스라인 재실행                      | Baseline               |        0.689 |
|   3 | `baseline_submit4.csv` | 상위 20개 변수 + IterativeImputer   | RandomForestClassifier |        0.728 |
|   4 | `baseline_submit5.csv` | 상위 20개 변수 + IterativeImputer   | DecisionTreeClassifier |        0.725 |
|   5 | `baseline_submit6.csv` | 상위 20개 변수 + IterativeImputer   | RandomForestClassifier |        0.676 |
|   6 | `baseline_submit7.csv` | 상위 20개 변수 + IterativeImputer   | DecisionTreeClassifier |        0.581 |
|   7 | `baseline_submit8.csv` | 상위 20개 변수 + IterativeImputer   | VotingClassifier 비교 실험 |        0.665 |
|   8 | `0226_cat.csv`         | 범주형·수치형 분리 전처리 + 하이퍼파라미터 수동 튜닝 | CatBoostClassifier     |    **0.740** |

CatBoost 적용 후에도 성능 개선 폭이 크지 않아, `iterations`, `learning_rate`, `depth` 등의 값을 직접 바꿔가며 반복 실험했습니다. 작은 파라미터 변화에도 ROC-AUC가 민감하게 달라졌기 때문에, 검증 점수를 기준으로 조합을 비교하며 최종 설정을 선택했습니다.

<br>

## Final Model

최종 모델은 `CatBoostClassifier`입니다. CatBoost 적용 이후에도 `iterations`, `learning_rate`, `depth`, `scale_pos_weight`를 직접 조정하며 검증 ROC-AUC를 비교했습니다.

초기 CatBoost 실험의 검증 ROC-AUC는 **0.7294**였고, 반복적인 하이퍼파라미터 조정 후 **0.7374**까지 개선했습니다.

> 얼레벌레 파라미터 수정 실험 기록은 [Notion 실험 로그](https://app.notion.com/p/0226-1a66981db97b80d08021e6a20c1af25c?source=copy_link)

```python
cat_model = CatBoostClassifier(
    iterations=1000,
    learning_rate=0.01,
    depth=10,
    random_seed=100,
    loss_function='Logloss',
    scale_pos_weight=3,
    one_hot_max_size=1,
    verbose=10
)
```

| 모델                 | Validation ROC-AUC | Public Score |
| ------------------ | -----------------: | -----------: |
| CatBoostClassifier |         **0.7374** |    **0.740** |


<br>

## Project Structure

```text
.
├── 4th_randomforestclassifier.ipynb
├── 5th_decisiontreeclassifier.ipynb
├── 678th_predictions_IterativeImputer.ipynb
├── 9th_catboost.ipynb
└── README.md
```

<br>

## Result

| 최종 제출 파일       | 모델                 | Public Score | Team Rank | Participants |
| -------------- | ------------------ | -----------: | --------: | -----------: |
| `0226_cat.csv` | CatBoostClassifier |    **0.740** |  **248위** |   **1,568명** |

<br>

## Note

실행 시 데이터 파일을 첨부 후 프로젝트 루트 경로에 추가해야 합니다.
데이터 파일은 저작권 문제로 첨부하지 않았습니다. 사이트에서 직접 데이터 다운로드 바랍니다.

<br>

## 아쉬운점

- 만약 다시 프로젝트를 진행한다면 더 간결하게 한 코드 내에서 여러 모델들을 돌려 결과를 뽑아내기
- 결측치에 대한 다른 방안 공부해보기
- 모델 하이퍼파라미터 값을 수동으로 하지 않고 **Grid search**,**Random Search**, **Optuna**같은 Optimization 도구를 써보기
- 팀 기록 더 자세히 기록하기
