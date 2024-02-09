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
#### RL에서 모든 Environment의 MDP는 블랙박스로 진행되고 Environment는 문제와 관련된 변수의 집합으로 표현된다.
#### State space는 변수의 집합이 취할 수 있는 모든 가능성을 가진 값의 조합이며, State는 주어진 시간에 변수가 취하는 특정한 값의 집합니다.
#### Agent는 실제 environment의 state에 접근할 수도 있고 못 할 수도 있다.
#### Agent는 environment로 부터 무언가를 관측할 수 있으며, 주어진 시간에 Agent가 인지한 변수의 집합을 Observation이라고 하고 이 변수 들의 가능한 값의 조합을 Observation space라고 한다.
#### State와 Observation 사이의 다른점도 인지해야 한다. 그러나 RL에서 용어를 혼용해서 사용하는 경우가 있긴하다.
#### 모든 state에서 환경은 agent가 선택가능한 action의 집합을 만든들고 이는 종종 모든 스테이드에서 같은 action의 집합을 가지기도 한다. 모든 state에 대해서 가능한 모든 action의 집합을 Action space라고 한다.
#### Agent는 action을 통해 Environment에 영향을 끼치려고 시도하고, Environment는 agent의 action에 반응하여 state를 변화하려한다.
#### 이 transition에 대한 응답에 관한 함수를 Transition Function이라고 한다.
#### Transition 이후 Environment는 새로운 관측을 하고, Environment는 응답에 대한 Reward signal을 제공하며 이 응답함수를 Reward Function이라고 한다.
#### Transition 과 Reward Function을 Environment의 모델 이라고 한다.

#

<img width="522" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/b83b758f-b693-4b37-b1f6-f669f03f5404">

#### Environment는 어떠한 방식으로든 Agent의 action에 반응하며, Agent의 무응답에도 반응한다. 그러나 결국에는 불확실한 내부 프로세스가 있다.
#### MDP에서 상호작용하는 Environment의 성질을 나타내려면 State, Observation, Action, Transition Function, Reward Function을 알아야한다.

### Agent-environment interaction cycle
#### Envionment는 일반적으로 잘 정의된 테스크이며, 이 테스크의 목표는 Reward signal을 통해 정의하는 것이다. 보상신호는 많거나 적을 수 있다.
#### Envionment를 설계할때, Reward Function은 Agent를 원하는 방향으로 학습 시키는 좋은 방법이다.
#### Agent에게 많은 보상신호와 지도를 하였을때, 더 빠르게 학습을 수행하며 많은 bias가 주입되고 돌발 행동은 하지 않게 된다.
#### 하지만 Agent에게 적은 보상신호 와 부족한 지도를 하게된다면, 새로운 행동을 할 기회가 많아지지만 학습시간이 길어지게 된다는 단점이 있다.
#### Agent와 Environment 간의 상호작용의 각 사이클을 Time step이라고 하며 이는 시간의 단위이다.
#### 각 Timestep마다 Agent는 Envionment를 관측, Action 수행, 새로운 Observation과 reward를 받는다. 알고있어야 할 점은 음수의 Reward도 Reward인 점이다.
#### Observation(or state), Action, Reward, New observation(or New state)의 튜플을 Experience tuple이라고 한다.
#### Agent가 해결해야할 문제는 자연스러운 엔딩이 있는 문제일 수도 있고 아닐수도 있다. 자연스러운 엔딩을 가진 테스크의 경우 Episodic Task라고 하며 그렇지 않고 지속적으로 학습을 수행해야하는 경우, Continuing Task 라고한다.
#### Episodic task 의 시작과 끝까지의 Time step순서를 Episode라고 하며, Agent는 테스크를 해결하기 위한 학습을 위해 몇가지 time step과 episode를 가진다.
#### 단일 episode에 대한 수집된 Reward의 합을 Return이라고 하며, Agent는 종종 최대한의 Return을 얻도록 설계된다. Continuing Task에도 종종 Time step limit이 추가되며, episodic task로 볼 수 있게되며 Agent는 최대한의 Return을 얻을 수 있다.
#### 모든 Experience tuple은 Agent를 학습하고 성능을 개선하는데 사용될 수 있고 Agent는 학습을 위한 하나이상의 구성요소가 있다.
#### Agent는 관측에서 행동으로 부터 학습을 맵핑할 수 있고 이를 Policies라고 한다. 또한 Model이라 불리는 새로운 Observation 그리고/또는 Reward으로 부터 매핑학습된다. 그리고 Value function이라 불리는 보상추정관측으러 부터 맵핑학습된다.

### MDPs:The engine of the environment



Reference from grokking Deep Reinforcement Learning
