Unsupervised Image-to-Image Translation Networks (UNIT)
=
요약
=
Unsupervised Image-to-Image Translation은 개별 도메인의 marginal 분포로부터의 이미지를 사용하여 다른 도메인의 이미지들의 joint 분포를 학습하는 것을 목표로 한다.
주어진 marginal 분포에 닿을 수 있는 join 분포 세트는 무한하기 때문에, marginal 분포로부터의 joint 분포에 대한 추가적인 가정없이는 무엇도 추론할 수 없다.
이 문제를 해결하기 위해, shared-latent space 가정을 만들고 Conpled GAN을 기반으로 하여 Unsupervised Image-to-Image Translation Framework를 제안한다.
코드와 추가적인 결과는 https://github.com/mingyuliutw/unit 에서 확인할 수 있다.
요약 영상은 https://www.youtube.com/watch?v=dqxqbvyOnMY&feature=youtu.be 에서 볼 수 있다.

소개
=
컴퓨터비전에서는 Image-to-Image Translation에서 한 도메인의 이미지를 다른 도메인에서 상응하는 이미지로 mapping하는데 문제를 가지고 있다.
예로 저해상도에서 고해상도 이미지로의 mapping, 회색에서 색상 이미지로의 mapping이 있다.
이 문제들은 supervised와 unsupervised에서 모두 발견된다.
supervised에선 다른 도메인에서 상응하는 이미지 pair가 가능하다.
unsupervised에선 서로 다른 도메인으로부터의 이미지가 독립적으로 있다.
상응하는 이미지의 부족으로 UNIT 문제는 어렵게 생각되지만, 트레이닝 데이터 수집이 쉬워서 적용성은 높다.

확률적 모델링 관점에서 이미지 translation 문제를 분석하면, key challenge는 다른 도메인들의 이미지의 joint 분포를 학습하는 것이다.
비지도 학습세팅에서 (2개의 다른 도메인의 2개의 marginal 분포로부터의 이미지로 구성된 2개의 set) 과제는 이런 이미지를 사용하여 joint 분포를 추론하는 것이다.
coupling 이론에 따르면 joint 분포는 무한한 set이 존재하며 이는 보통 given marginal 분포에 다다를 수 있다.
따라서 marginal 분포로부터 joint 분포를 추론하는 것은 매우 적절하지 않은 문제이다.
이 ill-posed 문제를 다루기 위해, joint 분포의 구조에 대한 추가적 가정이 필요하다.

이에 shared-latent space 가정을 만든다. *이 가정은 다른 도메인에서의 상응하는 이미지 pair가 shared-latent space의 같은 latent representation에 연결될 수 있는 것이다.*
가정을 따르고 GAN과 VAE를 기반으로 UNIT framework를 제안한다.
각 이미지 도메인을 VAE-GAN을 이용하여 모델로 만든다.
적대적 훈련 objective는 weight-sharing constraint와 함께 교차하여 (shared-latent space를 유도한다) 2개 도메인에 상응하는 이미지를 만들고,
VAE는 각 도메인에서의 input 이미지와 변형된 이미지를 관계시킨다.
shared-latent space 가정은 Coupled GAN에서 join 분포 학습을 위해 사용되었고, 여기에선 UNIT 문제를 위해 확장되었다.
또한 shared-latent space constraint는 cycle-consistency constraint를 암시한다.

가정
=
X1과 X2는 2개의 이미지 도메인이다
supervised image-to-image translation에선 joint 분포로부터 나온 샘플 x1, x2가 주어진다.
UNIT에선 marginal 분포 Px1(x1)과 Px2(x2)로부터 나온 샘플이 주어진다.
joint 분포의 무한한 set이 given marginal 분포를 야기할 수 있기 때문에, 추가적인 가정이 필요하다.

가정은 shared-latent space이다.
E1과 E2는 각 이미지 x1과 x2를 shared-latent space에 연결하고, G1과 G2는 다시 latent code를 이미지로 연결한다.
이 가정은 weight sharing constraint를 사용하여 E1과 E2의 깊은 레이어의 weight connection으로 E1과 E2가 밀착하게 하고,
또한 G1과 G2의 초기 레이어의 weight도 connection하여 G1과 G2를 밀착시킨다.
G1는 재구성 이미지와 도메인변경 이미지를 생성하고, G2도 동일하다. 이후에 D1과 D2가 각 module에서 변형된 이미지를 판별한다.

이 가정의 실행을 위해 추가로 shared intermediate representation h를 가정하여 (z를 받은 h가 x1과 x2를 생성하는 형태의) 상응하는 이미지 pair 생성과정을 만든다.
중요부분인 G_H는 일반적인 고레벨 생성함수로 z를 h로 연결하고 G_L,1과 G_L,2는 저레벨 생성함수로 각각 h를 이미지 x1과 x2로 연결한다.
화창한 이미지와 비오는 이미지 같은 멀티도메인 translation의 경우에, z는 compact한 (정면의 차, 후면의 나무 같은 장면의) 고차원 representation이며, h는 G_H를 통해 z의 부분적 사실화로 고려되고 (나무가 다음 픽셀을 채운다) G_L,1과 G_L,2는 각 modality에서의 실제이미지 formation function이 된다. (나무는 화창할 때 밝은 녹색이지만 비가 올 때는 어두운 녹색이다.) 
h를 가정하는 것은 또한 E1과 E2를 논문의 수식에 의해 represent 하도록 해준다.
위 아이디어의 구현은 다음 섹션에서 본다.

