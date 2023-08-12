# AlexNet

## [ImageNet Classification with Deep Convolutional Neural Networks](https://proceedings.neurips.cc/paper/2012/file/c399862d3b9d6b76c8436e924a68c45b-Paper.pdf)

#### AlexNet code 구현

## Abstract

#### ImageNet LSVRC-2010 contest 에서 1200만개의 고해상도 이미지를 분류하기 위하여 CNN모델을 학습시켰다.
#### Test 데이터에서 top-1 and top-5 error rate를 37.5%, 17%로 이전기술보다 개선되었다.
#### 6000만개의 파라미터와 65만개의 뉴런으로 구성되었고, 몇개의 max-pooling layer를 포함한 5개의 CNN layer, 3개의 Fully-connected layer로 구성되었고 마지막층은 softmax 함수가 있다.
#### 빠른 train을 위하여 non-saturaing(발산함수 ex. relu) 뉴런을 사용하였고, CNN연산을 위한 효과적인 GPU 구현했다.
#### Overfitting을 줄이기 위해, Dropout 을 적용했다.
#### 위 모델의 변형으로 ImageNet LSVRC-2012 에서 또한 top-5 error rate를 15.3%를 달성하였으며, 2등의 26.2%와 많은 격차를 냈다.

## 1. Introduction
#### 현재 객체인식의 접근법에는 머신러닝 필수적으로 사용된다. 성능을 개선하기위하여 우리는 많은 데이터셋을 모으고, 강한 모델을 학습하고, 오버피팅을 방지하기위해 더 나은 기술을 적용한다.
#### 최근까지 라벨링된 데이터는 상대적으로 적었다. 작은 객체인지는 특히 데이터 증강이 이루어진 이후에 대해서 작은 데이터셋에서도 잘 이루어졌다.
#### 현실의 데이터는 매우 다양하기 때문에, 이를 학습하기 위해서는 거대한 학습데이터가 필요하다.
#### 실제로 소규모 데이터 셋의 단점은 널리 알려져 있다. 그러나 이제는 LabelMe, ImageNet과 같은 수많은 레이블된 데이터를 얻을 수 있다. 
#### 몇만개의 이미지중에서 몇천개의 객체를 학습하기 위해서는 거대한 용량의 모델이 필요하다.
#### 그러나 객체인식의 복잡성은 ImageNet 만큼 거대한 데이터셋으로도 해결하지 못할만큼 어렵다는 것을 의미합니다. 그래서 우리의 모델은 우리가 몰랐던 모든 사전지식을 적용해 보강해야했다.
#### CNN 모델은 깊이와 너비를 다양하게 조절할 수 있고, 좋은 정확도를 나타낸다. 게다가 비슷한 사이즈의 기존 신경망에 비해 적은 연결과 파라미터로 쉽게 훈련이 가능했다.
#### 운이좋게도 현재의 GPU의 성능과 잘 최적화된 2D Convolution으로 거대 CNN 을 학습시키기엔 충분했다.
#### 논문의 성과 : <br> 1. ILSVRC-2010 and ILSVRC-2012 에서 거대 CNN 모델을 학습시켜 좋은 성적을 달성했다. <br> 2. 2D Convolution 에 최적화된 GPU 구현으로 공식적으로 사용이 가능함을 증명했다. <br> 3. 성능을 향상시키고 시간을 단축시키는 방법. <br> 4. 오버피팅을 방지하기 위한 효과적인 기술을 사용했다. <br> 5. 5개의 CNN 레이어와 3개의 Fully-connected 레이어로 구성하였는데, 깊이가 매우 중요했다. 하나의 Convolution layer를 지워보았는데 성능의 저하가 발생했다.
#### 네트워크 크기의 제한은 현재 GPU의 메모리에의해 제한되며, 더 빠른 GPU와 큰 데이터 셋이 있다면 성능이 향샹 될 것이다.

