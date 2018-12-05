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

