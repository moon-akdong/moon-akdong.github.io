---
layout: post
title: "Linked List"
categories: coding_test
tags: Python, 자료구조
---

배열과 함께 가장 기본이 되는 대표적인 선형 자료구조 중 하나, 다양한 추상자료형 구현의 기반
연결 구조를 통해 물리 메모리를 연속적으로 사용하지 않아도 되기 때문에 관리도 쉽다.

데이터를 구조체로 묶어서 포인터로 연결하는 개념은 여러 가지 방법으로 다양하게 활용이 가능하다.

배열은 조회에서 O(1)로 장점이 있었고, 삽입/삭제는 O(n) 인 단점이 있었지만,
연결리스트는 조회에서 O(n), 삽입/삭제는 O(1)이다

삽입/삭제에서 O(1)인 이유는 재정렬을 할 필요가 없기 때문이다. 단순히 추가 후 새로운 노드의 연결 부분 주소만 원래 처음 있었던 노드로 지정해주면 된다. 조회는 원하는 곳 까지 가기 위해서는 index 접근이 아니라, 하나씩 확인하고 넘어가야 하기 때문이다.

배열은 삽입/삭제에서 요소들의 인덱스를 재정렬해야 하기 때문에 O(n) 이다.

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next # 다음 연결 부분
```

# LeetCode 234

## 리스트로 변환하여 풀이

```python
class Solution:
    def isPalindrome(self, head: Optional[ListNode]) -> bool:
        # 리스트 변환
        q = collections.deque()
        if not head:
            return True

        node = head
        while node is not None:
            q.append(node.val)
            node = node.next

        # deque를 이용한 최적화
        while len(q) > 1:
            if q.popleft() != q.pop():
                return False
        return True
```

## 러너 풀이

while 의 조건을 fast.val and slow.val: 로 했더니, NoneType에러 발생

- 짝수 : fast 에서 none
- 홀수 : fast.next에서 none

slow 와 rev 비교하는 while 문에서도 slow.val and rev.val 로 했더니 오류 발생

- 동시 할당

```
# 1단계: rev 쪽
rev, rev.next = slow, rev  # rev가 slow로 바뀌는 순간 rev.next도 slow.next를 건드림
# 2단계: slow 쪽
slow = slow.next  # 이미 1단계에서 slow.next가 망가졌을 수 있음
```

```
rev, rev.next = slow, rev
# 오른쪽: slow = [1]노드, rev = None
# 동시 할당:
#   rev = [1]노드
#   rev.next = None   ← 이 시점 rev는 이미 [1]노드
#                        즉 [1].next = None

slow = slow.next
# slow는 아직 [1]노드
# [1].next는 방금 None으로 바꿨음
# 그래서 slow = None 💥
```

```
rev, rev.next, slow = slow, rev, slow.next
# 오른쪽 먼저 평가: slow, rev, slow.next 값을 전부 읽어둠
# 그 다음 동시에 왼쪽에 할당
```

# Leetcode 21

```python
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        if (not list1) or (list2 and list1.val > list2.val):
            list1, list2 = list2, list1

        if list1:
            list1.next = self.mergeTwoLists(list1.next,list2)
        return list1
```

# Leetcode 206

## 반복 풀이

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node,prev = head, None

        while node:
            prev, node, prev.next = node, node.next,prev

        return prev
```

## 재귀풀이

```python
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        def reversed(node,prev=None):
            if not node:
                return prev
            next, node.next = node, prev

            return reversed(next, node)
        return reversed(head)
```

# Leetcode 2

## 전가산기 풀이

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        result = 0
        carry = 0
        root = head = ListNode(0)
        while (l1 is not None) or (l2 is not None) or carry:
            sum_num = 0
            if l1:
                sum_num += l1.val
                l1 = l1.next
            if l2:
                sum_num += l2.val
                l2 = l2.next

            carry,val = divmod(sum_num+carry,10)
            head.next = ListNode(val)
            head = head.next
        return root.next
