StarGAN (Unified Generative Adversarial Networks for Multi-Domain Image-to-Image Translation)
=

요약
=
#### 하나의 모델로 multi domain에 대해 translation 한다. input에 이미지와 도메인 정보를 함께 넣어서 condition을 준다. mask vector를 활용하여 모델이 모든 도메인의 label을 컨트롤할 수 있도록 한다.
기존 image-to-image translation은 2개를 넘는 도메인을 다룰때 제한이 있었고
그 이유는 다른 모델들은 모든 이미지 도메인 pair에 대해 독립적으로 여러개가 만들어져야 하기 때문이다.
이를 해결하기 위해, StarGAN을 제안하고 *!!오로지 하나의 모델만으로 multiple 도메인에 대한 translation!!* 을 할 수 있도록 한다.
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
하나의 generator만으로 모든 도메인간의 mapping을 학습하는데, 이는 fixed translation의 학습 (예: 검정머리에서 금발로) 대신에, *!!생성자가 input으로 이미지와 domain information을 함께 받고, 도메인에 따라서 유연하게 이미지를 바꾸도록 학습하기 때문이다.!!*
도메인 정보를 표현하기 위해 label을 사용한다. (binary 혹은 one-hot vector)

또한 *!!domain label에 mask vector란 것을 추가!!* 하여 다른 데이터셋의 도메인간에 joint training을 효과적으로 하도록했다.
이 방법은 모델이 관련없는 label은 무시하고 특정 데이터셋에서 제공된 label에 집중하도록 한다.
핵심들을 요약하면 아래와 같다.

1. StarGAN을 통해 모든 도메인 이미지를 1개의 생성자와 판별자만으로 훈련하여 multi-domain 간의 mapping을 학습한다.
2. mask vector를 활용하여 모델이 모든 도메인의 label을 컨트롤할 수 있도록 한다.

관련 연구
=
- GAN
- conditional GAN
- Image-to-image Translation

StarGAN
=
먼저 단일 데이터셋 내에서 multi-domain translation을 보여주고, 다음으로 multi 데이터셋을 이용한 translation을 보여준다.

### 1. Multi-Domain Image-to-Image Translation
보조 classifier를 추가하여 판별자가 여러 도메인을 컨트롤할 수 있도록한다.
즉, 판별자는 source와 domain label 둘다에 대한 확률분포를 만든다.

#### Adversarial loss
생성자는 이미지 G(x,c)를 만들고 판별자는 진짜 여부를 본다.
논문에서의 D_src(x)는 D에 의해 만들어진 source에 대한 확률분포로 정의한다.

#### Domain Classification Loss
목표는 x를 y로 바꾸면서, 타겟도메인 c로 적절히 분류되는 것이다.
이를 위해 판별자 맨위에 보조 classifier를 추가하고 D와 G를 최적화할때 domain classification loss를 부여한다.
식은 2개의 term으로 나뉘는데, 하나는 D 최적화를 위한 real image loss이고, 다른하나는 G 최적화를 위한 fake image (domain classification) loss이다.

전자는 D에 의해 계산된 도메인에 대한 확률분포이다. 이걸 최소화면서 판별자는 진짜이미지 x를 상응하는 원본도메인 c'에 맞추어 분류한다.
후자는 fake 이미지에 대한 것으로, 생성자는 이 term을 최소화하여 생성되는 이미지가 타겟 도메인 c로 분류되도록 한다.

#### Reconstruction Loss
적대적 loss와 분류 loss를 최소화하여, 생성자는 사실적 이미지를 생성하면서 타겟 도메인으로 분류되도록 한다.
그러나 이것은 input의 도메인 관련부분만 바꾸면서 input 이미지의 내용을 보장하지는 않는다.
이를 해결하기 위해, cycle consistency loss를 생성자에 추가한다.
이것은 생성자가 translated image G(x,c)와 원본 도메인 label c'을 input으로 받으면서 원본 이미지 x를 재구성하도록 한다.
이때 L1 norm을 적용했다.
하나의 생성자를 두 번 사용하는데, 처음엔 원본 이미지를 타겟 도메인에 맞추어 변형하고 두번째는 변형된 이미지로부터 원본 이미지를 재구성한다.

