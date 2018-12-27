BigGAN
==
요약
1.
2.
3.

SCALING UP GANS
==
baseline은 hinge loss를 쓰는 SAGAN을 쓴다. G에는 class-conditional BatchNorm과 함께 class 정보를 준다.
최적화는 Zhang 논문에서의 학습률을 절반으로 하고, 1번의 생성스텝에 2번의 판별스텝을 하도록 한다.

batch size를 늘릴수록 Inception Score가 상승한다.
추측하기론 각 batch가 더 많은 모델을 커버하여 각 네트워크에 더 좋은 gradient를 주기 때문일 것이다.
주목할 부수효과는 더 적은 iteration에도 더 좋은 성과가 나오는 것인데, 다만 불안정하고 training collapse를 겪는다.
이 부분은 section 4에서 다루는데, collapse 전에 훈련을 멈추고 그 시점의 score를 report한다.




