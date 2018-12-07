StarGAN (Unified Generative Adversarial Networks for Multi-Domain Image-to-Image Translation)
=

요약
=
기존 image-to-image translation은 2개를 넘는 도메인을 다룰때 제한이 있었고
그 이유는 다른 모델들은 모든 이미지 도메인 pair에 대해 독립적으로 여러개가 만들어져야 하기 때문이다.
이를 해결하기 위해, StarGAN을 제안하고 오로지 하나의 모델만으로 multiple 도메인에 대한 translation을 할 수 있도록 한다.
하나로 통일된 모델구조는 하나의 네트워크 안에서 여러 도메인의 multiple dataset을 동시에 훈련할 수 있도록 한다.
이는 변형된 이미지 품질이 좋으면서도 input 이미지를 어떤 target 도메인에라도 유연하게 바꿀 수 있는 능력을 가지도록 해준다.

소개
=
2개의 다른 도메인의 훈련데이터로부터 모델은 이미지를 한 도메인에서 다른 도메인으로 변형시킨다.
attribute란 term은 머리색, 성별 같은 이미지에서 의미있는 feature이고, attribute value는 검정머리, 갈색머리 같이 특성의 특정한 value이다.
또한 domain은 같은 attribute value를 공유하는 이미지셋으로 본다.


