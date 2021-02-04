---
layout: post
title: Neural Collaborative Filtering 논문 리뷰
tags: [recommender system, deep learning, paper]
comments: true
---


## Learning from Implicit Data

$$M$$을 사용자의 수, $$N$$을 아이템의 수라고 가정하며, 사용자의 implicit feedback으로부터 사용자와 아이템 간 인터랙션을 나타내는 행렬 $$\mathbf{Y} \in \mathbb{R}^{M \times N}$$ 을 나타낼 수 있다. 행렬의 요소가 의미하는 바는 다음과 같다.

$$
y_{ui} =
\begin{cases}
1, & \text{if interaction (user }u \text{, item }i\text{) is observed;} \\
0, & \text{otherwise.}
\end{cases}
$$

$$y_{ui}$$의 값이 1이라는 것은 사용자와 아이템 간에 인터랙션이 존재한다는 (observed) 의미이며 (사용자가 해당 아이템을 좋아한다는 뜻이 아니다), 반대로 값이 0이라는 것은 둘 사이에 인터랙션이 없었다는 (unobserved) 것을 의미한다.

관찰된 엔트리 ($$y_{ui} = 1$$) 들은 최소 사용자의 관심을 반영하기라도 하지만, 관찰되지 않은 엔트리 ($$y_{ui} \neq 1$$) 들은 단순히 누락된 아이템으로 인식될 것이며, 이는 **네거티브 피드백 부재** 라는 문제를 발생시킨다.

> **네거티브 피드백 부재가 무슨 소리인가?** 이 문제는 이 모델에 한정되어 발생하는 것이 아니라 implicit feedback 방식을 사용하는 모든 모델에서 발생할 수 있다. explicit feedback 같은 경우에는 주어진 데이터에서 positive feedback과 negative feedback을 명확하게 나타내는 정보(예: 리뷰 별점)를 가져올 수 있는 반면, implicit feedback 방식에서는 데이터가 긍정/부정을 나타내기보다는 그저 인터랙션의 발생 유무만을 나타내기 때문에 (예: 상품 클릭 횟수) 사실은 긍정도 아니고 부정도 아니게 된다.
하지만 해당 인터랙션을 최소한 사용자의 관심으로 보고 그것을 포지티브 피드백이라고 가정한다면, 네거티브 피드백은 무엇으로 정의를 해야 하는지에 대한 문제에 봉착하게 된다. 이것이 네거티브 피드백 부재라고 불리는 이유이다. 이 논문에서는 이러한 문제를 해결하기 위해 관찰되지 않은 모든 아이템들을 네거티브 피드백으로 정의하거나, 아니면 그 중 몇 개만 샘플링해서 네거티브 피드백으로 정의한다.

implicit feedback 방식으로 추천하는 문제는 행렬 $$\mathbf{Y}$$에서 관찰되지 않은 엔트리들의 스코어로 정의되며, 아이템에 대한 순위로 사용된다. 공식적으로 이는 $$\hat{y}_{ui} = f(u, i \vert \Theta)$$를 학습하는 것으로서 정의될 수 있다. 여기서 $$\hat{y}_{ui}$$는 인터랙션 $$y_{ui}$$의 예측 점수이며 $$\Theta$$는 모델 파라미터를 나타내고 $$f$$는 모델 파라미터로부터 예측 점수를 구해내는 함수이다 (우리는 이를 *interaction function* 이라고 부른다).

파라미터 $$\Theta$$를 학습하기 위해서 일반적으로 objective-function을 최적화하는 머신러닝 패러다임을 따른다. 가장 흔하게 사용되는 objective-function에는 두 가지로 **pointwise loss**와 **pairwise loss** 가 있다. explicit feedback 방식에서 자주 사용되던 작업을 자연스럽게 확장시키면 pointwise loss 학습은 대개 $$\hat{y}_{ui}$$와 $$y_{ui}$$ 간의 squared loss를 최소화하는 회귀 프레임워크를 따른다. 네거티브 데이터의 부재를 처리하기 위해서, 관찰되지 않은 엔트리 전부를 네거티브 피드백으로서 처리하거나, 아니면 그 중 몇 개만 샘플링해서 네거티브 피드백으로 처리한다.

