---
layout: post
title: "스택, 큐, 힙"
tags: [data structure and algorithm]
comments: true
---

# 스택 (Stack)

여러 물건이 위아래로 포개져 있는 형태를 활용한 데이터구조이다. 예로 겹겹이 쌓아둔 책이나 접시 등을 연상할 수 있다. 아래 그림에서 데이터를 하나씩 쌓고 하나씩 치우는 과정에서 마지막으로 올린 데이터가 맨 처음으로 나가게 되듯이, 이 데이터타입은 가장 늦게 들어온 값이 가장 빠르게 나간다는 특징을 갖고 있다.

## 주요 연산

- push: 스택에 데이터를 집어 넣음.
- pop: 스택의 최상단에 있는 데이터를 제거하고 그 데이터를 반환함.
- peek: 스택의 최상단에 있는 데이터를 반환하되 제거하지는 않음.
- size: 스택에 들어있는 데이터의 개수를 반환함.
- isEmpty: 스택이 비어있는지 판단함.
- isFull: 스택이 꽉 차있는지 판단함.

![/assets/images/stack_queue_heap/stack-data-structure.gif](/assets/images/stack_queue_heap/stack-data-structure.gif)

## 스택의 응용

### 수식의 후위 표기법

수식을 표현하는 방법에는 크게 세 가지로 **전위(prefix) 표기법**, **중위(infix) 표기법**, **후위(postfix) 표기법**이 있다. 수식을 표현할 때는 연산자와 피연산자를 사용하는데, 우리는 다음과 같이 연산자를 피연산자 사이에 둠으로써 수식을 표현한다.

- 3 + 5
- 10 * 3

이러한 표기 방법은 연산자가 중간에 위치해있기 때문에 중위 표기법이라고 한다. 이 표기법은 사람에게 편리한 방법이지만 컴퓨터에게는 불편한 방식이다. 컴퓨터는 수식을 처리함에 있어서 수식을 [피연산자, 피연산자, 연산자] 순으로 나열해 연산자가 마지막에 오도록 하는 표기법을 사용하는 것이 효율적이다. 그리고 결과를 계산하는 과정에서 **스택**을 사용한다.

예를 들어 $(3 + 4) * (5 * 6 + 9 - 2)$ 라는 수식이 있을 때 결과를 컴퓨터 내부에서 계산하는 과정은 다음과 같다.

![/assets/images/stack_queue_heap/postfix-degerlendirme.gif](/assets/images/stack_queue_heap/postfix-degerlendirme.gif)

# 큐 (Queue)

큐는 대기 줄에 비유할 수 있다. 우리가 놀이공원에 입장하기 위해 줄을 설 때, 먼저 온 사람이 먼저 들어가게 된다. 나중에 온 사람일수록 나중에 들어가기 때문에 흔히 '공정한' 자료구조라고 비유된다. 이러한 구조를 선입선출 (First In First Out) 구조라고 한다.

![/assets/images/stack_queue_heap/craft-cms-yii2-async-queue.jpeg](/assets/images/stack_queue_heap/craft-cms-yii2-async-queue.jpeg)

## 주요 연산

- enqueue: 큐에 데이터를 추가함.
- dequeue: 큐의 맨 앞에 있는 데이터를 제거하고 그 데이터를 반환함.
- front: 큐의 맨 앞에 있는 데이터를 반환하되 제거하지는 않음.
- size: 큐에 들어있는 데이터의 개수를 반환함.
- isEmpty: 큐가 비어있는지 판단함.
- isFull: 큐가 꽉 차있는지 판단함.

![/assets/images/stack_queue_heap/queue-operations.gif](/assets/images/stack_queue_heap/queue-operations.gif)

## 큐의 변형

### 원형 큐 (Circular Queue)

위와 같은 전통적인 **선형 큐** (Linear Queue) 의 경우 삭제 연산을 할 때마다 `front`의 위치가 오른쪽으로 이동하게 되면서 왼쪽의 공간을 활용할 수 없게 된다는 문제가 발생한다. 원형 큐에서는 이러한 문제를 해결하고자 순환 구조를 사용했으며, 이에 따라 공간의 효율성을 높일 수 있다.

![/assets/images/stack_queue_heap/circular-queue-implementation.gif](/assets/images/stack_queue_heap/circular-queue-implementation.gif)

### 우선순위 큐 (Priority Queue)

우선순위 큐는 기본적으로 선입선출 구조를 따르지만 데이터 각각에 대한 우선순위에 따라 처리 순서를 달리 할 수 있는 데이터구조이다. 만약 더 늦게 입력된 데이터일지라도 앞의 데이터보다 우선순위가 높다면 먼저 출력될 수 있다. 이는 비행기 탑승 시 VIP 승객에 대한 우선탑승으로 비유할 수 있다.

![/assets/images/stack_queue_heap/priority-queue1-5.gif](/assets/images/stack_queue_heap/priority-queue1-5.gif)

# 힙 (Heap)

힙은 특정한 규칙을 가지는 트리이다. **트리**란 구조는 루트 노드가 하나 존재하고 그 하위에 여러 개의 노드로 줄기뻗는 형태의 데이터구조를 말한다.

![/assets/images/stack_queue_heap/tree.png](/assets/images/stack_queue_heap/tree.png)

트리 구조

일반적으로 데이터 처리를 위해 트리를 사용하는 경우에는 **완전이진트리**라는 제한된 형태의 트리 구조를 많이 사용한다.

### 완전이진트리 (Complete Binary Tree)

완전이진트리란 루트 노드가 하나 있고 그 아래로 자식 노드가 두 개씩 생성되는 데이터구조이며, 마지막 레벨을 제외한 모든 레벨의 노드는 완전히 채워져 있다. 그리고 마지막 레벨의 노드는 왼쪽에서 오른쪽 순으로 생성된다.

![/assets/images/stack_queue_heap/1_7PBp11cUQ4Sy5u_pz8hKCQ.gif](/assets/images/stack_queue_heap/1_7PBp11cUQ4Sy5u_pz8hKCQ.gif)

다시 본론으로 돌아와서, 힙이란 특정한 규칙을 가지는 트리라고 소개했는데, 대표적으로 **최소힙**과 **최대힙**이 있다.

### 최소힙 (Min-Heap)

최소힙은 루트 노드가 가장 값이 작은 노드가 되고 그 아래로 내려갈수록 값이 점점 커지는 형태를 띄도록 정렬된 완전이진트리 데이터구조이다.

![/assets/images/stack_queue_heap/min_heap_example.jpeg](/assets/images/stack_queue_heap/min_heap_example.jpeg)

### 최대힙 (Max-Heap)

최대힙은 부모노드가 가장 값이 큰 노드가 되고 그 아래로 내려갈수록 값이 점점 작아지는 형태를 띄도록 정렬된 완전이진트리 데이터구조이다.

![/assets/images/stack_queue_heap/max_heap_example.jpeg](/assets/images/stack_queue_heap/max_heap_example.jpeg)

최대힙의 생성 과정 예시:

![/assets/images/stack_queue_heap/max_heap_animation.gif](/assets/images/stack_queue_heap/max_heap_animation.gif)

최대힙의 삭제 연산 예시:

![/assets/images/stack_queue_heap/max_heap_deletion_animation.gif](/assets/images/stack_queue_heap/max_heap_deletion_animation.gif)