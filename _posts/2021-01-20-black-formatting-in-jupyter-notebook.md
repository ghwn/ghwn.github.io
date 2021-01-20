---
layout: post
title: "주피터 노트북에서 black 포맷팅 적용하기"
tags: [python, jupyter, black, formatting]
comments: true
---

평소에 [PEP8](https://www.python.org/dev/peps/pep-0008/){:target="_blank"} 파이썬 코드 스타일 가이드를 최대한 따르려고 노력하는 편이라 [autopep8](https://github.com/hhatto/autopep8){:target="_blank"}이나 [yapf](https://github.com/google/yapf){:target="_blank"}, 최근에는 [black](https://github.com/psf/black){:target="_blank"} 같은 코드 스타일 자동 포맷터를 사용하고 있는데요.

주피터 노트북에서는 이를 활용하지 못해 아쉬웠습니다. 그런데 찾아보니까 주피터에서도 black을 사용할 수 있어서 방법을 공유해보려고 합니다. 방법은 매우 간단합니다.


## 설치

```
$ pip install blackcellmagic
```


## 사용방법

1. `%load_ext blackcellmagic`을 실행해 모듈을 불러옵니다.
2. `%%black` 명령어를 통해 포맷팅을 적용합니다.

![how-to-use-blackcellmagic](/assets/images/how-to-use-blackcellmagic.gif)

```python
# black 기본 라인 길이 88로 포맷팅을 적용하려면
%%black

# 특정 라인 길이로 포맷팅을 적용하려면
%%black -l 79
%%black --line_length 79
```


## 출처

- [https://github.com/csurfer/blackcellmagic](https://github.com/csurfer/blackcellmagic){:target="_blank"}
