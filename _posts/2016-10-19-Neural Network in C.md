---
layout: post
title: Neural Network in C
author: cineraria219
---

John Bullinaria의 C로 구현하는 Neural network 단계별 가이드

 이 문서는 C로 간단한 Neural network를 구현하기 위한 단계별 가이드를 담고 있습니다. 이는 주로 자신이 만든 거대한 시스템에 Neural Network를 포함하거나 (그것에 대해 들었던) 학생들을 대상으로 하고 있습니다. 확실히 사용할 때 고려할 Neural Network의 타입은 많습니다. 여기선 한 특히 일반적이고 유용한 타입에 집중 할 것입니다. 간단한 3 layer feed-forward back-propation network라고 불립니다.(multi layer perceptron)

 이 네트워크 타입은 우리가 입력 벡터들과 상호작용하는 출력 벡터들을 가지고 있을 때 유용할 것입니다. 그리고 그 시스템은 주어진 각 입력에 대하여 적절한 출력을 낼 것입니다. 물론, 우리가 이미 완전히 노이즈가 없는 입출력 벡터를 가지고 있다면 단순한 loop-up table(LUT)로 충분할 것입니다. 하지만 만일 우리가 시스템이 일반화를 하고싶을 때, 가령 우리가 모르는 입력이 들어왔을 때 적절한 출력이 나오길 원할 때, 입출력(ex 우리 트레이닝 셋) 사이에 어떻게 사상(Map)할 지 학습한 Neural Network는 새로운 입력에 대해도 매우 훌륭하게 결과를 출력한다.

 이걸 읽고 있는 사람은 C에 이미 익숙하다고 가정하고, Neural network에 대해 더 세세히 알고싶다면, 단순히 뉴스그룹 comp.ai.neural-nets 을 참조하고   Neural Networks FAQ에 질문하길 바란다. 시작하자...

 하나의 뉴런(가령 처리기)은 모든 입력 합 In과 활성화한 Out을 출력한다. 여기서 sigmoid 함수를 사용할 것이다.

$Out = { 1.0 \over (1.0 + e^{-In}) }$

 비록 다른 활성화 함수 (선형이나 tanh) 가 자주 사용되긴 하지만 sigmoid함수는 0 ~ 1 범위에 In의 무한한 범위를 (밀어)넣는 효과가 있다. 또한 이 미분값이 간단한 식으로 나타낼 수 있다는 편한 성질이 있다.

$Sigmoid\ Derivative = {Sigmoid * (1.0 - Sigmoid);}$

보통, 주어진 Neuron의 입력 In은 다른 많은 뉴런들의 출력을 활성화한 것들에 가중치가 더해진 것이다. 뉴런층(Layers of Neurons)을 통해 활성화에 대해 생각하기 편하다. 그래서, 만약 레이어 1층에 NumUnits1수의 뉴런들이 있다면, Layer 2층의 뉴런에 들어가는 활성화들의 합은

$\sum{Layer1Out[i] * Weight[i]}$

여기서 Weight[i]는 Layer1에서 i 뉴런과 Layer2의 뉴런간의 연결 강도/가중치이다. 각 뉴런은 bias와 또는 resting state를 갖고 있을 것이다 이는 input의 합들에 더한다. 이는 weight[0]로 부르는 것이 편하다. 우리는 결국 이렇게 쓸 수 있다.

```c
Layer2In = Weight[0];
for(i = 1; i <= NumUnits1 ; i++) {
Layer2In += Layer1Out[i] * Weight[i];
}
Layer2Out = 1.0 / (1.0 + exp(-Layer2In));
```

보통 layer 2 또한 많은 뉴런들을 가지고 있을 것이다. 따라서 layer 1의 뉴런 i와 layer 2의 뉴런 j 사이의 가중치를 Weight[i][j]로 적는 게 적절할 것이다. 결국 layer 2의 뉴런 j의 출력을 얻기위해 다음과 같이 한다.

```C
Layer2In[j] = Weight[0][j];
for(i = 1; i <= NumUnits1 ; i++) {
     Layer2In[j] += Layer1Out[i] * Weight[i][j];
}
Layer2Out[j] = 1.0 / (1.0 + exp(-Layer2In[j]));
```

 C에서 배열 색인은 1이 아닌 0부터 시작한다는 것을 기억하자. 그래서 우리는 변수를 이렇게 선언한다.

```c
double Layer1Out[NumUnits1+1] ;
double Layer2In[NumUnits2+1] ;
double Layer2Out[NumUnits2+1] ;
double Weight[NumUnits1+1][NumUnits2+1] ;
```