```

```python
class Solution:
    def reverseList(self,head):
            node, prev = head, None
            while node:
                next, node.next = node.next, prev
                prev, node = node, next
            return prev

    def toList(self,node):
        ls=[]
        while node:
            ls.append(node.val)
            node = node.next
        return ls

    def toReversedLinkedList(self,result):
        prev = None
        for r in result:
            node = ListNode(int(r))
            node.next = prev
            prev=node
        return node

    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:

        a = self.toList(self.reverseList(l1))
        b = self.toList(self.reverseList(l2))

        resultStr = int(''.join(str(e) for e in a)) +\
                    int(''.join(str(e) for e in b))

        return self.toReversedLinkedList(str(resultStr))
```

# LeetCode 24

## 값만 교환

```python
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 값만 교환
        cur = head
        while cur and cur.next:
            cur.val, cur.next.val = cur.next.val, cur.val

            cur = cur.next.next
        return head
```

## 반복 풀이

```python
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 스왑을 하고,
        # 앞의 연결 리스트와 뒤의 연결 리스트를 연결해줘야 한다.

        root = prev = ListNode(None)
        prev.next = head # None - > 1 -> 2 -> ..

        while head and head.next:
            # 스왑 후 뒤의 연결 리스트 연결을 위해
            node = head.next # 2->3->4
            head.next = node.next # head = 1->3->4
            # 스왑
            node.next = head # 2->1->3->4

            prev.next = node # None 2 1 3 4

            # 다음 스왑을 위해
            head = head.next
            prev = prev.next.next
        return root.next
```

## 재귀

```python
class Solution:
    def swapPairs(self, head: Optional[ListNode]) -> Optional[ListNode]:

        if head and head.next:
            node = head.next
            # 뒤의 연결 리스트를 연결하기 위해
            head.next = self.swapPairs(node.next) # None
            # 스왑
            node.next = head # node = 4->3->None
            return node
        return head
```

# Leetcode 328

```python
class Solution:
    def oddEvenList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        odd = odd1 = ListNode(None)
        even = even1 = ListNode(None)

        while head and head.next:
            odd1.next = head
            even1.next = head.next
            # n 1 3
            # n 2 4
            head = head.next.next
            # 3 4 5
            odd1 = odd1.next
            even1 = even1.next

        if head:
            odd1.next = head
            odd1 = odd1.next

        odd1.next = even.next
        return odd.next
```

문제 발생 지점: 여기서 even.next는 처음 루프에서 설정된 2예요. 그런데 2.next는 루프 2회차에서 4로 설정됐고, 4.next는 그 이후 한 번도 안 바뀌었어요. 4 다음에 5가 와야 하는데, 5는 이미 odd 체인(3→5)에 들어가 있는 상태에서 5.next = 2로 다시 연결돼버려요.
결과적으로 4.next가 어디를 가리키는지 보면, even1.next = head.next가 루프 2회차에서 2.next = 4까지만 설정되고 끝났어요. 4의 .next는 그대로 원본 값인 5를 가리키고 있어요 (한 번도 안 건드림).
그래서 최종 연결은 1→3→5→2→4→5... 가 되고, 5가 두 번 등장하면서 사이클이 생겨요. 이게 무한루프(타임아웃)의 원인이에요.
핵심: even1.next는 매 회차 갱신되지만, even 체인의 마지막 노드(4)의 .next를 명시적으로 끊지 않아서 원본 연결(4→5)이 그대로 남아있고, 이게 odd 체인에 이미 들어간 5와 충돌하면서 사이클을 만들어요.

```python
class Solution:
    def oddEvenList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if not head or not head.next:

            return head
        odd = head
        # odd 와 연결을 위해 even는 head가 필요
        even = even_head = head.next

        while even and even.next:
            odd.next = even.next
            odd = odd.next
            even.next = odd.next
            even = even.next

        odd.next = even_head

        return head
```
