#### github : <https://github.com/by-hwa/kedro_test>

# 1. Install
## 1. Create virtual environment
```bash
pipenv --python 3.10  # kedro는 python version < 3.11
```

## 2. Install Kedro
```bash
pipenv kedro --dev
pipenv kedro-viz --dev
```

## 3. Create Kedro project
```bash
kedro new
```

# 2. Write Project
* Write pipeline and node.
* https://github.com/kedro-org/kedro/tree/main/kedro/extras/datasets

# 3. Run Kedro web
```bash
kedro viz
```

![image](https://github.com/by-hwa/Docs/assets/102535447/370873a2-cc57-43ea-b6a9-734324863eda)
