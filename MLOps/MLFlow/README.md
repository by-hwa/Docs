# MLFlow
* 머신러닝 라이프 사이클을 관리하는 라이브러리.

## 1. MLFlow Tracking

#### Example code
```python
# mlflow_tracking.py
import os
from random import random, randint

from mlflow import log_metric, log_param, log_artifacts

if __name__ == "__main__":
    print("Running mlflow_tracking.py")

    log_param("param1", randint(0, 100))

    log_metric("foo", random())
    log_metric("foo", random() + 1)
    log_metric("foo", random() + 2)

    if not os.path.exists("outputs"):
        os.makedirs("outputs")
    with open("outputs/test.txt", "w") as f:
        f.write("hello world!")

    log_artifacts("outputs")
  ```
  
* 파라미터와 결과를 비교하기 위해  실험결과를 저장함.
  * mlflow.log_param(“param1“, randint(0, 100))
  * mlflow.log_metric(“foo“, random())
  * mlflow.log_artifacts(“output“)


![image](https://github.com/by-hwa/Docs/assets/102535447/f0f539d4-1196-48ad-b710-fbaa7f5e41cf)

#### 위의 이미지와 같이 파라미터의 실험 결과를 저장한다.
#### MLflow experiments list 확인.

```bash
mlflow experiments search
```

## 2. MLFlow Projects
#### ML 모듈별 project example - <https://github.com/mlflow/mlflow/tree/master/examples>
#### use : sklearn_elasticnet_wine

### Run ML project
```bash
mlflow run --env-manager local sklearn_elasticnet_wine 
```

#### add parameter
```bash
mlflow run /Users/leebyeonghwa/workspace/mlflow_test/sklearn_elasticnet_wine -b local -P alpha=0.5 -P l1_ratio=0.1
```

#### experiment-name 지정지 experiment 생성 및 하위에서 작업.
```bash
mlflow run --env-manager local --experiment-name 'sklearn_elasticnet_wine'  sklearn_elasticnet_wine   
```

#### url 자리에 git 링크를 넣어 github repo의 MLproject도 사용가능.

```git@github.com:mlflow/mlflow-example.git```

![image](https://github.com/by-hwa/Docs/assets/102535447/e0d66fd8-3d2b-4de8-8c38-9fbfd761c21f)

## 2. MLFlow Models
### Manage Model
#### MLproject 실행시 학습, 모델 생성시마다 버저닝되어 관리된다.
![image](https://github.com/by-hwa/Docs/assets/102535447/07e1ca05-0024-45ce-b40a-93d1cf08ad80)

### Deploy model
ref : <https://mlflow.org/docs/latest/models.html#deploy-mlflow-models>

```bash
mlflow models serve --no-conda -m ./mlruns/281230758245112906/68ca6050d2c448b3a6ec9aaa2068b84c/artifacts/model -p 5001
```
