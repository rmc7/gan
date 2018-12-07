StarGAN (Unified Generative Adversarial Networks for Multi-Domain Image-to-Image Translation)
=

요약
=
#### 하나의 모델로 multi domain에 대해 translation 한다. input에 이미지와 도메인 정보를 함께 넣어서 condition을 준다.
기존 image-to-image translation은 2개를 넘는 도메인을 다룰때 제한이 있었고
그 이유는 다른 모델들은 모든 이미지 도메인 pair에 대해 독립적으로 여러개가 만들어져야 하기 때문이다.
이를 해결하기 위해, StarGAN을 제안하고 *오로지 하나의 모델만으로 multiple 도메인에 대한 translation*을 할 수 있도록 한다.
하나로 통일된 모델구조는 하나의 네트워크 안에서 여러 도메인의 multiple dataset을 동시에 훈련할 수 있도록 한다.
이는 변형된 이미지 품질이 좋으면서도 input 이미지를 어떤 target 도메인에라도 유연하게 바꿀 수 있는 능력을 가지도록 해준다.

소개
=
2개의 다른 도메인의 훈련데이터로부터 모델은 이미지를 한 도메인에서 다른 도메인으로 변형시킨다.
attribute란 term은 머리색, 성별 같이 이미지에서 의미있는 feature이고, attribute value는 검정머리, 갈색머리 같이 특성의 특정한 value이다.
또한 domain은 같은 attribute value를 공유하는 이미지셋으로 본다.

몇몇 이미지셋은 많은 labeled attribute를 가지는데, CelebA는 얼굴특성에 대해 40개의 label을 가지고 있다.
이런 데이터를 통해 여러 도메인의 특성을 따라서 이미지를 바꾸는 multi-domain image-to-image translation이 가능하다.
CelebA와 RaFC를 jointy 훈련시켜서 CelebA의 표정을 RaFD를 통해 학습된 특징을 사용하여 바꿀 수도 있다.

그러나 기존 모델들은 multi domain translation에는 비효율적ㆍ비효과적이었는데, 비효율성은 k(k-1)개의 generator가 훈련되어야 하기 때문이다.
비효과성에 있어선, 얼굴형태 같이 모든 도메인의 이미지로부터 학습된 global feature가 존재하더라도, 개별 generator는 전체 트레이닝셋 데이터를 활용할 수 없고 k개 중 2개의 도메인만 학습이 가능하다.
전체 훈련데이터를 활용하지 못하면 생성이미지의 품질이 좋지 않을 수 있다.
게다가 다른 데이터셋의 도메인들을 joint하여 훈련하는게 불가능한데 왜냐하면 각 데이터셋은 partially labeled이기 때문이다.

이런 문제를 해결하기 위해 연구진은 StarGAN을 제안한다.
하나의 generator만으로 모든 도메인간의 mapping을 학습하는데, 이는 fixed translation의 학습 (예: 검정머리에서 금발로) 대신에, *생성자가 input으로 이미지와 domain information을 함께 받고, 도메인에 따라서 유연하게 이미지를 바꾸도록 학습하기 때문이다.*
도메인 정보를 표현하기 위해 label을 사용한다. (binary 혹은 one-hot vector)

또한 *domain label에 mask vector란 것을 추가*하여 다른 데이터셋의 도메인간에 joint training을 효과적으로 하도록했다.
이 방법은 모델이 관련없는 label은 무시하고 특정 데이터셋에서 제공된 label에 집중하도록 한다.
핵심들을 요약하면 아래와 같다.

1. StarGAN을 통해 모든 도메인 이미지를 1개의 생성자와 판별자만으로 훈련하여 multi-domain 간의 mapping을 학습한다.
2. mask vector를 활용하여 모델이 모든 도메인의 label을 컨트롤할 수 있도록 한다.

관련 연구
=
GAN
conditional GAN
Image-to-image Translation

StarGAN
=
먼저 단일 데이터셋 내에서 multi-domain translation을 보여주고, 다음으로 multi 데이터셋을 이용한 translation을 보여준다.

### 1. Multi-Domain Image-to-Image Translation

### 2. Training with Multiple Datasets




