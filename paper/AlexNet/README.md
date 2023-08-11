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
#### 네트워크 크기의 제한은 현재 GPU의 메모리에의해 제한되며, 더 빠른 GPU와 큰 데이터 셋이 있다면 성능이 향샹 될 것 이다.

## 2. The Dataset
## 3. The Architecture
## 4. Reducing  Overfitting
## 5. Detail of Learning
## 6. Results