## 2. The Dataset
#### ImageNet은 22,000개의 카테고리를 가진 1500만개의 고해상도 데이터 셋이다.
#### ILSVRC는 ImageNet의 부분집합을 사용한다. 대략 120만개의 Train data, 5만개의 Validation data, 15만개의 Test data.
#### ImageNet은 다양한 고해상도 이미지로 구성되어있고, 우리는 고정된 차원의 Input이 필요하다. 따라서 256x256 사이즈로 이미지를 다운샘플링했다. 사각형의 이미지가 주어지면, 짧은 부분의 길이가 256이 되도록 다운샘플링 후 중앙에서 256x256이미지를 추출했다.
#### 이미지에서 평균값을 빼는것 말고는 다른 전처리는 수행하지 않았고, RGB값을 가지는 픽셀들로 훈련을 진행했다.

## 3. The Architecture
#### 요약된 구조는 Figure 2. 를 참고하면 된다.
#### 8개의 훈련된 레이어로 구성되어있고, 5개의 Convolution 레이어, 3개의 Fully-connected 레이어로 구성되었다.
#### 중요도에 따라 섹션 3.1~4의 순서대로 정렬했다.

### 3.1 ReLU Nonlinearity
<img width="412" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/d285d64e-db22-49be-95ca-f3623e5506c5">
Figure1 <br> ReLUs(solid line), tanh(dashed line) <br> CIFAR-10 데이터에 대해 6epoch 만에 error rate 25%를 달성했고, 어떠한 정규화도 사용하지 않았다. <br>ReLU 함수가 수렴함수보다 빠르게 학습하는것을 알 수 있었다.

#### 활성함수로 tanh 함수 또는 Sigmoid 함수를 주로 사용했다. 경사하강법을 사용한 훈련시간 측면에서 이 saturating nonlinearities 함수들이 non-saturating nonlinearities max(0,x) 함수보다 느리다. Nair and Hinton에 의해 non-saturating nonlinearities 함수를 Rectified Linear Units, ReLU 라 명명했다.
#### ReLU가  tanh 함수보다 빠른 학습능력을 보였다. Figure 1.
#### 전통적인 방식을 고려하지 않은 것은 아니다. 예를 들면 avg pooling을 사용한 데이터셋 Caltech-101에서 좋은 성능을 보였다. 그러나, 우리의 목표는 Overfitting을 방지하는 것 이기때문에 Overfitting이 관측되는 효과는 train set을 활동을 가속화 하는 것과 다르다.

### 3.2 Training on Multiple GPUs
#### GTX 580 GPU 는 3GB의 메모리를 가지고 있다. 이는 학습시에 네트워크의 크기를 결정한다. 하나의 GPU로 120만개의 데이터를 학습하기에는 벅찼고 2개의 GPU를 사용하였다.
#### 현재의 GPU는 병렬처리를 하기에 적합하다, GPU간 서로 주메모리에 접근없이 서로 메모리에 접근하고 읽고쓰기가 가능하다. GPU마다 커널(뉴련)의 반을 놓았다.
#### 연결패턴을 선택하는것은 Cross-Validation시에 문제가 되지만, 이것은 계산 양의 비율을 정확하게 조정해준다.
####  "columnar" CNN 아키텍처와 유사하지만, 우리의 아키텍처는 독립적이지 않고 2개의 GPU의 사용으로 에러를 낮췄다.

### 3.3 Local Response Normalization
#### ReLU는 Saturating을 막기위해 정규화를 할 필요가 없다. 그러나 우리는 다음에 나오는 지역 정규화 방법이 일반화를 돕는다는 사실을 발견했다.
#### 특정층에 ReLU를 적용한 후 이 정규화를 적용했다.
#### Jarrett의 지역 대비 정규화와 일부분 닮았지만, 여기에선 평균을 빼지 않기 때문에 '밝기 정규화'라고 부른다. 또 유의미한 에러율의 감소를 보였다.

### 3.4 

## 4. Reducing  Overfitting
## 5. Detail of Learning
## 6. Results
