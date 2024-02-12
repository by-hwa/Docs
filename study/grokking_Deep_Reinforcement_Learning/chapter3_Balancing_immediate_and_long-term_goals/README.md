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

#
### Policies:Per-state action prescriptions
#### Agent는 확률적인 환경에서 π라고 표시되는 정책을 찾을 필요가 있다.
#### 정책은 non-terminal state에서 수행할 action을 규정하는 function이다.
#### 정책은 확률론적 일 수도 있다. action또는 확률 분포를 알려준다.

<img width="426" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/c351626c-c3b5-4d6d-a1e5-50152cd17174">

#### 이 정책을 보자마자 이 정책이 얼마나 좋은지를 생각해 볼 필요가 있다.

<img width="507" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/b4df6043-f50a-4e48-b7b9-8dfd47c89a28">

#### 정책에 번호를 매길 수 있다면, 어느 정책이 더 나은지 비교할 수 있을 것이다.

#
## State-value function:What to expect from here?

#### 주어진 policy state에 번호를 매기는 것은 정책을 비교하는데 도움이 된다.
#### MDP 정책이 주어졌을 때, single state(대부분 Start state)로 부터 시작하여 예상되는 Return을 게산할 수 있어야 한다.

<img width="533" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/57e5c447-736e-428a-85de-56b2b72326b8">

#### 1/3 -> +1, 1/3 -> state10, 1/3 -> state14 1/3은 답의 일부이지만, Reward를 얻지 못하는 2/3를 고려한다.
#### 우리가 가지고 있지않은 다른 State(여기에선 10, 14)의 값의 의존성 때문에 Go-get-it 정책을 따를때 state 14의 가치를 계산하기 쉽지 않다.
#### 앞서 Return은 reward의 합이라 정의했다.
#### Agent가 따르는 정책에 관심을 갖지 않고도 획득한 모든 reward를 합하여 Return을 계산할 수 있다.
#### 그러나 지금 알고자 하는 것은 정책 π를 따를때 예상되는 Return이다.
#### 확률론적 환경에 있기때문에 환경이 우리 정책에 반응하는 모든 가능한 방법을 고려해야한다.

#
#### 정책 π를 따를때 state s 의 value를 정의한다.
#### s의 value은 agent가 s에서 정책 π를 따랐을 때 예상되는 return 이다.
#### 모든 state에 대해서 이를 계산하면, state-value function, V-function, value function(모두 같은 의미)을 얻을 수 있다.
#### 이는 state s에서 정책 π를 따를 때의 예상 return이라고 한다.

<img width="497" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/ad9b9608-505f-432b-bea3-f5bf36a594f0">

#### State의 value가 다른 많은 state value에 어떻게 재귀적으로 의존하는지 주목해야한다.
#### 이 value는 원래 state를 포함하여 다른 state value에 의존 가능하다.
#### 다음 섹션에서는 이러한 방정식을 반복적으로 해결하고 FL이나 실제와 같은 다른환경에서든 어떤 state-value function을 얻을 수 있는 알고리즘을 살펴볼 때, state와 연속적인 state 사이의 재귀 관계를 다시 설명한다. 또한 RL agent에서 일반적으로 볼 수 있는 다른 구성요소를 탐색하고 이 value를 계산하는 법을 배운다.
#### state-value function은 value function, V-fuction, Vπ(s)로도 표현된다.

#
### Action-value function:What should i expect from here if i do this?
#### 자주 물어보아야 할 질문은 state의 value가 아니라 state에서 action을 했을 때의 value이다.
#### 각 정책의 action중 어떤 행동이 더 나은지, get-it-go 의 right or carefully 의 down?
#### 동일한 정책하에서 다른 action들을 비교하여 더 나은 행동을 고를 수 있고 정책을 개선할 수 있다.
#### Action-value function은 Q-function or Qπ(s,a)로 알려져 있다.
#### 이 Function은 agent가 state에서 action을 수행한 이후 정책 π를 따랐을 때의 예상되는 return을 정확히 예측한다.
#### Control problem이라 불리는 정책 개선 문제를 다룰때, action-value function이 필요하다.
#### MDP 없이 단지 모든 state의 값 만으로 어떻게 action을 결정할 수 있나요.
#### V-function은 환경의 dynamics를 잡을 수 없지만 Q-function은 환경의 dynamics를 약간 포착하고 MDP없이 정책을 개선 할 수 있다.

<img width="498" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/a40ccb25-28db-4aaa-a33f-49544ff0e4ad">

#
### Action-advantage function:How much better if I do that?
#### Action-advantage function은 action-value function과 state-value function에서 파생되었다.
#### 이 function은 advantage function, A-function, Aπ(s,a)라고도 알려져 있다.
#### 앞선 function들과의 차이점은 action-value function과 state-value function의 차이이다. (q-v)

<img width="495" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/423fb938-1e1f-4e65-8a6f-f8839234119d">

#### Advantage function은 action a를 수행하는 것이 정책π를 따르는 것 보다 얼마나 더 나은지를 설명한다.
#### 기본 action보다 action a를 선택하는게 이점이다.
#### value는 정책에 따라 달라진다.
#### Qπ(s,a)는 State s에서 action a를 한후 정책π를 따른다고 가정한다.

<img width="527" alt="image" src="https://github.com/by-hwa/Docs/assets/102535447/80f74271-7cd2-444b-8856-4ce5ef0e7415">

#
### Optimality
#### Policies, State-value function, Action-value function, action-advantage function은 행동을 설명, 평가, 개선하는데 사용하는 구성요소이다.
#### 이 구성요소들이 최적이 되었을 때, Optimality라고 한다.
#### Optimal policy는 매 state에서 다른 어떤 policy보다 크거나 같은 예상 return값을 얻을 수 있다.
#### Optimal state-value function은 모든 state에 대한 모든 policies에 걸쳐 최대의 value를 얻는 state-value function이다.
#### Optimal action-value function은 모든 state-action pair에 대한 모든 policies들에 걸쳐 최대의 value를 얻는 action-value function이다.
#### Optimal action-advantage function은 비슷한 패턴을 따르지만, 어떠한 행동도 Optimal state-value function으로 부터 어떠한 advantage를 얻을 수 없기 때문에 모든 state-action pair에 대한 optimal advantage function은 0보다 작거나 같다.
#### 주어진 MDP에대해 최적 정책은 하나보다 많을 수 있다. 하지만 Optimal state-value function, Optimal action-value function, Optimal action-advantage function은 하나다.
#### Optimal V-function을 안다면 MDP를 이용하여 optimal Q-function에 대한 one-step search를 수행하여 Optimal policy를 얻을 수 있다.
#### Optimal Q-function을 안다면 MDP없이 단지 action에 최대값을 취함으로 V-function을 알 수 있다.
#### 그리고 action에 대해 argmax를 취함으로써 최적의 Q-function을 사용하여 Optimal policy를 얻을 수 있다.

## Planning optimal sequences of actions

Reference from grokking deep reinforcelearning
