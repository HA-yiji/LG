# 난임 환자 임신 성공 여부 예측

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge\&logo=python\&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge\&logo=pandas\&logoColor=white)
![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-F7931E?style=for-the-badge\&logo=scikit-learn\&logoColor=white)
![CatBoost](https://img.shields.io/badge/CatBoost-FFCC00?style=for-the-badge\&logoColor=black)

> Team **옹알이** | ROC-AUC **0.740** | Final Rank **248 / 1,568**

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
| 최종 순위           | ** 팀 248위 (참가인원 1,568명)**     |

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

최종 제출은 `sample_submission.csv`의 `probability` 컬럼에 **임신 성공 확률**을 예측해 제출하는 방식입니다.

<br>

## Workflow

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
CatBoostClassifier 최종 제출
```

초기에는 주요 변수 20개를 선택해 트리 기반 모델을 비교했고, 최종 실험에서는 전체 변수를 범주형과 수치형으로 나누어 전처리한 뒤 CatBoost 모델을 적용했습니다.

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

| No. | 제출 파일                  | 주요 설정                        | 모델                     | Public Score |
| --: | ---------------------- | ---------------------------- | ---------------------- | -----------: |
|   1 | `baseline_submit.csv`  | 제공 베이스라인                     | Baseline               |        0.689 |
|   2 | `baseline_submit2.csv` | 베이스라인 재실행                    | Baseline               |        0.689 |
|   3 | `baseline_submit4.csv` | 상위 20개 변수 + IterativeImputer | RandomForestClassifier |        0.728 |
|   4 | `baseline_submit5.csv` | 상위 20개 변수 + IterativeImputer | DecisionTreeClassifier |        0.725 |
|   5 | `baseline_submit6.csv` | 상위 20개 변수 + IterativeImputer | RandomForestClassifier |        0.676 |
|   6 | `baseline_submit7.csv` | 상위 20개 변수 + IterativeImputer | DecisionTreeClassifier |        0.581 |
|   7 | `baseline_submit8.csv` | 상위 20개 변수 + IterativeImputer | VotingClassifier 실험    |        0.665 |
|   8 | `0226_cat.csv`         | 범주형·수치형 분리 전처리               | CatBoostClassifier     |    **0.740** |

<br>

## Final Model

최종 모델은 `CatBoostClassifier`입니다.

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
| CatBoostClassifier |             0.7374 |    **0.740** |

<br>

## Project Structure

```text
.
├── train.csv
├── test.csv
├── sample_submission.csv
├── 데이터 명세.xlsx
├── 4th_randomforestclassifier.ipynb
├── 5th_decisiontreeclassifier.ipynb
├── 678th_predictions_IterativeImputer.ipynb
├── 9th_catboost.ipynb
└── README.md
```

<br>

## How to Run

```bash
pip install pandas numpy matplotlib seaborn scikit-learn catboost
```

노트북 실행 순서는 다음과 같습니다.

```text
1. 4th_randomforestclassifier.ipynb
2. 5th_decisiontreeclassifier.ipynb
3. 678th_predictions_IterativeImputer.ipynb
4. 9th_catboost.ipynb
```

<br>

## Result

| 최종 제출 파일       | 모델                 | Public Score |            Rank |
| -------------- | ------------------ | -----------: | --------------: |
| `0226_cat.csv` | CatBoostClassifier |    **0.740** | **248 / 1,568** |

<br>

## Note

대회 제공 데이터는 용량 및 규정 문제로 저장소에 포함하지 않았습니다.
실행 시 `train.csv`, `test.csv`, `sample_submission.csv`, `데이터 명세.xlsx` 파일을 프로젝트 루트 경로에 추가해야 합니다.
