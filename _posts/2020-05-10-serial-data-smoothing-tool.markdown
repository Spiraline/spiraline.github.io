---
layout: post
title:  "Serial Data Smoothing Tool"
date:   2020-05-10 10:31:14 +0900
category: tool
---
## Abstract
선형 회귀를 이용해서 serial한 data를 smoothing해주는 Tool
현재 값과 이전의 몇 개의 history를 바탕으로 선형 회귀를 통해 1차 함수의 회귀식을 만든 뒤
이를 바탕으로 현재 값을 smoothing한다.

## Usage [[Ref](https://github.com/Spiraline/serial-data-smoothing)]
---
```
git clone https://github.com/Spiraline/serial-data-smoothing.git 
cd serial-data-smoothing
g++ regression_filter.cpp -o smoothing
./smoothing <input file path> <output file path> <history_size>
```

window에서는 smoothing.exe로 생길텐데 그걸로 실행하면 된다.

### Option
- input file path : serial data를 담은 input file 경로. 한 줄 당 하나의 float data를 가지고 있어야 한다.
- output file path : smoothing된 file을 담을 파일 위치. 없으면 알아서 만들어준다.
- history_size : smoothing에 참고할 이전 값

## Result


## Description
ROS에서 들어오는 메시지를 callback 함수에서 smoothing하여 publish할 일이 생겨서 만들게 되었다. 원래는 Kalman Filter를 사용하려 했는데 단순 메시지다 보니 확률 변수로 만들기가 애매하기도 했고 결국 회귀로만 Filtering해도 같은 역할을 한다는 것을 확인해서 그냥 회귀를 통해 smoothing하게 만들었다. 들어오는 값에 glitch가 있을 때 이 값을 처리하기 위함이었는데 아예 누락을 시키기보다 이전 값들과 비교하여 smoothing을 시키는 것이 더 좋을 것이라고 판단하여 filtering이라고 표현을 안하고 smoothing이라고 이름을 짓게 되었다. 또 smoothing하다보면 겸사겸사 noise도 함께 잡을 수 있으니 그건 그것대로 좋은 일이었다. 그렇다고 ROS를 이용하는 모듈에만 사용할 수 있는 건 아니고 함수를 적절히 수정하면 serial한 data를 사용하는 어떤 모듈에나 사용할 수 있을 것이다. 실제로 ROS의 callback 함수에 넣기 위해 파일 형태가 아니라 메시지를 저장하도록 약간의 수정이 필요했으니..

history size가 크면 smoothing은 더 잘 되기는 하지만 반응이 느려지고 반대로 history size가 작으면 반응은 빠르지만 smoothing이 덜 된다. 이 때의 반응이라는 것은 값이 크게 변했을 때 그 값으로 적용이 되는 정도라고 생각하면 된다. 이렇게 말을 써놓고 보면 smoothing되는 정도의 반대말이라는 게 당연하다. 필터링할 때 기본 상식이라고 하는데 간단하게 만든 필터에서도 적용이 된다는 게 신기했다. 상황에 따라 조절할 일이 생길 것으로 보여서 ROS에서 모듈을 만들 때에는 parameter로 history size를 줄 수 있도록 하였다.

2차 함수로의 회귀 함수도 만들고자 했으나 만들고보니 1차 함수 회귀식만으로도 history size만 적절히 조절해도 smoothing이 잘 되는 듯 하여 구현하지는 않았다. (필터 함수 이름을 poly_1dim_filter로 만들었는데 2dim도 있었다)
2차 회귀식의 변수를 구하는 방법을 참고하여 구현하면 될듯하다.

### cf. Linear Regression

![Linear Regression Equation](/assets/2020-05-10-linear_eq.png)

모듈에서 사용한 식이다.