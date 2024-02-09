# Mathematical foundations of reinforcement learning

## In this chapter
#### 강화학습의 핵심 요소에 대해 배운다.
#### 수학적 프레임 워크 Markov decision processes를 사용하여 강화학습 환경의 Sequential 의사결정 문제를 표현한다.
#### Agent 학습 문제를 해결하기 위한 환경을 구축한다.

#
#### 불확실성 하에서 복잡하고 순차적인 의사결정을 하는 단계에서의 주목해야할 단어 3가지
1. #### Complex, Agent는 거대한 state와 action space를 통해 환경을 학습시킨다.
* #### 일반적으로 정확한 학습이 어려운 이유는 Sampled feedback을 통하여 학습하기 때문이다.
2. #### Sequential, 이가 가지고 있는 많은 문제는 지연된 결과를 받는다는 것이다.
* #### Sequential feedback을 통해 학습하기 때문에 지난 결정에 신용을 할당하는 것은 어려운 문제이다.
3. #### Uncertainty, 우리의 행동이 내부 작업에 어떠한 영향을 끼지는지 알 수 없다.
* #### Evaluative feedback을 통해 학습하기 때문에 탐색과 활용 사이의 적절한 밸런스를 찾는 것은 어렵다.

## Components of reinforcement learning

<img width="498" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/b087af04-0b5e-4fee-ac6c-57f966eb9e26">

#### RL의 핵심 두가지 구성요소는 Agent와 Environment이다.
#### Agent는 문제에 대한 해결책인 의사결정자 이다.
#### Environment는 문제를 표현하는 것이다.
#### RL과 다른 ML의 근본적인 접근법의 차이는 Agent와 Environment의 상호작용이다.
#### Agent는 Action을 통해 Environment에 영향을 끼려고 시도하고, Environment는 Agent의 Action에 반응한다.

#
### The agent:The decision maker

<img width="527" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/b2743398-ecd7-4e45-b875-1ba72421bc60">

#### Agent는 RL 큰 그림의 의사결정권자이다.
#### Agent는 내부 구성요소와 프로세스를 가지고 있고, 또한 각각을 독특하게 만들고 특정한 문제를 해결하기 좋다.
#### 모든 Agent는 학습을 위한 데이터 수집방법인 상호작용 구성요소가 있고, 현재 행동을 평가한다.
#### 그리고 전체적인 성능 향상을 위해 내부 구성요소를 개선한다.

#
### The Environment:Everything else
#### 대부분의 현실의 의사결정 문제를 RL Environment로 압축할 수 있다.
#### RL에서 Decision-making process를 표현하는 일반적인 방법은 수학적 프레임 워크인 Markov decision processes(MDPs)를 이용하여 문제를 모델링하는 것이다.
#### RL에서 모든 Environment의 MDP는 블랙박스로 진행된다.
#### 

Reference from grokking Deep Reinforcement Learning