반면 pairwise 학습의 아이디어는 관찰된 엔트리들이 관찰되지 않은 엔트리들보다 더 높게 랭크되어야 한다는 것이다. 그러니까 $$\hat{y}_{ui}$$와 $$y_{ui}$$ 간의 손실을 줄이는 것 대신에, pairwise 학습에서는 관찰된 엔트리 $$\hat{y}_{ui}$$와 관찰되지 않은 엔트리 $$\hat{y}_{uj}$$ 간의 마진을 최대화한다.

> **마진을 최대화한다는 것이 무슨 소리인가?** 관찰된 엔트리가 관찰되지 않은 엔트리보다 더 높게 랭크되어야 한다는 것의 의미는 관찰된 엔트리가 관찰되지 않은 엔트리보다 더 큰 값을 가져야 한다는 뜻이다. 이 말은 두 값 사이의 격차가 더 벌어져야 한다는 뜻이며, 그러기 위해서는 두 값 사이의 마진을 오히려 크게 만들어야 한다. 그렇게 한다면 모델은 관찰된 엔트리들과 관찰되지 않은 엔트리들의 사이를 서서히 구분시켜가며 학습이 진행될 것이다.

한 발자국 더 나아가, 우리의 NCF 프레임워크는 $$\hat{y}_{ui}$$를 계산하기 위해 뉴럴넷을 사용해서 interaction function $$f$$를 파라미터화한다. 즉, 이는 자연적으로 pointwise 방식과 pairwise 방식 학습 둘 다 지원한다.

---

## Matrix Factorization

MF에서는 각 사용자와 아이템을 실수로 이루어진 벡터로 잠재 특성을 나타낸다. 각각을 $$\mathbf{p}_u$$와 $$\mathbf{q}_i$$로 정의한다. MF는 인터랙션 $$y_{ui}$$를 다음과 같이 $$\mathbf{p}_u$$와 $$\mathbf{q}_i$$의 내적(inner product)으로 계산한다.

$$
\hat{y}_{ui} = f(u, i | \mathbf{p}_u, \mathbf{q}_i) = \mathbf{p}_u^T \mathbf{q}_i = \sum_{k=1}^K p_{uk} q_{ik}
$$

여기서 $$K$$는 잠재 공간의 차원을 의미한다. 볼 수 있듯이, MF는 잠재 공간의 각 차원이 서로 독립적이고 그들이 동일한 가중치로 선형 결합한다고 가정하면서 사용자와 아이템의 잠재 요소 간 two-way 인터랙션을 모델링한다.

![figure-1](/images/ncf-fig1.PNG)

위 예제는 저차원의 잠재공간에 있는 사용자와 아이템 간의 복잡한 인터랙션을 계산하기 위해서 inner product 라는 심플하고 고정된 연산을 사용하면 발생할 수 있는 MF의 한계를 보여준다. 이 문제를 해결하기 위한 한 가지 방법은 잠재  요소 $$K$$의 숫자의 늘리는 것이다. 하지만 이는 모델의 일반화에 불리하게 작용될 수 있으며 (예: 오버피팅), 특히 sparse settings 경우에는 더욱 그렇다. 우리는 데이터로부터 DNN을 사용한 interaction function 학습을 진행함으로써 이 문제를 해결한다.

---

## General Framework

![neural-collaborative-filtering-framework](/images/ncf-fig2.PNG)

위 그림처럼 사용자-아이템 인터랙션 $$y_{ui}$$를 모델링하기 위해 멀티 레이어를 적용시킨다. 한 레이어 출력은 다음 레이어의 입력이 된다. 최하단 입력 레이어는 두 개의 특징 벡터 $$\mathbf{v}_u^U$$와 $$\mathbf{v}_i^I$$로 구성되며, 각각 사용자 $$u$$와 아이템 $$i$$를 나타낸다. 이들은 context-aware, content-based, neighbor-based 와 같이 더 넓은 범위의 모델링을 지원할 수 있도록 커스텀될 수 있다. 여기서는 순수 collaborative filtering setting에 집중할 것이기 때문에 사용자와 아이템만을 가지고 one-hot 인코딩을 통해 binarized sparse vector로 변환한 벡터들을 입력 특징으로서 사용하도록 한다.

