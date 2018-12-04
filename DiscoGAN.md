요약
=
unpaired data에서 서로 다른 도메인 사이의 관계를 발견하는 것을 GAN을 통해 학습한다.
이를 통해 네트워크는 문양이나 얼굴특징 등을 유지하며 한 도메인에서 다른 도메인으로 스타일을 transfer한다.

소개
=
이렇게 크로스 도메인의 관계를 찾는 모델을 DiscoGAN이라고 한다.
이전 모델과는 달리, pair label과 pre-training이 없어도 된다.
모델은 한 도메인의 이미지를 input으로 받아서 다른 도메인에서 이에 상응하는 이미지를 생성한다.

모델의 핵심은 2개의 다른 커플로 된 GAN이며, 각 Network는 생성함수가 각 도메인을 맞은편 도메인으로 map하도록 한다.
각 핸드백 이미지를 기반으로 신발 이미지를 생성할 때, reconstruction loss를 통해 생성된 이미지가 핸드백 이미지의 representation이 되도록하며 또한 GAN loss는 이미지가 신발 도메인에 가까워지도록 한다.
이런 속성을 이용하여 2개 도메인 사이를 양방향으로 잘 mapping하도록 한다.

모델은 toy domain과 실제 이미지 실험에서 다른 모델보다 mode collapse 문제를 겪고 2개 도메인간의 양방향 mapping을 학습한다.

모델
=
1.수식
강한 제약은 최적화가 어려우므로 가벼운 최적화 관점에서 거리인 d(G_BAㅇG_AB(x_A),x_A)를 최소화한다.
d(G_ABㅇG_BA(x_B),x_B)도 최소화한다.

G_AB를 도메인B에 mapping하는 것은 최적화가 어려워서 GAN loss를 최소화하여 제약을 약하게한다.

2.Notation and Architecture
Generator는 G_AB : R_A 64x64x3을 R_B 64x64x3으로 denote하고, input에서 output 도메인으로 denote하고, input에서 output 사이즈로 denote한다.
Discriminator는 D_B : R_B 64x64x3을 [0,1]로 바꾸고 subscript B는 도메인 B의 이미지를 구별하도록 한다.
G_BA와 D_A도 유사하게 사용된다.

각 Generator는 64x64x3 이미지를 받아서 encoder-decoder pair에 넣는다.
encoder는 4x4 필터의 ConvNet이며 leakyReLU를 쓴다.
decoder는 4x4 필터의 DeconvNet이며 ReLU를 쓰고 output은 64x64x3의 타겟도메인 이미지다.
encoder와 decoder의 layer수는 도메인에 따라 4개 혹은 5개를 사용한다.



