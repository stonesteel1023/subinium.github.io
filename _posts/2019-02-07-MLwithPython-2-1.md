---
title : \[ML with Python\] 2장. 지도 학습 - 1,2절
category :
  - ML
tag :
  - python
  - deep-learning
  - AI
  - machine learning
  - 머신러닝
  - 지도 학습
  - 입문
  - subinium
  - 소스코드

sidebar:
  nav: sidebar-MLwithPython

use_math : true

header:
  teaser : /assets/images/category/ml.jpg
  overlay_image : /assets/images/category/ml.jpg
published : true
---

2.1 분류와 회귀 / 2.2 일반화, 과대적합, 과소적합

본 문서는 [파이썬 라이브러리를 활용한 머신러닝] 책을 기반으로 하고 있으며, subinium(본인)이 정리하고 추가한 내용입니다. 생략된 부분과 추가된 부분이 있으니 추가/수정하면 좋을 것 같은 부분은 댓글로 이야기해주시면 감사하겠습니다.

1장의 내용은 기초적인 설명이기에 생략하고, 2장부터 시작합니다.
2장의 경우, 내용이 너무 많은 절이 있어 주제 별로 3개로 나누어 포스팅합니다.

## 책에서 사용되는 기본 라이브러리

앞으로 사용할 기본적인 모듈만 가볍게 소개합니다.

``` python
from IPython.display import display
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
import mglearn # 이 책을 위해 만들어진 라이브러리
```

대부분 아시겠지만 필요한 라이브러리는 `pip install` 명령어를 이용하여 다운로드하시면 됩니다.
번역서의 `mglearn`의 모듈은 `pip install`로 다운받은 모듈과 살짝 차이가 있다고 합니다.
저는 쉽게 `pip install mglearn`으로 진행하겠습니다.

---

**지도 학습** 은 가장 널리 그리고 성공적으로 사용되는 머신러닝 방법 중 하나입니다.
입력과 출력 데이터가 있고, 주어진 입력으로부터 출력을 예측하고자 할 때 사용합니다.
보통 머신러닝 공부의 시작은 지도 학습이라해도 과언이 아닙니다.

## 2.1 분류와 회귀

지도 학습에는 대표적으로 **분류(classification)** 과 **회귀(regression)** 가 있습니다.

### 분류

분류는 미리 정의된, 가능성 있는 여러 **클래스 레이블(class label)** 중 하나를 예측하는 것입니다.
얼굴 인식, 유명한 숫자 판별(MNIST) 등이 이에 속합니다.
분류에는 두 개로만 나누는 **이진 분류(binary classification)** 과 셋 이상의 클래스로 분류하는 **다중 분류(multiclass classification)** 로 나뉩니다.

> 이진 분류에서 한 클래스를 **양성(positive)**, 다른 하나를 **음성(negative)** 클래스라고 합니다. 좋고 나쁘고가 아니기에 분야, 목적에 따라 부를 수 있습니다.

### 회귀

회귀는 연속적인 숫자, 또는 **부동소수점수** (실수)를 예측하는 것입니다. 주식 가격을 예측하여 수익을 내는 알고 트레이딩 등이 이에 속합니다. 분류와 회귀는 연속성이 있는가를 고민하면 쉽게 구분할 수 있습니다.

## 2.2 일반화, 과대적합, 과소적합

지도 학습의 메인 아이디어는 기존의 입력/훈련 데이터가 앞으로 나올 데이터에 대한 대표성을 가진다는 것입니다.
즉, 새로운 데이터(테스트 세트)가 훈련 데이터와 같은 패턴을 가진다면 이를 이용한 모델은 새로운 데이터를 정확하게 예측할 수 있다는 것입니다. 이를 훈련 세트에서 테스트 세트로 **일반화(generalization)** 되었다고 합니다.

일반화를 목표로 하지만 이는 쉬운 일이 아닙니다. 훈련된 세트에 대해서만 정확한 모델이 되는 경우가 많기 때문입니다.
기존 데이터에 대한 분류는 비교적 간단합니다. 분류 문제에서 예를 들면 다음과 같은 방법이 있습니다.

- 개발자를 도비 수준으로 일을 시킨다면 하드 코딩으로 기존 데이터에 대한 분류는 할 수 있습니다. 적어도 데이터 N개에 대한 분류는 O(N)으로 가능하게 만들 수 있습니다.
- 위와 비슷한 말이지만 N개의 서로 다른 데이터를 분류하기 위해서는 최대 N차원이면 충분합니다.

저런 방식으로 컴퓨터도 모델을 만들 수 있기 때문에 모델의 성능을 검증하기 위해서는 테스트 세트로 평가를 해야합니다.

그러나 직관적으로 봐도 간단한 모델이 새로운 데이터에 더 잘 일반화될 것이라고 예측할 수 있습니다.

여기서 보통 ['오컴의 면도날'](https://ko.wikipedia.org/wiki/%EC%98%A4%EC%BB%B4%EC%9D%98_%EB%A9%B4%EB%8F%84%EB%82%A0)을 가져와 설명합니다. 이는 경제성의 원리 또는 단순성의 원리라고도 불리는 것인데 다음과 같습니다.

> 1. "많은 것들을 필요없이 가정해서는 안된다" (Pluralitas non est ponenda sine neccesitate.)
> 2. "더 적은 수의 논리로 설명이 가능한 경우, 많은 수의 논리를 세우지 말라."(Frustra fit per plura quod potest fieri per pauciora.)

결국 우리는 가장 간단한 모델을 찾기 위해 노력해야합니다.

복잡한 모델이 되어 훈련 세트에만 적합하게 된 것이 **과대적합(overfitting)** 이라고 하고, 너무 간단한 모델이 되어 제 성능을 못하는 것을 **과소적합(underfitting)** 이라고 합니다.

즉 앞으로는 과대적합과 과소적합의 절충점, 머신 러닝 성능의 최적점을 찾는 것을 목표로 합니다.

### 2.2.1 모델 복잡도와 데이터셋 크기의 관계

입력 데이터의 다양성에 따라 모델의 복잡도는 변할 수 있습니다.
다양한 데이터 포인트가 많다면 과대적합 없이 더 복잡한 모델을 만들 수 있습니다.
또 다른 말로 표현하자면 중복된 데이터나 매우 비슷한 데이터는 크게 도움이 되지 않습니다.

모델의 조정보다 모델의 양이 더 좋을 수 있으니 다양한 데이터를 모으는 것이 중요합니다.
현재 머신러닝의 발전은 ImageNet과 같은 데이터 집합과 함께 발전한 것임을 알아야 합니다.