위의 입력 레이어는 임베딩 레이어이며, sparse 표현을 dense 표현으로 투사시켜준다. 주어진 사용자 (또는 아이템) 임베딩은 잠재 요인 모델 입장에서 해당 사용자 (아이템)에 대한 잠재 벡터로 보여질 수 있다. 사용자 임베딩과 아이템 임베딩은 그 후 잠재 벡터라는 형태에서 스코어로 변환되기 위해 *neural collaborative filtering layers* 라고 불리는 멀티 레이어 뉴럴 아키텍쳐에 들어간다. 뉴럴 CF의 각 레이어는 사용자와 아이템 간 특정 잠재 구조를 발견하기 위해 커스텀될 수 있다. 마지막 히든 레이어 $$X$$의 차원은 모델의 capacity를 결정한다. 최종 출력 레이어는 예측된 스코어 $$\hat{y}_{ui}$$이며, 학습 과정은 이 스코어 $$\hat{y}_{ui}$$와 타겟값 $$y_{ui}$$ 사이의 pointwise loss를 줄임으로써 진행된다. 우리는 모델을 학습하기 위한 또 다른 방법으로 pairwise 학습이 있다는 것을 유념해야 하며, 이 학습은 Bayesian Personalized Ranking과 margin-based loss와 같은 것들을 사용할 수 있다.

각설하고 NCF의 예측 모델을 공식화하면 다음과 같다.

$$
\hat{y}_{ui} = f(\mathbf{P}^T \mathbf{v}_u^U, \mathbf{Q}^T \mathbf{v}_i^I | \mathbf{P}, \mathbf{Q}, \Theta_f)
$$

여기서 $$\mathbf{P} \in \mathbb{R}^{M \times K}$$ 이고 $$\mathbf{Q} \in \mathbb{R}^{N \times K}$$ 이며 각각 사용자와 아이템에 대한 잠재요인 행렬을 나타낸다. 그리고 $$\Theta_f$$는 interaction function $$f$$의 모델 파라미터를 나타낸다. 그리고 $$f$$는 여러 계층으로 정의되어 있기 때문에 다음과 같이 공식화 된다.

$$
f(\mathbf{P}^T \mathbf{v}_u^U, \mathbf{Q}^T \mathbf{v}_i^I) = \phi_{out}(\phi_{X}(...\phi_{2}(\phi_{1}(\mathbf{P}^T \mathbf{v}_u^U, \mathbf{Q}^T \mathbf{v}_i^I))...))
$$

$$\phi_{out}$$과 $$\phi_x$$는 최종 출력 레이어와 $$x$$번째 neural collaborative filtering 레이어에 해당한다.


### Learning NCF

모델 파라미터를 학습하기 위한 기존의 pointwise 방법은 대체로 다음과 같이 squared loss를 통한 회귀를 수행한다.

$$
L_{sqr} = \sum_{(u, i) \in \mathcal{Y} \cup \mathcal{Y}^-} w_{ui}(y_{ui} - \hat{y}_{ui})^2
$$

여기서 $$\mathcal{Y}$$는 행렬 $$\mathbf{Y}$$에서 관찰된 인터랙션들의 집합이고 $$\mathcal{Y}^-$$는 네거티브 인스턴스들의 집합, 즉 관찰되지 않은 인터랙션 전체 또는 샘플링된 몇몇개의 집합이다. 그리고 $$w_{ui}$$는 트레이닝 인스턴스 $$(u, i)$$에 대한 가중치를 의미한다. squared loss는 관측값이 가우시안 분포의 형태를 띨 때 의미가 있기 때문에 implicit data에는 적용이 어려울 수 있다. 왜냐하면 타겟값 $$y_{ui}$$는 $$u$$가 $$i$$와 인터랙션이 있었는지의 여부를 나타내는 1과 0으로 표현되기 때문이다. 이에 따라 우리는 이러한 implicit data의 이진적 특징에 특히 주목하는 pointwise NCF를 학습하기 위한 통계적 접근방식을 소개한다.

implicit feedback은 자연적으로 one-class인 것을 생각해보면, $$y_{ui}$$에게서 0 또는 1이라는 값만 볼 수 있다 (1: $$u$$는 $$i$$와 관련됨, 0: 그렇지 않음). 예측 점수 $$\hat{y}_{ui}$$는 그럼 결국 $$i$$가 $$u$$와 얼마나 관련 있는지를 표현하게 된다. NCF를 이러한 확률적 해석에 접목시키려면 우리는 출력 $$\hat{y}_{ui}$$의 범위를 [0, 1]로 제한해야 하는데 이렇게 해야 확률 함수 (예: *Logistic* 또는 *Probit* 함수)를 사용했을 때 더 쉽게 원하는 결과를 달성할 수 있다. 이때 확률 함수는 출력 레이어 $$\phi_{out}$$에 대한 activation function으로서 사용된다. 이러한 세팅과 함께 우리는 우도함수를 다음과 같이 정의할 수 있다.