### 2. Training with Multiple Datasets
*!!StarGAN의 중요한 장점은 다른 label type의 다수의 데이터셋을 동시에 사용하고, test시에 모든 label을 컨트롤할 수 있다는 점이다.!!*
그러나 다수 데이터셋의 학습시 이슈는 label 정보는 각 데이터셋에 대해 *!!partially known!!* 이라는 것이다.
CelebA는 머리색, 성별을 가지지만 RaFD에 있는 행복한, 화난 표정을 가지지는 않는다.
이 문제는 변형된 이미지 G(x,c)로부터 input image x를 재구성할때 label vector c'에 대한 완벽한 정보가 필요하기 때문이다.

#### Mask Vector
이를 해결하기 위해 mask vector m 을 사용하여 모델로 하여금 구체적이지 않은 label은 무시하고 특정 데이터셋으로부터 확실하게 알게된 label에만 집중하도록 한다. m을 표현하기 위해 n차원의 one-hot vector를 사용하고 n은 데이터셋의 수이다.
알려진 label c_i의 vector는 binary 특성에 대한 binary vector 혹은 카테고리 특성에 대한 one-hot vector로 표현될 수 있다.

#### Training Strategy
위 mask vector의 수식으로 정의된 domain lable c를 생성자의 input으로 하여 훈련시킨다.
이를 통해 생성자는 구체적이지 않은 label (zero vector인) 을 무시하도록 학습하며 잘 알려진 label에 집중한다.
판별자에서는 모든 데이터셋의 label에 대한 확률분포를 생성하도록 보조 classifier를 확장시킨다.
그래서 multi-task 학습시 판별자는 알려진 label에 대한 classification loss만 최소화한다.
CelebA의 이미지를 훈련할 때, 판별자는 오로지 CelebA에 관련된 label에 대한 classification loss만 최소화한다.

Implemenation
=
#### Improved GAN training
훈련과정을 안정화하고 고품질 이미지를 생성하기 위해 Adversarial loss를 gradient penalty가 있는 wGAN objective로 대체한다.

#### Network Architecture
CycleGAN을 적용하여 생성자는 2개의 downsample ConvNet, 6개의 residual block, 2개의 upsample ConvNet으로 구성된다.
판별자에는 PatchGAN을 참고하여 local image patch가 진짜인지 아닌지 구분한다.

실험
=
세 개의 실험을 하는데, 먼저 얼굴 특성에 대한 기존모델과 StarGAN을 비교하고, 다음으로 얼굴표정에 대한 분류실험, 마지막으로 다수 데이터셋에 대한 image-to-image translation 실험을 한다.

### 1. Base Models
다음 모델을 base로 한다 : DIAT, CycleGAN, IcGAN, 

### 2. Datasets
CelebA, RaFD

### 3. Training

### 4. Results on CelebA

### 5. Results on RaFD

*!!StarGAN의 또 다른 장점은 parameter가 적게 필요하다!!* 는 점이다.
이것은 도메인수에 상관없이 하나의 생성자와 판별자만 있어도 되기 때문이다.
CycleGAN 같은 cross-domain 모델에서는 각 source-target domain pair에 대해 완벽히 다른 모델이 필요하다.

### 6. Results on CelebA + RaFD
마지막으로 multiple dataset에 대한 실험을 한다.
mask vector를 사용하여 두 데이터셋을 jointly하게 훈련시킨다.

#### joint training의 효과
얼굴 keypoint 인식이나 segmentation 같은 low-level task가 공유되는걸 향상시키기 위해 2개의 데이터셋을 다 사용할 수 있다.
이것은 얼굴표정 합성 학습에 도움이 된다.

#### mask vector의 학습된 역할
잘못된 mask vector가 사용되면 얼굴표정 합성에 실패하고, 예시에서는 input 이미지의 나이를 바꾸었다.
이것은 모델이 mask vector에 의해 얼굴표정 label을 unknown으로 보고 무시하고, 얼굴특징 label을 valid로 보았기 때문이다.
얼굴특징 중 하나가 young 이기 때문에, 모델이 zero vector를 input으로 받을때 젊은 이미지를 나이들게 바꾸기 때문이다.
즉, multiple 데이터셋의 모든 label을 포함할때 이미지 변형에 있어 mask vector의 목표된 역할을 적절히 학습한다고 볼 수 있다.

결론
=
하나의 생성자와 판별자만으로 multiple domain에서의 이미지변형이 가능하다.
또한 scalability확장성과 더불어 고품질 이미지 생성의 장점이 있다.
이는 multi-task 학습과제에 대한 일반적인 능력 덕분이다.
그리고 mask vector의 사용은 모델로 하여금 domain label들이 다른 데이터셋들을 활용가능하도록 한다.
따라서 데이터셋의 모든 label을 사용할 수 있다.
