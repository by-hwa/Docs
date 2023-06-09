# Index

1. [Airflow](#1-airflow)
2. [Install Airflow](#2-airflow-설치)
3. [DAG](#3-dag)
4. [DAG 작성법](#4-dag-작성법)



# 1. Airflow
#### Airflow 는 어떠한 일련의 작업 프로세스 (ex. ETL...) 를 관리하는 도구이다.
#### 여기서 말하는 관리란 workflow 작성, 스케줄링, 모니터링이다.
#### 작성한 workflow를 원하는 일정에 실행시킬 수 있으며, 모니터링 할 수 있다.

# 2. Airflow 설치
## - Local (M1 Mac)
### Airflow 홈 디렉토리 설정
```bash
mkdir ~/airflow
export AIRFLOW_HOME=~/airflow # 환경변수 설정
```

### 가상환경 생성 (권장)
```bash
# 가상환경 생성
python3 -m venv ./airflow_env

# 가상환경 활성화
source airflow_env/bin/activate
```

### Airflow 버전, Python 버전, 설치경로
```bash
AIRFLOW_VERSION=2.3.2 # set airflow version
PYTHON_VERSION="$(python --version | cut -d " " -f 2 | cut -d "." -f 1-2)" # set python version
CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"
```

### Airflow 설치
```bash
pip install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"
```

### Airflow-db initialize
```bash
airflow db init
```

### Make account
```bash
airflow users create \
    --username admin \
    --firstname Peter \
    --lastname Parker \
    --role Admin \
    --email user@abc.def \
    --password PASSWD
```

### Run airflow
```bash
airflow webserver --port 8080
```

### Remove DAG example
```bash
vi airflow.cfg # airflow.cfg 의 load_examples 옵션 True → False
rm -rf ./airflow.db
```
#### Airflow-db initialize 부터 재실행

### Run scheduler
```bash
airflow scheduler
```

## Docekr
#### ref: <https://airflow.apache.org/docs/apache-airflow/2.3.2/start/docker.html>

### Docker-compose yml 다운
```bash
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.3.2/docker-compose.yaml'
```

<details>
<summary>Docker-compose.yml 수정본</summary>
  
  
<pre><code>

  # Licensed to the Apache Software Foundation (ASF) under one
  # or more contributor license agreements.  See the NOTICE file
  # distributed with this work for additional information
  # regarding copyright ownership.  The ASF licenses this file
  # to you under the Apache License, Version 2.0 (the
  # "License"); you may not use this file except in compliance
  # with the License.  You may obtain a copy of the License at
  #
  #   http://www.apache.org/licenses/LICENSE-2.0
  #
  # Unless required by applicable law or agreed to in writing,
  # software distributed under the License is distributed on an
  # "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  # KIND, either express or implied.  See the License for the
  # specific language governing permissions and limitations
  # under the License.
  #

  # Basic Airflow cluster configuration for CeleryExecutor with Redis and PostgreSQL.
  #
  # WARNING: This configuration is for local development. Do not use it in a production deployment.
  #
  # This configuration supports basic configuration using environment variables or an .env file
  # The following variables are supported:
  #
  # AIRFLOW_IMAGE_NAME           - Docker image name used to run Airflow.
  #                                Default: apache/airflow:2.3.2
  # AIRFLOW_UID                  - User ID in Airflow containers
  #                                Default: 50000
  # Those configurations are useful mostly in case of standalone testing/running Airflow in test/try-out mode
  #
  # _AIRFLOW_WWW_USER_USERNAME   - Username for the administrator account (if requested).
  #                                Default: airflow
  # _AIRFLOW_WWW_USER_PASSWORD   - Password for the administrator account (if requested).
  #                                Default: airflow
  # _PIP_ADDITIONAL_REQUIREMENTS - Additional PIP requirements to add when starting all containers.
  #                                Default: ''
  #
  # Feel free to modify this file to suit your needs.
  ---
  version: '3'
  x-airflow-common:
    &airflow-common
    # In order to add custom dependencies or upgrade provider packages you can use your extended image.
    # Comment the image line, place your Dockerfile in the directory where you placed the docker-compose.yaml
    # and uncomment the "build" line below, Then run `docker-compose build` to build the images.
    image: ${AIRFLOW_IMAGE_NAME:-apache/airflow:2.3.2}
    # build: .
    environment:
      &airflow-common-env
  #    AIRFLOW__CORE__EXECUTOR: CeleryExecutor
      AIRFLOW__CORE__EXECUTOR: LocalExecutor # modified
      AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
      # For backward compatibility, with Airflow <2.3
      AIRFLOW__CORE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
  #    AIRFLOW__CELERY__RESULT_BACKEND: db+postgresql://airflow:airflow@postgres/airflow # removed
  #    AIRFLOW__CELERY__BROKER_URL: redis://:@redis:6379/0 # removed
      AIRFLOW__CORE__FERNET_KEY: ''
      AIRFLOW__CORE__DAGS_ARE_PAUSED_AT_CREATION: 'true'
      AIRFLOW__CORE__LOAD_EXAMPLES: 'true'
      AIRFLOW__API__AUTH_BACKENDS: 'airflow.api.auth.backend.basic_auth'
      _PIP_ADDITIONAL_REQUIREMENTS: ${_PIP_ADDITIONAL_REQUIREMENTS:-}
    volumes:
      - ./dags:/opt/airflow/dags
      - ./logs:/opt/airflow/logs
      - ./plugins:/opt/airflow/plugins
    user: "${AIRFLOW_UID:-50000}:0"
    depends_on:
      &airflow-common-depends-on
  #    redis: # removed
  #      condition: service_healthy
      postgres:
        condition: service_healthy

  services:
    postgres:
      image: postgres:13
      environment:
        POSTGRES_USER: airflow
        POSTGRES_PASSWORD: airflow
        POSTGRES_DB: airflow
      volumes:
        - postgres-db-volume:/var/lib/postgresql/data
      healthcheck:
        test: ["CMD", "pg_isready", "-U", "airflow"]
        interval: 5s
        retries: 5
      restart: always

  #  redis: # removed
  #    image: redis:latest
  #    expose:
  #      - 6379
  #    healthcheck:
  #      test: ["CMD", "redis-cli", "ping"]
  #      interval: 5s
  #      timeout: 30s
  #      retries: 50
  #    restart: always

    airflow-webserver:
      <<: *airflow-common
      command: webserver
      ports:
        - 8080:8080
      healthcheck:
        test: ["CMD", "curl", "--fail", "http://localhost:8080/health"]
        interval: 10s
        timeout: 10s
        retries: 5
      restart: always
      depends_on:
        <<: *airflow-common-depends-on
        airflow-init:
          condition: service_completed_successfully

    airflow-scheduler:
      <<: *airflow-common
      command: scheduler
      healthcheck:
        test: ["CMD-SHELL", 'airflow jobs check --job-type SchedulerJob --hostname "$${HOSTNAME}"']
        interval: 10s
        timeout: 10s
        retries: 5
      restart: always
      depends_on:
        <<: *airflow-common-depends-on
        airflow-init:
          condition: service_completed_successfully

  #  airflow-worker: # removed
  #    <<: *airflow-common
  #    command: celery worker
  #    healthcheck:
  #      test:
  #        - "CMD-SHELL"
  #        - 'celery --app airflow.executors.celery_executor.app inspect ping -d "celery@$${HOSTNAME}"'
  #      interval: 10s
  #      timeout: 10s
  #      retries: 5
  #    environment:
  #      <<: *airflow-common-env
  #      # Required to handle warm shutdown of the celery workers properly
  #      # See https://airflow.apache.org/docs/docker-stack/entrypoint.html#signal-propagation
  #      DUMB_INIT_SETSID: "0"
  #    restart: always
  #    depends_on:
  #      <<: *airflow-common-depends-on
  #      airflow-init:
  #        condition: service_completed_successfully

    airflow-triggerer:
      <<: *airflow-common
      command: triggerer
      healthcheck:
        test: ["CMD-SHELL", 'airflow jobs check --job-type TriggererJob --hostname "$${HOSTNAME}"']
        interval: 10s
        timeout: 10s
        retries: 5
      restart: always
      depends_on:
        <<: *airflow-common-depends-on
        airflow-init:
          condition: service_completed_successfully

    airflow-init:
      <<: *airflow-common
      entrypoint: /bin/bash
      # yamllint disable rule:line-length
      command:
        - -c
        - |
          function ver() {
            printf "%04d%04d%04d%04d" $${1//./ }
          }
          airflow_version=$$(gosu airflow airflow version)
          airflow_version_comparable=$$(ver $${airflow_version})
          min_airflow_version=2.2.0
          min_airflow_version_comparable=$$(ver $${min_airflow_version})
          if (( airflow_version_comparable < min_airflow_version_comparable )); then
            echo
            echo -e "\033[1;31mERROR!!!: Too old Airflow version $${airflow_version}!\e[0m"
            echo "The minimum Airflow version supported: $${min_airflow_version}. Only use this or higher!"
            echo
            exit 1
          fi
          if [[ -z "${AIRFLOW_UID}" ]]; then
            echo
            echo -e "\033[1;33mWARNING!!!: AIRFLOW_UID not set!\e[0m"
            echo "If you are on Linux, you SHOULD follow the instructions below to set "
            echo "AIRFLOW_UID environment variable, otherwise files will be owned by root."
            echo "For other operating systems you can get rid of the warning with manually created .env file:"
            echo "    See: https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html#setting-the-right-airflow-user"
            echo
          fi
          one_meg=1048576
          mem_available=$$(($$(getconf _PHYS_PAGES) * $$(getconf PAGE_SIZE) / one_meg))
          cpus_available=$$(grep -cE 'cpu[0-9]+' /proc/stat)
          disk_available=$$(df / | tail -1 | awk '{print $$4}')
          warning_resources="false"
          if (( mem_available < 4000 )) ; then
            echo
            echo -e "\033[1;33mWARNING!!!: Not enough memory available for Docker.\e[0m"
            echo "At least 4GB of memory required. You have $$(numfmt --to iec $$((mem_available * one_meg)))"
            echo
            warning_resources="true"
          fi
          if (( cpus_available < 2 )); then
            echo
            echo -e "\033[1;33mWARNING!!!: Not enough CPUS available for Docker.\e[0m"
            echo "At least 2 CPUs recommended. You have $${cpus_available}"
            echo
            warning_resources="true"
          fi
          if (( disk_available < one_meg * 10 )); then
            echo
            echo -e "\033[1;33mWARNING!!!: Not enough Disk space available for Docker.\e[0m"
            echo "At least 10 GBs recommended. You have $$(numfmt --to iec $$((disk_available * 1024 )))"
            echo
            warning_resources="true"
          fi
          if [[ $${warning_resources} == "true" ]]; then
            echo
            echo -e "\033[1;33mWARNING!!!: You have not enough resources to run Airflow (see above)!\e[0m"
            echo "Please follow the instructions to increase amount of resources available:"
            echo "   https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html#before-you-begin"
            echo
          fi
          mkdir -p /sources/logs /sources/dags /sources/plugins
          chown -R "${AIRFLOW_UID}:0" /sources/{logs,dags,plugins}
          exec /entrypoint airflow version
      # yamllint enable rule:line-length
      environment:
        <<: *airflow-common-env
        _AIRFLOW_DB_UPGRADE: 'true'
        _AIRFLOW_WWW_USER_CREATE: 'true'
        _AIRFLOW_WWW_USER_USERNAME: ${_AIRFLOW_WWW_USER_USERNAME:-airflow}
        _AIRFLOW_WWW_USER_PASSWORD: ${_AIRFLOW_WWW_USER_PASSWORD:-airflow}
        _PIP_ADDITIONAL_REQUIREMENTS: ''
      user: "0:0"
      volumes:
        - .:/sources

    airflow-cli:
      <<: *airflow-common
      profiles:
        - debug
      environment:
        <<: *airflow-common-env
        CONNECTION_CHECK_MAX_COUNT: "0"
      # Workaround for entrypoint issue. See: https://github.com/apache/airflow/issues/16252
      command:
        - bash
        - -c
        - airflow

    # You can enable flower by adding "--profile flower" option e.g. docker-compose --profile flower up
    # or by explicitly targeted on the command line e.g. docker-compose up flower.
    # See: https://docs.docker.com/compose/profiles/
  #  flower: # removed
  #    <<: *airflow-common
  #    command: celery flower
  #    profiles:
  #      - flower
  #    ports:
  #      - 5555:5555
  #    healthcheck:
  #      test: ["CMD", "curl", "--fail", "http://localhost:5555/"]
  #      interval: 10s
  #      timeout: 10s
  #      retries: 5
  #    restart: always
  #    depends_on:
  #      <<: *airflow-common-depends-on
  #      airflow-init:
  #        condition: service_completed_successfully

  volumes:
    postgres-db-volume:


</code></pre>

</details>
 
  
  
### Make folder
```bash
mkdir -p ./dags ./logs ./plugins
```
  
  
### Airflow-db initialize
```bash
docker-compose up airflow-init
```
  
### Airflow ui 접속
#### localhost:8080
  
## Connect - Minio
#### ref : <https://airflow.apache.org/docs/apache-airflow-providers-amazon/stable/operators/s3.html>
#### ref : <https://airflow.apache.org/docs/apache-airflow/1.10.6/_api/airflow/hooks/S3_hook/index.html>
#### ref : <https://blog.min.io/apache-airflow-minio/>

### 1. Admin - Connections
![image](https://github.com/by-hwa/Docs/assets/102535447/cf16557d-7800-4c7f-a0f3-d56ecd8d01a9)
### 2. Add a new record
![image](https://github.com/by-hwa/Docs/assets/102535447/c0351e23-6aba-4efe-8f43-b3395ece6a1b)
### 3. Write detail
![image](https://github.com/by-hwa/Docs/assets/102535447/bd419d63-39c2-4f2d-b47f-789fd5dc2a5d)

#### Access Key ID - minio
#### Secret Access Key - minio 발급시 1회만 알려줌
  
![image](https://github.com/by-hwa/Docs/assets/102535447/99b7e1f0-0860-4fe4-8413-20fc7dc38f31)

## Connect - Mongodb
#### ref : <https://stackoverflow.com/questions/38199851/mongodb-connection-refused-docker-compose>
#### ref : <https://www.devkuma.com/docs/docker/host-localhost-connection/>

### 1. Admin - Connections
![image](https://github.com/by-hwa/Docs/assets/102535447/dcb70b7a-70bd-44f2-8674-0996f24dc96f)
### 2. Add a new record
![image](https://github.com/by-hwa/Docs/assets/102535447/3191ae39-5963-49f1-9fc5-7027c9e1ecc7)
### 3. write detail
![image](https://github.com/by-hwa/Docs/assets/102535447/4d9e0aa9-7e31-40cd-b321-071bc1ca1e74)

#### Host - docker container name
#### Port - port num

![image](https://github.com/by-hwa/Docs/assets/102535447/a6b0518b-f051-4145-b4c5-d29b24162d80)
### 외부 서버 연결
#### password - required
#### Schema - 지정
  
# 3. DAG
#### 기본적으로 DAG는 airflow/dags 폴더내에 있는 파이썬 파일을 읽어 온다. 
#### airflow 폴더내에 dags 폴더가 없다면 생성해주도록 한다.
#### DAG는 비순환 단방향 그래프로 스케줄러에 의해 일정 시간마다 실행될 workflow 이다.
  

<details>
<summary>DAG example code</summary>
  
  
<pre><code>
from airflow import DAG
from airflow.operators.bash import BashOperator
import datetime

# default argument 설정
default_arg={
  'owner':'user',
  'retries':3, # 실패시 재시도 횟수
  'retry_delay':datetime.timedelta(minutes=5) # 재시도 딜레이
}

today = datetime.datetime.now()
year = today.year
month = today.month
day = today.day

# DAG 본문
with DAG(
  dag_id='first_dag',
  default_args=default_args,
  description='DAG description',
  start_date=datetime.datetime(year=year, month=month, day=day)
) as dag:
    task1 = BashOperator(
        task_id='first_task',
        bash_command="echo hello world, this is the first task!"
    )

    task2 = BashOperator(
        task_id='second_task',
        bash_command="echo hey, i am task2 and will be running after task1"
    )

    task3 = BashOperator(
        task_id='third_task',
        bash_command="echo hey, i am task3 and will be running after task1 at the same time as the task2 "
    )

    # Task dependency method1
    # task1.set_downstream(task2)
    # task1.set_downstream(task3)

    # Task dependency method2
    # task1 >> task2
    # task1 >> task3

    # Task dependency method3
    task1 >> [task2, task3]
    
    # 위의 3가지 방법 모두 같은 결과를 나타낸다.
</code></pre>

</details>
  
![image](https://github.com/by-hwa/Docs/assets/102535447/e7b20c48-ef46-4454-b501-a717ec2d3d7e)

# 4. DAG 작성법
#### DAG는 Operator 로 task를 구성한다.

## Operator
#### ref: <https://airflow.apache.org/docs/apache-airflow/2.2.3/_api/airflow/operators/index.html>
#### ref: <https://www.bearpooh.com/152>

### Operator 종류
* DummyOperator
  * 빈 task 생성.
  ```python
  task = DummyOperator(task_id='task_id')
  ```
* BashOperator
  * bash 터미널에서 동작 수행.
  ```python
  bask_task = BashOperator(task_id='task_id',
                        bash_command='echo bash command')
  ```
* PythonOperator
  * Python 코드를 실행하기 위한 Operator
  ```python
  def function(arg):
    return print(arg)

  python_task = PythonOperator(task_id='task_id',
                                python_callable=function,
                                op_kwargs={arg:'arg!!'})
  ```
* PythonBranchOperator
  * 다음으로 실행 될 task 선택.
  ```python
  def choose_branch(**kwargs):
    branches = ['b1', 'b2', 'b3']
    chosen = random.choice(branches)
    print(f'chosen: {chosen}')
    return chosen

  branching = BranchPythonOperator(task_id='choose_branch', python_callable=choose_branch)
  b1 = BashOperator(task_id='b1', bash_command='echo b1')
  b2 = BashOperator(task_id='b2', bash_command='echo b2')
  b3 = BashOperator(task_id='b3', bash_command='echo b3')
  c1 = BashOperator(task_id='c1', bash_command='echo c1')

   start_dag >> branching >> [b1, b2, b3]
  ```

![image](https://github.com/by-hwa/Docs/assets/102535447/694a2cd0-56dd-47e9-bedb-5ad6fd25c89c)

* EmailOperator
  * Email 전송 task 생성

## Hook
* S3Hook
  * ref : <https://airflow.apache.org/docs/apache-airflow-providers-amazon/stable/_api/airflow/providers/amazon/aws/hooks/s3/index.html#airflow.providers.amazon.aws.hooks.s3.S3Hook.download_file>
```python
def upload_file_to_minio():
  s3_hook = S3Hook(aws_conn_id='conn_hyundai_rotem')
  s3_hook.load_file(
      filename='/Users/leebyeonghwa/Downloads/query_result_2023-03-23T05_25_21.344092Z.csv',
      key='hyundairotem/',
      bucket_name='hyundairotem',
      replace=True
  )
def download_file_from_minio(key: str, bucket_name: str, local_path: str):
  s3_hook = S3Hook(aws_conn_id='minio_connection')
  s3_hook.download_file(key=key, bucket_name=bucket_name, local_path=local_path)


upload_task = PythonOperator(
  task_id='upload_file_to_minio',
  python_callable=upload_file_to_minio,
)
download_task = PythonOperator(
      task_id='download_file_from_minio',
      python_callable=download_file_from_minio,
      op_kwargs={'key':'query_result_2023-03-23T05_25_21.344092Z.csv',
                 'bucket_name':'hyundairotem',
                 'local_path':'/opt/airflow/downloads/'}
  )
```
  
* MongoHook
```python
def save_data_to_mongodb():
  mongo_hook = MongoHook(conn_id='mongo')
  collection = mongo_hook.get_collection('mydb', 'mycollection')
  data = {'name': 'John', 'age': 30, 'city': 'New York'}
  collection.insert_one(data)

save_data_mongo_task = PythonOperator(
      task_id='save_data_to_mongodb',
      python_callable=save_data_to_mongodb,
  )
````