$$
p(\mathcal{Y}, \mathcal{Y}^- | \mathbf{P}, \mathbf{Q}, \Theta_f) = \prod_{(u, i) \in \mathcal{Y}} \hat{y}_{ui} \prod_{(u, i) \in \mathcal{Y}^-} (1 - \hat{y}_{uj})
$$

해당 우도의 음의 로그를 취하면서 다음과 같이 손실을 구한다.

$$
\begin{matrix}
L &=& - \sum_{(u, i) \in \mathcal{Y}} \log \hat{y}_{ui} - \sum_{(u, j) \in \mathcal{Y}^-} \log (1 - \hat{y}_{uj}) \\\\
&=& - \sum_{(u, i) \in \mathcal{Y} \cup \mathcal{Y}^-} y_{ui} \log \hat{y}_{ui} + (1 - y_{ui}) \log (1 - \hat{y}_{ui})
\end{matrix}
$$

이는 *binary cross-entropy loss* (또는 *log loss*로도 알려짐) 와 동일하다. 이 손실을 줄이면서 NCF 모델이 학습되어야 한다.

---

## Generalized Matrix Factorization (GMF)

여기서는 첫 번째 뉴럴 CF 레이어를 다음과 같이 정의한다.

$$
\phi_1 (\mathbf{p}_u, \mathbf{q}_i) = \mathbf{p}_u \odot \mathbf{q}_i
$$

$$\odot$$은 벡터 간의 element-wise product를 의미한다. 이 결과로 나온 벡터를 출력 레이어에 투사시킨다.

$$
\hat{y}_{ui} = a_{out}(\mathbf{h}^T(\mathbf{p}_u \odot \mathbf{q}_i))
$$

$$a_{out}$$는 activation function을 의미하고 $$\mathbf{h}$$는 출력 레이어의 edge weight를 의미한다. 직관적으로, 만약 우리가 $$a_{out}$$에 항등함수를 사용하고 $$\mathbf{h}$$에 모든 요소가 1로 초기화된 uniform-vector를 사용한다면, 이는 기존의 MF와는 다를 게 없다는 사실을 알 수 있다.

$$
\hat{y}_{ui} = a_{out}(\mathbf{h}^T(\mathbf{p}_u \odot \mathbf{q}_i)) = 1(1(\mathbf{p}_u \odot  \mathbf{q}_i)) = \mathbf{p}_u \odot \mathbf{q}_i = \text{MF}
$$

다만 여기서 $$\mathbf{h}$$가 uniform 제약사항에서 벗어나 데이터로부터 학습이 될 수 있도록 한다면 잠재 벡터 각 차원의 중요성이 달라지도록 하는 변형 MF가 만들어진다. 그리고 $$a_{out}$$에 비선형 함수를 적용시키면 기존의 MF 모델보다 더 표현력 있게 될 것이다. 이 작업에서는 activation function으로 시그모이드 함수를 사용하고, $$\mathbf{h}$$는 log loss를 사용했다. 우리는 이를 GMF (*Generalized Matrix Factorization*) 라고 부른다.

---

## Multi-Layer Perceptron (MLP)

NCF는 사용자와 아이템을 모델링하기 위한 두 가지 방법을 적용시키기 때문에 각 방법에서 나온 두 개의 특징들을 concatenate 해주는 것이 직관적인 생각이다. 이는 멀티모델 딥러닝 작업에서 널리 적용되는 방식이다. 하지만 단순히 벡터를 concatenate 하는 것은 사용자와 아이템 잠재 특성 사이의 인터랙션을 설명하지 못하므로 collaborative filtering effect를 모델링하기엔 충분하지 못하다. 이 문제를 해결하기 위해 concatenated vector에 히든 레이어를 추가한다. 둘 사이의 인터랙션을 학습하기 위해 standard MLP를 사용한다. 이런 의미에서 우리는 $$\mathbf{p}_u$$와 $$\mathbf{q}_i$$ 사이의 인터랙션을 학습하기 위해서 기존의 고정된 element-wise product 만을 적용시키던 GMF 대신 모델에 더 높은 유연성과 비선형성을 적용시킬 수 있다는 것이다. 더 정확하게는 NCF 프레임워크에서의 MLP 모델을 다음과 같이 정의할 수 있다.

