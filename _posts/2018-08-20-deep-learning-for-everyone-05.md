---
layout: post
title: "모두의 딥러닝 05일차"
categories:
  - Deep learning
tags:
  - deep learning
last_modified_at: 2018-08-20
---


<6장> 퍼셉트론
---
**퍼셉트론**
신경망을 이루는 기본 단위
입력 값과 활성화 함수를 사용해 출력 값을 다음으로 넘기는 가장 작은 신경망 단위

>y = ax + b (a는 기울기, b는 y절편)
>y = wx + b (w는 가중치, b는 바이어스)

가중합 : 입력값x와 가중치w의 곱을 모두 더한 다음 바이어스b를 더한 값
활성화 함수 : 가중합의 결과를 놓고  0과 1을 판단하는 함수(ex. 시그모이드 함수)

퍼셉트론은 XOR 문제를 해결하지 못함

![image](https://github.com/sg0415/sg0415.github.io/blob/master/_images/deep05.png?raw=true)

<7장> 다층 퍼셉트론
---

평면에서 직선으로 해결할 수 없다면???
평면을 왜곡시키자ㅇㅁㅇ

<i>입력층과 출력층 사이에 은닉층 만들기 !</i>

>퍼셉트론이 자신의 가주잋와 바이어스를 은닉층으로 보냄
>은닉층에서 모인 값이 시그모이드 함수를 이용해 최종 값으로 결과를 보냄
>노드(node) : 은닉층에 모이는 중간 정거장(n1, n2)

n1 = σ(x1w11 + x2w21 + b1)
n2 = σ(x1x12 + x2w22 + b2)

yout = σ(n1w31 + n2w32 + b3)

W(1) = [w11 w12
..............w21 w22]
W(2) = [w31
..............w32]
B(1) = [b1
.............b2]
B(2) [b3]

XOR 예시

| x1 | x2 | n1 | n2 | y_out | 원하는 값 |
|--------|--------|--------|--------|--------|--------|
|0|0|1|0|0|0|
|0|1|1|1|1|1|
|1|0|1|1|1|1|
|1|1|0|1|0|0|

n1 은 x1, x2에 대한 NAND 게이트
n2 는 x1, x2에 대한 OR 게이트
원하는 값은 n1, n2에 대한 AND 게이트

예제
```python
import numpy as np

w11 = np.arrau([-2, 2])
w12 = np.array([2, 2])
w2 = np.aray([1,1])
b1 = 3
b2 = -1
b3 = -1

#퍼셉트론 함수
def MLP(x, w, b):
    y = np.sum(w * x) + b
    if y <= 0:
        return 0
    else:
        return 1

#NAND 게이트
def NAND(x1, x2):
    return MLP(np.array([x1, x2]), w11, b1)

#OR 게이트
def OR(x1, x2):
    return MLP(np.array([x1, x2]), w12, b2)

#AND 게이트
def AND(x1, x2):
        return MLP(np.araay([x1, x2]), w2, b3)

#XOR 게이트
def XOR(x1, x2):
    return AND(NAND(x1, x2), OR(x1, x2))

if __name__ == '__main__':
    for x in [(0, 0), (1, 0), (0, 1), (1,1)]:
        y = XOR(x[0], x[1])
        print("입력 값 : " + str(x) + " 출력 값 : " + str(y))
        
```
        
실행결과
```
입력 값 : (0, 0) 출력 값 : 0
입력 값 : (1, 0) 출력 값 : 1
입력 값 : (0, 1) 출력 값 : 1
입력 값 : (1, 1) 출력 값 : 1
```

다층 퍼셉트론으로 XOR 문제를 해결함!

인공 <b>신경망</b>
은닉층을 여러 개 쌓아 올려 복잡한 문제를 해결하는 과정
뉴런이 복잡한 과정을 거쳐 사고를 낳는 사람의 신경망과 닮음

<8장> 오차 역전파
---

신경망 내부의 가중치는 오차 역전파 방법을 사용해 수정함
오차 역전파는 경사 하강법의 확장 개념

가중치와 바이어스를 어떻게 구할 수 있을까?

<오차 역전파><br>
>1. 임의의 초기 가중치(w1)를 준 뒤 결과(y_out)를 계산
>2. 계산 결과와 우리가 원하는 값 사이의 오차를 구함
>3. 경사 하강법을 이용해 바로 앞 가중치를 오차가 작아지는 방향으로 업데이트
>4. 1~3 과정을 더이상 오차가 줄어들지 않을 때까지 반복

오차가 작아지는 방향 = 미분값이 0에 가까워짐 = 기울기가 0이 되는 방향
>가중치에서 기울기를 빼도 값의 변화가 없을 때까지 가중치 수정작업 반복

입력된 실제 값과 다층 퍼셉트론의 계산 결과를 비교하여 가중치를 역전파 방식으로 수정

>1. 환경 변수 지정
>	입력 값과 타깃 결괏값이 포함된 데이터셋, 학습률 포함. 활성화 함수와 가중치 선언
>2. 신경망 실행
>	초깃값을 입력하여 활성화 함수와 가중치를 거쳐 결괏값을 나오게 함
>3. 결과를 실제 값과 비교
>	오차를 측정
>4. 역전파 실행
>	출력층과 은닉층의 가중치를 수정
>5. 결과 

오차 역전파의 계산법, XOR문제의 역전파 해결 스크립트는 부록에 있음
케라스, 텐서플로 라이브러리에 구현되어있음
몰라도 학습에 문제는 없지만 나중에 꼭 공부할것!!!

<9장> 신경망에서 딥러닝으로
---

1. 기울기 소실 문제와 활성화 함수
층이 늘어나면서 기울기가 중가네 0이 되어버리는 문제 발생
why? 시그모이드 함수의 특성떄문에
시그모이드 함수는 미분하면 최대치가 0.3임 계속 미분할수록 기울기가 사라져간다 ㅇㅁㅇ

    시그모이드, 하이퍼볼릭 탄젠트, 렐루, 소프트플러스....
    
    <b>렐루(ReLU)</b> : 시그모이드의 대안, 현재 가장 많이 사용됨
    - x가 0보다 작을 때는 모든 값을 0으로, 0보다 큰 값은 x를 사용
    - x가 0보다 클때 미분 값은 1. 기울기가 사라지지 않음!

2. 고급 경사 하강법
	경사 하강법의 단점 : 업데이트 할 때마다 전체 데이터를 미분 > 계산량 매우 많음
	
    <b>확률적 경사 하강법(SGD)</b>
     - 전체 데이터가 아니라 랜덤하게 추출한 일부 데이터를 사용 
     - 중간 결과의 진폭이 크고 불안정해 보이지만 속도가 빠름 	
 
 <b>모멘텀</b>
 	- 경사 하강법과 마찬가지로 매번 기울기를 구함
 	- 오차를 수정하기 전 바로 앞 수정 값과 방향을 참고하여 같은 방향으로 일정한 비율만 수정
 	- 이전 이동 값을 고려하여 일정 비율만큼만 다음 값을 결정(관성 효과)


여러 가지 고급 경사 하강법이 존재함
현재 가장 많이 쓰이는 것은 <b>아담(Adam)</b>

```python
keras.optimizers.Adam(Ir = 0.001, b eta_1 = 0.9, beta_2 = 0.999, epsion = 1e - 08, decay = 0.0)
```
다른 고급 경사 하강법과 파이썬 코드는 부록 C 참조
나중에 공부해서 추가할것
