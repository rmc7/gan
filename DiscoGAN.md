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

모델은 toy domain과 실제 이미지 실험에서 다른 모델보다 mode collapse 문제를 적게 겪고 2개 도메인간의 양방향 mapping을 학습한다.

모델
=
### 1.수식
강한 제약은 최적화가 어려우므로 가벼운 최적화 관점에서 거리인 d(G_BAㅇG_AB(x_A),x_A)를 최소화한다.
d(G_ABㅇG_BA(x_B),x_B)도 최소화한다.

G_AB를 도메인B에 mapping하는 것은 최적화가 어려워서 GAN loss를 최소화하여 제약을 약하게한다.

### 2.Notation and Architecture
Generator는 G_AB : R_A 64x64x3을 R_B 64x64x3으로 denote하고, input에서 output 도메인으로 denote하고, input에서 output 사이즈로 denote한다.
Discriminator는 D_B : R_B 64x64x3을 [0,1]로 바꾸고 subscript B는 도메인 B의 이미지를 구별하도록 한다.
G_BA와 D_A도 유사하게 사용된다.

각 Generator는 64x64x3 이미지를 받아서 encoder-decoder pair에 넣는다.
encoder는 4x4 필터의 ConvNet이며 leakyReLU를 쓴다.
decoder는 4x4 필터의 DeconvNet이며 ReLU를 쓰고 output은 64x64x3의 타겟도메인 이미지다.
encoder와 decoder의 layer수는 도메인에 따라 4개 혹은 5개를 사용한다.

Discriminator는 Generator의 encoder와 유사하다.
ConvNet에 LeakyReLU를 쓰고 4x4 필터의 추가적인 ConvNet을 쓰고 마지막에 sigmoid로 output을 0~1 사이로 만든다.

### 3.GAN with Reconstruction Loss
original GAN은 gaussian noise z를 쓰는데, 이 모델에선 약간 수정을 해서 input으로 noise 대신 이미지를 쓴다.
기존 구조는 도메인A에서 도메인B로만 가는 단방향 mapping만 학습하므로, 2차 generator를 추가하여 도메인B에서 도메인A로 다시 mapping한다.
또한 input 이미지를 재구성된 이미지와 비교하기 위해 reconstruction loss를 추가했다.

Generator G_AB는 2개의 loss를 받는다.
하나는 reconstruction loss로 원본 이미지가 2번의 생성 이후에 얼마나 잘 구성되었는지 측정하며,
다른 하나는 standard GAN generator loss로 도메인 B에 생성된 이미지가 얼마나 사실적인지 측정한다.
Discriminator는 standard GAN discriminator loss를 받는다.

Training 하는동안, generator G_AB는 2개의 제약 아래에 도메인A에서 B로의 mapping을 학습한다.
하나는 도메인A에서 B로 mapping, 다른 하나는 도메인B에 mapping한 것이 도메인A로 재구성되는 것이다.
그러나 이 모델은 B에서 A로의 mapping을 제약하는데 부족하며, 2개의 조건은 단독으로 cross-domain 관계를 보장하지 않는다.
왜냐하면 이 제약들을 만족하는 mapping은 단방향이기 때문이다.
즉, mapping은 bijection이 아니라 injection이며 일대일 대응을 보장하지 않는다.

2개의 multi-modal image 도메인A와 B를 고려하면, mode collapse 문제가 생길 수 있다.
즉, 한 도메인의 여러 mode들이 다른 도메인의 단일 mode에 mapping할 수 있는 것이다.
알려진대로 standard GAN에 reconstruction loss를 추가하면 mode collapse의 예방을 시도할 수 있다.
하지만 이 loss는 2개 상태의 oscillation진동을 만들뿐 정말로 mode collapse를 해결하지는 못한다.

### 4.제안 모델: Discovery GAN
커플로 이뤄진 2개의 모델은 각각 하나의 도메인에서 다른 하나로의 mapping과 재구성을 위한 reverse mapping을 학습한다.
2개 모델은 동시에 학습된다. G_AB와 G_BA는 파라미터를 공유하고 생성된 이미지 x_BA와 x_AB는 각각 discriminator L_D_A와 L_D_B에 들어간다.
이전 모델들과 다른 점은 양 도메인의 input 이미지가 재구성되고 2개의 reconstruction loss가 있다는 것이다 : L_CONST_A와 L_CONST_B

*2개 모델의 커플링으로 인해, 전체 generator loss는 각 부분 모델의 GAN loss와 reconstruction loss의 합이다.*
*유사하게 전체 discriminator도 D_A와 D_B의 loss의 합이다. 이는 도메인A와 B에서 진짜 이미지와 가짜 이미지를 구별한다.*

*따라서 2개의 GAN loss와 reconstruction loss로 bijection mapping이 달성되고 (cross-domain 관계로 정의한) 일대일 대응이 발견될 수 있다.*

실험
=


