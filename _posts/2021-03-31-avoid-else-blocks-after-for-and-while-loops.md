---
layout: post
title: for과 while 루프 뒤에는 else 블록을 쓰지 말자
tags: [python]
comments: true
---

파이썬의 루프에는 대부분의 다른 프로그래밍 언어에는 없는 추가적인 기능이 있다.
루프에서 반복되는 내부 블록 바로 다음에 else 블록을 둘 수 있는 기능이다.

```python
for i in range(3):
    print("Loop %d" % i)
else:
    print("Else block!")
```
```
>>>
Loop 0
Loop 1
Loop 2
Else block!
```

놀랍게도 else 블록은 루프가 종료되자마자 실행된다. 이걸 왜 else라고 부르는 걸까? and라고 해야 하지 않을까? if/else 문에서 else는 '이전 블록이 실행되지 않으면 이 블록이 실행된다'는 의미다. try/except 문에서 except도 마찬가지로 '이전 블록에서 실패하면 이 블록이 실행된다'고 정의할 수 있다.

비슷하게 try/except/else의 else도 '이전 블록이 실패하지 않으면 실행하라'는 뜻이므로 이 패턴을 따른다. try/finally도 '이전 블록을 실행하고 항상 마지막에 실행하라'는 의미이므로 이해하기 쉽다.

파이썬에서 else, except, finally 개념을 처음 접하는 프로그래머들은 for/else의 else 부분이 '루프가 완료되지 않는다면 이 블록을 실행한다'는 의미라고 짐작할 것이다. 실제로는 정확히 반대다. 루프에서 break 문을 사용해야 else 블록을 건너뛸 수 있다.

```python
for i in range(3):
    print("Loop %d" % i)
    if i == 1:
        break
else:
    print("Else block!")
```
```
>>>
Loop 0
Loop 1
```

다른 놀랄 만한 점은 빈 시퀀스를 처리하는 루프문에서도 else 블록이 즉시 실행된다는 것이다.

```python
for x in []:
    print("Never runs")
else:
    print("For Else block!")
```
```
>>>
For Else block!
```

else 블록은 while 루프가 처음부터 거짓인 경우에도 실행된다.

```python
while False:
    print("Never runs")
else:
    print("While Else block!")
```
```
>>>
While Else block!
```

이렇게 동작하는 이유는 루프 다음에 오는 else 블록은 루프로 뭔가를 검색할 때 유용하기 때문이다. 예를 들어 두 숫자가 서로소(coprime: 공약수가 1밖에 없는 둘 이상의 수)인지를 판별한다고 하자. 이제 가능한 모든 공약수를 구하고 숫자를 테스트해보겠다. 모든 옵션을 시도한 후에 루프가 끝난다. else 블록은 루프가 break를 만나지 않아서 숫자가 서로소일 때 실행된다.

```python
a = 4
b = 9
for i in range(2, min(a, b) + 1):
    print("Testing", i)
    if a % i == 0 and b % i == 0:
        print("Not coprime")
        break
else:
    print("Coprime")
```
```
>>>
Testing 2
Testing 3
Testing 4
Coprime
```

실제로 이런 방식으로 코드를 작성하면 안 된다. 대신에 이런 계산을 하는 헬퍼 함수를 작성하는 게 좋다. 이런 헬퍼 함수는 두 가지 일반적인 스타일로 작성한다.

첫 번째 방법은 찾으려는 조건을 찾았을 때 바로 반환하는 것이다. 루프가 실패로 끝나면 기본 결과(True)를 반환한다.

```python
def coprime(a, b):
    for i in range(2, min(a, b) + 1):
        if a % i == 0 and b % i == 0:
            return False
    return True
```

두 번째 방법은 루프에서 찾으려는 대상을 찾았는지 알려주는 결과 변수를 사용하는 것이다. 뭔가를 찾았으면 즉시 break로 루프를 중단한다.

```python
def coprime2(a, b):
    is_coprime = True
    for i in range(2, min(a, b) + 1):
        if a % i == 0 and b % i == 0:
            is_coprime = False
            break
    return is_coprime
```

이 두 가지 방법을 적용하면 낯선 코드를 접하는 개발자들이 코드를 훨씬 쉽게 이해할 수 있다. else 블록을 사용한 표현의 장점이 나중에 여러분 자신을 비롯해 코드를 이해하려는 사람들이 받을 부담감보다는 크지 않다. 루프처럼 간단한 구조는 파이썬에서 따로 설명할 필요가 없어야 한다. 그러므로 루프 다음에 오는 else 블록은 절대로 사용하지 말아야 한다.

### 출처

브렛 슬라킨, 『파이썬 코딩의 기술: 똑똑하게 코딩하는 법』, 김형철 옮김, 길벗(2016), p46-49