Framework
=
VAE와 GAN을 기반으로 한다. 총 6개의 서브네트워크로 구성된다.
2개 도메인 이미지 encoder E1과 E2, 2개 도메인 이미지 generator G1과 G2, 2개의 판별자 D1과 D2이다.

### VAE
encoder-generator pair인 {E1, G1}은 X1 도메인을 위한 VAE로 구성되어 있다.
input 이미지 x1에 대하여, VAE1은 먼저 x1을 E1을 통해 latent space z의 code로 연결하고 G1을 통해 input 이미지를 재구성하기 위해 code의 random-perturbed version으로 decode한다. latent space Z의 구성은 독립적이고 unit variance의 가우시안으로 가정한다.
encoder의 output은 평균 vector E_,1(x1)이고 latent code z1의 분포는 identity matrix I와 함께 한다. (수식 참고)

reparameterization trick을 활용하는 것은 back-propagation을 사용하여 VAE를 훈련할 수 있도록 한다.
이 트릭은 non-differentiable 샘플링이 보조 random variable을 사용하여 마치 differentaible하게 reparameterized 할 수 있도록 한다.

### Weight-sharing
shared-latent space 가정을 기반으로 2개의 VAE를 관계시키기 위해 weight-sharing constraint를 유도한다.
E1, E2 후반 레이어의 weight 공유는 2개 도메인의 input 이미지들의 고차원 representation의 추출을 담당한다.
유사하게 G1, G2 초반 레이어의 weight 공유는 input 이미지들을 재구성하기 위한 고차원 representation의 decoding을 담당한다.

단, 이런 weight sharing이 혼자서 2개의 도메인의 상응하는 이미지가 같은 latent code를 가지는 것을 보장하지는 않는다.
상응하는 이미지 pair를 위해 추출한 latent code는 종종 다른 경우가 있다.
만약 같더라도, 같은 latent component는 때로 다른 도메인의 다른 semantic 의미를 가질 수 있다.
따라서 같은 latent code는 2개의 무관계한 이미지로 decode된다.
그러나 모델에서는 적대적 훈련으로 2개 도메인의 상응하는 이미지가 하나의 latent code로 연결하는 것을 보여준다.

latent 가정은 image-to-image translation을 가능하게 한다.
x1 이미지를 X2 도메인에 맞추어 변형이 가능하고, 이런 정보 프로세싱 stream을 image translation stream이라고 정의한다.
모델에는 X1→X2, X2→X1의 2개의 stream이 있으며, 이것들은 VAE로부터의 2개의 이미지 재구성 stream과 함께 훈련된다.
따라서 상응하는 이미지 pair를 동일한 latent code에 연결하고 이를 다시 상응하는 이미지 pair로 decode한다.

### GAN
모델은 2개의 GAN을 가진다. GAN1은 {D1, G1}이며 GAN2는 {D2, G2}이다.
G1은 2 종류의 이미지를 생성하는데 하나는 reconstruction stream으로부터의 이미지이고, 다른 하나는 translation stream으로부터의 이미지다.
전자는 지도적으로 학습되어야 하며, 적대적 훈련은 후자로부터의 이미지에만 적용하면 된다.
GAN2에도 유사한 과정을 적용한다.

### Cycle-consistency (CC)
shared-latent space 가정은 CC를 암시하므로, cycle-consistency constraint를 적용하여 ill-posed UNIT 문제를 정규화할 수 있다.
결과의 정보처리 stream은 cycle-reconstruction stream으로 부른다.

### Learning
image reconstruction, image translation, cycle-reconstruction stream을 위한 VAE1, VAE2, GAN1, GAN2의 학습 문제를 합쳐서 해결한다.

VAE 훈련은 variational upper bound를 최소화하는걸 목표로 한다.
hyper-parameter λ1과 λ2는 objective term의 weight를 조절하고, KL divergence term은 이전 분포로부터 오는 latent code 분포의 deviation에 페널티를 준다.
이 정규화는 latent space로부터의 sampling을 쉽게 한다.
pG1과 pG2를 라플라시안 분포를 이용하여 각각 모델로 한다.
따라서 (수식 뒷편의) negative log-likelihood term을 최소화하는 것은 이미지와 재구성 이미지의 절대적 거리를 최소화하는 것과 같아진다.
이전 분포는 zero mean Gaussian p(z) = N(z|0,I) 이다.

GAN 부분의 수식은 cGAN의 수식을 따른다.
이는 각각 변형된 이미지를 타겟도메인으로 재구성하는데 사용된다.
hyper-parameter λ0는 GAN objective function의 영향을 관리한다.

그리고 CC constraint는 VAE 같은 objective function을 이용하여 모델로 한다.
negative log-likelihood term은 2번 변형된 이미지를 input으로 바꾸고, KL term은 latent code가 cycle-reconstruction stream 안의 이전 분포로부터의 편차가 생기는 것에 페널티를 준다.

GAN처럼, 제안한 모델의 학습은 saddle point를 찾기 위한 최적화를 목표로 하는 mini-max problem을 해결하도록 한다.
모델은 2명의 player가 제로썸 게임을 하는 것처럼 보이는데, 전자는 encoder와 generator이고 후자는 discriminator이다.
전자는 후자를 속이면서 VAE loss와 CC loss를 최소화해야 한다.
전체 수식을 풀기위해 GAN objective에서 표현된 것과 유사하게 alternating gradient update scheme을 적용한다.
구체적으로는 E1, E2, G1, G2를 고정하고 *D1과 D2에 gradient ascent*를 적용한다.
그 후 후자를 고정하고 전자들에게 gradient descent를 적용한다.

### Translation
학습 이후, subnetwork의 부분집합을 모아서 2개의 image translation function을 얻는다.

실험
=

