**이 강의의 학습 목표**

1.  딥러닝을 위한 효과적이고 현대적인 방법에 대한 이해
2.  인간의 언어에 대한 큰 그림과 언어를 이해하고 생산하는 것에 대한 난관들 이해
3.  PyTorch로 대부분의 NLP 문제를 해결할 수 있는 능력

## **우리가 언어의 의미를 표현하는 방법**

언어학자들이 '의미'에 대해 생각하는 일반적인 방식은 `denotational sementics`, 표시론적 의미론으로, 의미를 사물이 나타내는 것으로 생각한다는 것이다. '의자'라는 단어는 의자와 같은 모든 것을 가리킨다. '달리기'라는 단어를 생각했을 때 떠오르는 일련의 행동이 '달리기'의 의미다.

이 '의미'에 대해 계산적으로(컴퓨터 상으로) 접근하는 것은 어려운 일이다. 사람들이 가장 보편적으로 사용하는 계산적인 접근 방법은 `wordnet` 등을 통해 단어의 사전적 의미와 단어 사이의 관계(동의어, 유의어, 상위어 등)를 알아내는 것이었다.

하지만 이런 종류의 관계는 많은 뉘앙스를 놓치고 있었다. 예를 들어 단어 'good'은 'proficient'와 유의어 관계지만, 'proficient'는 'good'보다 더 많은 함축과 뉘앙스를 담고 있다. 대부분 손으로 만든 자료인 `wordnet`은 이렇듯 불완전하고, 한 단어에 대해 고정된 output을 내며, 단어의 새로운 의미나 신조어, 속어 등을 바로 반영하지 못한다.

## **단어 표현 방법의 개선 과정**

2012년 이전까지의 전통적인 NLP 방식을 조금씩 개선해 나가려는 노력이 있었다. 2013년에 사람들이 neuron style representation을 사용하기 시작하면서 실제로 바뀌기 시작했다.

### **1\. 이산 기호(one-hot vector)를 통한 단어 표현**

전통적인 NLP에서는 단어를 이산 기호인 `**one-hot vectors**`로 표현했다. 예를 들어 국소주의적 표현(localist representation, 1개의 개념을 나타내는 단어를 말하는 듯)인 'motel'과 'hotel'은 아래와 같이 `one-hot vectors`로 표현될 수 있다.

```
motel = [0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0]
hotel = [0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0]
```

하지만 언어에는 수많은 언어가 존재하고, 파생 형태학적으로 새로운 단어를 기존의 단어로부터 파생시킬 수도 있기 때문에, 모든 단어를 이런 식으로 표현하다가는 엄청나게 큰 크기의 벡터가 필요할 것이다. 하지만 단어를 one-hot vector로 만드는 것의 가장 큰 문제는 **단어의 의미와 관계를 이해할 수 없다**는 것이다. 우리는 'motel'과 'hotel'이 비슷한 단어라는 걸 알지만, 수학적으로 두 단어의 one-hot vector는 **직교(orthogonal)**하기 때문에 이 둘 사이에는 유사 관계가 없다.

그래서 사람들은 유사 관계를 표현한 큰 데이터 테이블을 생성하고, 이것을 참고해 단어 사이의 유의 관계를 따져보려고 했다. 하지만 250,000개의 단어들 사이에 유사성 테이블을 만들고자 한다면 약 2.5조의 셀을 만들어야 하므로 이것은 사실상 불가능에 가깝다. 그래서 사람들은 단어를 의미를 담은 벡터 방식으로 표현할 방법을 연구하게 되었다.

### **2\. 문맥을 반영한 단어 표현**

**분산 표현(distribution representation)**은 단어 간 의미론적 유사성을 정량화, 범주화하기 위한 방법이다. 분산 표현을 이용해 만들어진 word vector는 **모든 숫자가 0이 아닌 조밀한 벡터로, 단어의 의미를 숫자로 표현**한 것이다. 이렇게 단어의 유사도를 벡터화하는 작업을 **워드 임베딩(word embedding)**이라고 한다. 개인 랩탑에서 보통 사용하는 워드 벡터의 차원은 50에서 300 정도다. 유사한 문맥에서 나타나는 단어끼리 비슷한 형태의 벡터를 갖는다.