$$
\mathbf{z}_1 = \phi_1(\mathbf{p}_u, \mathbf{q}_i) = \begin{bmatrix}
\mathbf{p}_u \\
\mathbf{q}_i
\end{bmatrix}, \\
\phi_2(\mathbf{z_1}) = a_2(\mathbf{W}_2^T \mathbf{z}_1 + \mathbf{b}_2), \\
...... \\
\phi_L(\mathbf{z_{L-1}}) = a_L(\mathbf{W}_L^T \mathbf{z}_{L-1} + \mathbf{b}_L), \\
\hat{y}_{ui} = \sigma (\mathbf{h}^T \phi_L(\mathbf{z}_{L-1}))
$$

---

## Fusion of GMF and MLP

여기서 드는 질문: "GMF와 MLP가 서로를 보강하며 더 나은 모델을 만들도록 NCF 프레임워크에 어떻게 적용시키는가?"

간단한 방법은 GMF와 MLP가 같은 임베딩 레이어를 공유하고 그것들의 interaction function 결과를 결합시키는 것이다. 이 방법은 Neural Tensor Network (NTN) 라고 알려진 것과 비슷한 정신을 공유한다. 1단계의 레이어로 이루어진 GMF와 MLP를 결합하는 모델은 다음과 같이 공식화될 수 있다.

$$
\hat{y}_{ui} = \sigma (\mathbf{h}^T a(\mathbf{p}_u \odot \mathbf{q}_i + \mathbf{W} \begin{bmatrix}
\mathbf{p}_u \\
\mathbf{q}_i
\end{bmatrix} + \mathbf{b}))
$$

그러나 GMF와 MLP의 임베딩을 공유하는 것은 합쳐진 모델의 성능을 제한시킬지도 모른다. 예를 들면 GMP와 MLP는 반드시 같은 사이즈의 임베딩을 사용해야만 한다는 암묵적인 규칙이 생긴다. 그러면 두 개의 모델에 대해서 서로 다른 사이즈의 최적값을 갖는 데이터셋에 대해서는 최적화된 모델을 얻는데 실패하게 된다.
합쳐진 모델에 조금 더 유연성을 제공하기 위해 우리는 GMF와 MLP가 독립된 임베딩을 학습하도록 하고 마지막 출력 레이어에서 합쳐주도록 한다.

![neural-matrix-factorization-model](/images/ncf-fig3.PNG)

위 그림이 우리가 제안하는 것을 표현한 것이며 공식은 다음과 같다.

$$
\begin{matrix}
\phi^{GMF} & = & \mathbf{p}_u^G \odot \mathbf{q}_i^G, \\
\phi^{MLP} & = & a_L(\mathbf{W}_L^T(a_{L-1}(...a_2(\mathbf{W}_2^T \begin{bmatrix}
\mathbf{p}_u^M \\
\mathbf{q}_i^M
\end{bmatrix} + \mathbf{b}_2)...)) + \mathbf{b}_L), \\\\
\hat{y}_{ui} & = & \sigma(\mathbf{h}^T \begin{bmatrix}
\phi^{GMF} \\
\phi^{MLP}
\end{bmatrix})
\end{matrix}
$$

여기서 $$\mathbf{p}_u^G$$와 $$\mathbf{p}_u^M$$는 GMF와 MLP 파트에 대한 사용자 임베딩을 의미한다. 이와 비슷하게 $$\mathbf{q}_i^G$$와 $$\mathbf{q}_i^M$$는 GMF와 MLP 파트에 대한 아이템 임베딩을 의미한다. 앞서 논의한대로 MLP 레이어의 activation function은 ReLU이다. 이 모델은 사용자-아이템 잠재 구조를 모델링하기 위해 MF의 선형성과 DNN의 비선형성을 결합한다. 별칭은 *Neural Matrix Factorization (NeuMF)* 이다.

---

## 출처

- [https://arxiv.org/abs/1708.05031](https://arxiv.org/abs/1708.05031)
