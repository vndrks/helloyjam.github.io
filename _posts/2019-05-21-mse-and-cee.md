---
title: "[손실함수] 평균제곱오차(Mean Squared Error)와 교차엔트로피 오차(Cross Entropy Error)"
classes: wide
categories:
  - machine learning
date: 2019-05-21 19:00:00 -0600
---


  
손실함수는 정답에 대한 오류를 나타내는 것으로  
정답에 가까울수록 작은 값이 나오고, 오답에 가까울 수록 큰 값이 나온다.  

### 평균제곱오차  

yi는 신경망의 출력(신경망이 추정한 값)  
ti는 정답 레이블  
i는 데이터의 차원수  

수식은 아래와 같다.  
<img width="503" alt="스크린샷 2019-05-22 오전 12 11 48" src="https://user-images.githubusercontent.com/10937193/58108494-e7bc7580-7c26-11e9-90a1-b988522a0b64.png">

이 수식을 파이썬으로 표현하면 다음과 같다.  
```python
import numpy as np

def mean_squared_error(y, t):
    return ((y-t)**2).mean(axis=None)

# with axis=0 the average is performed along the row, for each column, returning an array
# with axis=1 the average is performed along the column, for each row, returning an array
# with axis=None the average is performed element-wise along the array, returning a single value
```
  
y와 t배열은 첫번째 인덱스부터 순서대로 숫자 0, 1, 2, ... 일때의 값이고  
y는 소프트맥스 함수의 출력이므로 데이터가 0일 확률은 0.1, 1일 확률은 0.05로 해석할 수 있다.  
t는 정답 레이블이므로 정답 위치의 원소는 1로, 그 외에는 0으로 표기해서 아래에서는 숫자 2를 의미한다.
```python
# 2일 확률이 가장 높다고 추정함 (0.6)
y = [0.1, 0.05, 0.6, 0.0, 0.05, 0.1, 0.0, 0.1, 0.0, 0.0]
# 정답은 2
t = [0, 0, 1, 0, 0, 0, 0, 0, 0, 0]

mse_value1 = mean_squared_error(np.array(y), np.array(t))
print(mse_value1)
# 0.0195

# 7일 확률이 가장 높다고 추정함 (0.6)
y = [0.1, 0.05, 0.1, 0.0, 0.05, 0.1, 0.0, 0.6, 0.0, 0.0]

mse_value2 = mean_squared_error(np.array(y), np.array(t))
print(mse_value2)
# 0.1195
```

첫번째 예는 정답이 2이고 신경망의 출력도 2에서 가장 높은 경우입니다.
두번째 예는 정답이 2이고 신경망의 출력은 7에서 가장 높은 경우입니다.

MSE값이 첫번째 예의 손실함수 출력이 작고 정답레이블과의 오차도 작은 것을 알 수 있다.  
평균제곱오차를 기준으로는 첫번째 추정 결과가 정답에 더 가까울 것으로 판단이 가능하다.  

### 교차 엔트로피 오차

수식은 아래와 같습니다.

<img width="547" alt="스크린샷 2019-05-22 오전 12 11 53" src="https://user-images.githubusercontent.com/10937193/58108191-577e3080-7c26-11e9-8b54-097fec3e5f0e.png">
수식을 파이썬을 표현하면 아래와 같다.  
여기서 y값이 0이 나오면 log의 결과 마이너스 무한대가 발생하지 않도록 아주 작은 값을 더했다.
```python
def cross_entropy_error(y, t):
    # y의 차원수가 1이면 1차원이면 2차원으로 변환
    if 1 == y.ndim: 
        y = y.reshape(1, y.size)
        t = t.reshape(1, t.size)
    delta = 1e-7
    return -np.sum(np.log(y+delta)*t)/y.shape[0]

# 2일 확률이 가장 높다고 추정함 (0.6)
y = [0.1, 0.05, 0.6, 0.0, 0.05, 0.1, 0.0, 0.1, 0.0, 0.0]
# 정답은 2
t = [0, 0, 1, 0, 0, 0, 0, 0, 0, 0]

cee_value1 = cross_entropy_error(np.array(y), np.array(t))
# 0.51082545709933802

# 7일 확률이 가장 높다고 추정함 (0.6)
y = [0.1, 0.05, 0.1, 0.0, 0.05, 0.1, 0.0, 0.6, 0.0, 0.0]

cee_value2 = cross_entropy_error(np.array(y), np.array(t))
# 2.3025840929945458
```

첫번째 예는 정답일 때의 출력이 0.6인 경우로 교차엔트로피 오차는 0.51입니다.
두번째 예는 정답일 때의 출력이 0.1인 경우로 이 때의 교차엔트로피 오차는 무려 2.3입니다.

오차값이 더 작은 첫번째 추정이 정답이 가능성이 높다 판단한 것으로 앞서  MSE 판단과 일치합니다.

