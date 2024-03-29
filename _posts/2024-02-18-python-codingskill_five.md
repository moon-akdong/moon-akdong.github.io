---
title: 파이썬 클래스
author: moon-akdong
date: 2024-02-18 14:10:00 +0800
categories: [python, python-coding-skill]
tags: [python-coding-skill]
render_with_liquid: false
---

# 내장타입을 여러 단계(계층)으로 내포시키기 보단 클래스를 합성하라. 

```python 
example = {
    {'red':(255,0,0)},
    {'blue':(0,0,255)},
    {'green':(0,255,0)}
}
```
예를 들어 이런 식으로 내장타입(딕셔너리)를 2계층 이상 사용한다면, 유지보수가 힘들어 진다. 

# 간단한 인터페이스의 경우 클래스 대신 함수를 받아라. 
파이썬의 내장 API 중 상당수는 함수를 전달하여 동작을 원하는 대로 바꿀 수 있게 해준다.  API가 실행되는 과정에서 전달하는 함수를 실행하는 경우 이런 경우를 훅(hook)이라고 부른다. 

예를들어 list의 정렬을 도와주는 sort메서드는 정렬 시 각 인덱스에 대응하는 비교 값을 결정하는 선택적인 key인자를 받을 수 있는데, key인자에 lambda함수라던지 , len 함수를 집어 넣어주는 걸 들 수 있다. 

```python
# 요소 길이에 따른 정렬
names = ["소크라테스","아르키메데스","플라톤","아르스토텔레스"]
names.sort(key=len)  # hook
print(names)
```
파이썬에서 함수는 일급시민 객체로 취급되기 때문에 함수를 훅(hook)으로 사용할 수 있다. 즉, 다른 일반적인 값과 마찬가지로 함수나 메서드를 다른 함수에 넘기거나 변수 등으로 참조할 수 있다는 뜻이다. 

collections.defaultdict는 딕셔너리 안에 없는 키에 접근할 셩우 
호출되는 인자가 없는 함구를 전달 할 수 있다. 단, 이 함수는 존재하지 않는 키에 해당하는 값이 가질 수 있는 객체를 반환해야 한다. 

```python 
from collections import defaultdict
def log_missing():
    print('키 추가됨') # log 
    return 0 # 존재하지 않는 키에 해당하는 값이 될 객체 반환 

current = {'green':12,'blue':3}

increments = [
    ('red',5),
    ('blue',17),
    ('orrange',9),
]

result = defaultdict(log_missing,current)
print('previous : ',dict(result))
for key,amount in increments:
    result[key] += amount

print('after',dict(result))
```
```
# return 
previous :  {'green': 12, 'blue': 3}   
키 추가됨   
키 추가됨   
after {'green': 12, 'blue': 20, 'red': 5, 'orrange': 9}   
```
log_missing과 같은 함수를 사용할 수 있으면 정해진 동작과 부수효과를 분리 할 수 있기 때문에 API를 더 쉽게 만들 수 있다. 

이때 함수 말고 작은 클래스를 훅으로 사용 할 수 있다. 
```python 
class CountMissing:
    def __init__(self):
        self.added = 0 
    def missing(self):
        self.added +=1 
        return 0
# CountMissing의 클래스만 본다면 클래스의 목적이 무엇인지 분명히 알기는 어렵다. 

counter = CountMissing()
result = defaultdict(counter.missing,current)
for key,amount in increments:
    result[key] +=amount

assert counter.added == 2 
```
클래스를 더 명화히 표현하기 위해 파이썬에서는 클래스에 \_\_call\_\_ 특별 메서드를 정의할 수 있다. 
- \_\_call\_\_을 사용하면 객체를 함수처럼 호출할 수 있다. 
- 그리고 \_\_call\_\_이 정의된 클래스의 인스턴스에 대해 callable내장 함수를 호출하면, 다른 일반 함수나 메서드와 마찬가지로 True가 반환된다. 
- 이런 방식으로 정의돼서 호출될 수 있는 모든 객체를 호출가능(callable) 객체라고 부른다. 

```python
class BetterCountMissing:
    def __init__(self) -> None:
        self.added = 0 

    def __call__(self):
        self.added +=1 
        return  0 
counter = BetterCountMissing()
assert counter() ==0
assert callable(counter)
```
```python
counter = BetterCountMissing()
result = defaultdict(counter,current) # __call__에 의존함

for key,amount in increments:
    result[key] += amount

assert counter.added == 2 
```

 \_\_call\_\_ 메서드는 함수가 인자로 쓰일 수 있는 부분에 클래스의 인스턴스를 사용할 수 있다는 사실을 나타낸다. 
 코드를 처음 읽는 사람도 이 클래스의 동작을 알아보기 위한 시작점이  \_\_call\_\_ 함수라는 사실을 쉽게 알 수 있으며, 이 클래스를 만든 목적인 상태를 젖아한느 클로저 역활이다라는 걸 알 수 있다. 

