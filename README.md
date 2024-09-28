## CGDPO-ML 알고리즘 소개

CGDPO-ML(CGDPO - Martingale Loss)는 policy update에만 집중하는 CGDPO 알고리즘을 policy evaluation까지 되게끔 확장하여 금융 분야의 option pricing에 응용하는 강화학습 알고리즘입니다. 물론 금융 분야 이외의 stochastic optimal control problem에도 적용될 수 있습니다.

본 알고리즘은 다음 연구자에 의해 개발되었습니다:
* 성균관대 수학과 허정규 교수
* 성균관대 수학과 김지훈 학생
* 북경대 HSBC 경영대학 최재혁 교

궁금한 점이 있으시면 허정규 교수에게 메일(jghuh@skku.edu)로 문의 부탁드립니다. 이 프로젝트는 현재 진행중으로 연구에 이용하신다면 다음을 인용 부탁드립니다.

Huh, J., Koo, H. K., & Lim, B. H. (2024, July). Continuous-time portfolio optimization via model-based reinforcement learning [Paper presentation]. 2024 Asia/Pacific Conference, Financial Management Association International, Seoul, South Korea.

## CGDPO 알고리즘의 특징

CGDPO 알고리즘은 REINFORCE 알고리즘의 변형으로 다음과 같은 특징이 있습니다.

1. 모델 기반 강화학습
2. 정책 기반 강화학습
3. 역전파를 이용한 정책 신경망의 직접 학습
4. 연속 시간 문제 해결을 위한 초기 상태 랜덤 샘플링
5. 오차가 높은 부분을 타개하기 위한 어댑티브 샘플링
   
REINFORCE 알고리즘과 1,2번은 동일하지만 3,4,5번에서 차별화됩니다. CGDPO에 관한 자세한 설명이 필요한 분은 다음 GitHub를 참고하세요:
[CGDPO GitHub](https://github.com/huhjeonggyu/cgdpo/tree/main)

## CGDPO-ML 알고리즘이란

CGDPO-ML 알고리즘은 policy update에만 집중하는 CGDPO 알고리즘을 policy evaluation까지 되게끔 확장한 알고리즘입니다. 즉, 

CGDPO-ML = CGDPO : 정책 개선 + ML : 가치 평가

CGDPO에서는 policy network만 등장하고 value network는 나타나지 않는 반면, CGDPO-ML에서는 두 유형의 신경망이 모두 등장합니다. 그럼에도 policy update에 value network가 직접적으로 관여하지 않기 때문에 여전히 정책 기반 강화학습으로 분류되어야 합니다. 

* Jia, Y., & Zhou, X. Y. (2022). Policy gradient and actor-critic learning in continuous time and space: Theory and algorithms. Journal of Machine Learning Research, 23(275), 1-50.

위 연구는 연속 확률 모형에서 policy evaluation에 대한 핵심적 이론을 제공합니다. 이 연구에선 

1. Martingale loss를 이용한 policy evaluation
2. Martingale orthogonality condition을 이용한 어댑티브 샘플링의 개선

## CGDPO와 PPO의 성능 비교

2024년 9월 기준으로 본 알고리즘은 dynamic choice & portfolio choice problem을 푸는 가장 강력한 도구입니다. 강화학습 알고리즘 중 널리 사용되는 PPO 알고리즘과 비교시 상대에러의 관한 RMSE에서 훨씬 우월합니다.

### 완전 시장
| | | 1min | 5min | 10min | 30min | 60min |
|--------|------|------|------|-------|-------|-------|
| Consumption | PPO | 1.61E+00 | 1.25E+02 | 4.49E+00 | 3.31E+00 | 1.52E+00 |
| | CGDPO | 2.41E+00 | 6.37E-01 | 3.02E-01 | 1.52E-01 | 1.22E-01 |
| Portfolio Weight | PPO | 1.34E-01 | 1.74E-01 | 1.77E-01 | 2.03E-01 | 2.22E-01 |
| | CGDPO | 3.04E-02 | 1.06E-02 | 6.54E-03 | 3.00E-02 | 1.47E-02 |

### 불완전 시장
| | | 1min | 5min | 10min | 30min | 60min |
|--------|------|-------|-------|--------|--------|--------|
| Myopic Demand | PPO | 4.94E+01 | 1.64E+02 | 3.61E+01 | 6.15E+01 | 1.32E+02
| | CGDPO | 2.19E-01 | 5.67E-02 | 2.10E-02 | 8.50E-03 | 1.21E-02
| Hedging Demand | PPO | 1.34E+01 | 1.58E+01 | 6.20E+00 | 6.25E+00 | 1.23E+01
| | CGDPO | 8.99E-01 | 8.42E-01 | 2.79E-01 | 2.54E-01 | 2.37E-01

위험 자산은 1개만 고려되었으며, 완전 시장은 Merton이 고려한 로그 수익률이 기하브라운운동을 따르는 시장을, 불완전 시장은 Kim & Omberg가 제시한 리스크 프리미엄이 OU process로 움직이는 시장을 의미합니다. 또한 완전 시장에서는 소비가 고려되나, 불완전 시장에서는 소비가 고려되지 않습니다. 이는 Merton과 Kim & Omberg의 원래의 설정을 따르는 것입니다. 완전 시장에서는 최적 소비량, 최적 포트폴리오 비율을 예측하는 신경망 2가지가 있으며, 불완전 시장에서는 최적 근시안적 수요(myopic demand), 최적 헷징 수요(hedging demand)를 예측하는 신경망 2가지가 있습니다. 근시안적 수요와 헷징 수요를 합치면 포트폴리오 비율이 됩니다. 잘 알려져 있는 것처럼, 불완전 시장에서의 dynamic choice problem은 마코위츠의 static choice problem에서 도출된 결과와 동일한 근시안적 수요에 헷징 수요를 더하는 것으로 설명됩니다. 자세한 설명이 필요한 분들은 complete 폴더와 incomplete 폴더를 들어가보시길 바랍니다.

PPO 알고리즘은 완전 시장에서도 수렴하지 않고 해답 주변을 순회하는 모습을 보여주며, 불완전 시장에서는 아예 해답과 동떨어진 답을 내놓습니다.