```
banking = {0.286, 0.792, -0.177, -0.107, 0.109, -0.542, 0.349, 0.271}
```

### **3\. word2vec**

2013년에 만들어진 **`word2vec`** 은 워드 벡터를 학습하기 위한 프레임워크다.

> word2vec에 대한 자세한 (한국어) 설명: [https://wikidocs.net/22660](https://wikidocs.net/22660)

-   우리는 많은 양의 말뭉치(NLP에서는 Dataset을 말뭉치라고 표현한다)를 갖고 있다.
-   모든 고정된 어휘들은 **벡터**로 표현된다.
-   텍스트 내 각각의 포지션 t를 지나면서 중심 단어(center words) c와 문맥 단어(context words) o를 갖는다.
-   c와 o 의 word vector간 유사성을 사용해 c가 주어졌을 때의 o의 확률을 계산한다. (역도 가능)
-   이 확률을 최대화하는 word vectors를 계속 조정해 나간다.

#### Word2vec의 목적함수

첫번째 식은 $t = 1, ..., T$ 의 각 포지션에서 고정된 window size m 내의 **center word가 주어졌을 때 context words를 예측**하는 likelihood다. $P(W\_{t+j} | W\_t;\\theta)$는 center word $w\_t$가 주어진 경우 context word $w\_{t+j}$가 주어질 확률이다. t에 따라 윈도우를 움직이며(c를 중심으로 하는 슬라이딩 윈도우) $P(W\_{t+j} | W\_t;\\theta)$를 계산한다.

word2vec의 목적함수는 슬라이드의 두번째 식으로, 위의 식 $L(\\theta)$에 음수 부호와 로그를 씌운 negative log likelihood 형태다. 위 식에 log를 씌우면 곱셈식을 덧셈식으로 변환함으로써 계산 복잡도를 낮추고, 음수 부호를 붙여 최대화가 아닌 최소화를 할 수 있도록 변형한 것이다. 이 목적함수가 작아질 수록 다른 단어를 예측하는 성능이 좋아진다.

$P(W\_{t+j} | W\_t;\\theta)$ 는 어떻게 계산할 수 있을까? 우리는 단어 하나 당 2개의 벡터를 사용하는데, 이 두 벡터의 평균을 해당 단어의 word vector로 사용한다.

-   $v\_w$: 단어 w가 center word일 때
-   $u\_w$: 단어 w가 context word일 때

그러면 중심 단어 c와 문맥 단어 o에 대해 이렇게 식을 만들 수 있다.

$$P(o|c) = \\cfrac{exp(u^T\_ov\_c)}{\\sum\_{w\\in V}exp(u^T\_wv\_c)}$$

분모의 $exp(u^T\_ov\_c)$에서는 내적을 통해 o와 c 사이의 유사도를 계산한다. 내적값이 커질수록 높은 확률을 갖게 된다. 우리가 구하고자 하는 것은 확률이므로, 무조건 양수 값을 가지도록 제곱해 줘야 한다.

#### 최적화

cost function을 최소화하기 위해서 경사하강법을 사용한다. 현재 $\\theta$에 대해서 cost function의 그래디언트를 계산하고, 음의 경사 방향으로 이동하며 최적화하는 방법이다.

여기서 $\\theta$는 모든 단어들에 대한 word vectors며, 모든 단어가 2개의 word vectors를 갖는다는 점에 주의해야 한다.

하지만 cost function $J(\\theta)$는 말뭉치 내의 모든 윈도우를 다루기 때문에, $J(\\theta)$의 그래디언트를 계산하는 것은 비용이 굉장히 많이 든다. 이런 문제를 해결하기 위한 방법은 SGD(Stochastic gradient descent)로, sample window를 정해서 업데이트하는 방식을 사용한다.
