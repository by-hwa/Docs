# Balancing immediate and long-term goals

## In this chapter
#### Sequential feedback을 통한 학습의 어려움과 단기, 장기 목적의 적절한 벨런스를 찾는 법을 다룬다.
#### MDP로 모델링된 순차적 의사결정 문제에서 행동의 최적의 정책 알고리즘을 개발한다.
#### MDP 환경의 최적의 정책을 찾는다.

#
#### MDP는 RL 환경의 핵심이고, Agent가 state & action space를 통해 agent와 환경이 상호작용하는 법을 설명하고 보상함수를 통한 Agent의 목표 달성, 전환함수를 통한 Agent의 행동에 환경이 어떻게 반응하는지, discount factor를 통한 시간이 행동에 어떻게 영향을 미치는지 설명한다.
#### 여기서는 MDP를 해결하기 위한 알고리즘을 배우고, Agent의 목표와 왜 간단한 계획은 MDP를 해결하기에 충분하지 않은지를 다룬다.
#### MDP를 해결하기위한 근본적인 알고리즘 2가지 Dynamic programming이라 불리는 Value iteration(VI) & Policy iteration(PI)을 다룬다.
#### 그러나 이러한 방법 또한 속임수인 것을 알게되고 이들은 MDP에 대한 Full access와 환경의 Dynamics에 의존하는데 항상 얻을 수 없는 정보이다.
#### 그러나 배우게될 근본적인 개념은 고급 알고리즘을 배우는데 유용하다. 즉, VI & PI 는 사실상 모든 RL & DRL 알고리즘이 시작되는 기반이다.
#### Agent가 MDPdp Full access가 가능하다면 dynamic, reward, expectation을 직접 알 수 있기때문에 불확실성이 없게된다. 
#### Expectation을 직접 계산한다는 것은 탐색이 필요없다는 것 이며 탐색과 활용의 밸런스가 필요없음을 의미한다 또한 상호작용이 필요없고 시행착오 학습 또한 필요하지 않다. 이 전부는 피드백의 평가가 아니라 지도에 활용됨을 의미한다.
#### DRL Agent는 sequential, evaluate, sampled가 동시에 주어지는 피드백을 통해 학습한다.
#### 이번장에서는 evaluate 와 sampled feedback을 통해 학습하고 sequential feedback을 독립적으로 학습하는데 따르는 복잡성을 제거한다. 또한 여기서는 Sequential, Supervised, Exhaust한 feedback으로 부터 학습한다.

## The objective of a decision-making agent
#### Decision making agent의 목표는 Return을 최대화할 action의 sequence를 찾는 것이다.
#### Reward의 합은 작업에 따라 Agent의 episode 또는 전체 수명 동안의 합을 말한다.

#
<img width="521" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/ea82db39-54f5-4d2a-af53-e42771cdf6ef">
<img width="521" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/25d62a73-b208-44cc-b34e-25ee498dba29">

#
<img width="530" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/6694eea4-769c-45d3-b9cb-4400ba268e58">

#### Return을 과거 단계에서 "얼마 벌었어?"와 같이 과거를 되돌아 보는 것으로 생각할 수 있다. 그러나 기본적으로 미래 지향적인 다른 방법 "앞으로의 보상"도 있다.
<img width="495" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/d1e76344-4c47-411a-b887-a58bde0732d1">

#### 만약 discount가 없다면 어떠한 방식으로도 Goal에 도달하기만 한다면 Reward 1, Hole에 도달한다면 Reward 0 을 얻게된다.
#### Agent는 plan이라 불리는 출발부터 Goal까지의 action sequence를 찾아야한다.

#
<img width="478" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/0fbe0494-f93f-4d65-ab2f-6475557e2e63">

#### 위의 계획은 완벽한 계획이다. 하지만 확률적인 환경에서는 높은 확률로 의도하지 않은 방향으로 가기때문에 이 계획은 올바르지 않다.

#
<img width="490" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/9da309f9-2636-4537-aaf3-71e46b281316">

#### Plan의 문제점은 환경의 확률론은 고려하지 않았다.

#
<img width="516" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/61ee4ab5-6dce-4e50-895c-0a6f24100e93">

#### 모든 State에 대한 계획, 보편적인 계획, 정책이 필요하다.

#
#### Agent는 policy을 제시해야하고, 정책은 보편적인 Plan이며, 가능한 모든 state를 다룬다.
#### 정책은 확률적 또는 결정론적이고, 주어진 state에 대한 action-probability distributions 또는 단일 action을 반환한다.

<img width="477" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/b0cb179e-a29c-48f6-9695-385b8493b65b">

#### 정책은 non-terminal state에만 action을 처방한다.
#### 어떻게 Action을 하여야 최적의 행동인지를 알고있음에도 불구하고 환경은 Agent를 hole앞으로 가져가 놓을 수 있다. 이것은 보상이 충분하지 않다는 것을 의미한다.
#### Agent는 Return을 최대화 하려고 한다. 이것이 의미하는 것은 Agent가 환경의 확률성을 고려한 Return을 반환한다는 것이다.

#
#### 자동으로 최적의 policy를 찾는 것이 필요하다.
#### Agent는 최적의 동작을 찾을 수 있도록 도와주는 내부에 유지되는 몇가지 구성요소가 있다.
#### 정책이 있고 주어진 환경에 여러가지 정책들이 있을 수가 있다. 실제로 명확한 환경에서 최적의 다양한 정책들이 있을 수 있다.

#
#### 예상되는 Return을 추정하는데 도움이 되는 Value Function이 있다.
#### 주어진 MDP에 대한 하나의 최적 Value function이 있지만 일반적으로는 여러 value function있다.

### Policies:Per-state action prescriptions


