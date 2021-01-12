# Linked List
연결 리스트는 배열처럼 연속된 데이터의 집합이다.  
배열(array)과의 차이는 배열은 객체를 저장하기 위한 연속된 메모리를 할당받지만, 연결 리스트는 객체가 메모리 상에서 서로 떨어져 있으며 단지 링크(포인터)로 연결되어 있다.  

## Singly linked lists
```
+--------+    +--------+    +--------+    +--------+
|        |    |        |    |        |    |        |
| node 0 |--->| node 1 |--->| node 2 |--->| node 3 |
|        |    |        |    |        |    |        |
+--------+    +--------+    +--------+    +--------+
```
연결 리스트의 각 element를 node 라고 부르며, 다음 노드에 대한 포인터를 갖는 연결 리스트를 singly linked list,  
이전 노드에 대한 포인터도 갖고 있는 연결 리스트를 doubly linked list 라 한다.
## Doubly linked lists
```
         +--------+    +--------+    +--------+    +--------+
head --->|        |--->|        |--->|        |--->|        |---> nil
         | node 0 |    | node 1 |    | node 2 |    | node 3 |
 nil <---|        |<---|        |<---|        |<---|        |<--- tail
         +--------+    +--------+    +--------+    +--------+
```
연결 리스트의 시작이 어디인지 추적할 필요가 있으므로, head(포인터)로 시작 노드를 연결한다.  

## Why use a linked list
대표적으로는 queue가 필요할 때 linked list를 사용한다.  
배열의 경우 배열의 맨 앞 element를 제거하려면, element를 제거하고 뒤에 element들을 한 자리씩 이동시켜야 하므로 느리다.  
하지만 linked list라면 head의 포인터만 바꾸면 되므로 훨씬 빠르다.  
이처럼 객체를 연결하는 개념이 trees와 graphs에서도 사용된다.  

## Linked List Implementation in Swift
연결 리스트는 노드의 집합이므로 노드를 구현하기 위한 class를 작성한다.
```swift
public class Node {
  var value: String
  var next: Node?
  weak var previous: Node?
  
  init(value: String) {
    self.value = value
  }
}
```
doubly linked list로 구현하기 위해 previous 프로퍼티를 추가한다. 이 때, `weak`로 선언하는 것은 노드 간 강한 순환 참조를 피하기 위함이다.  
(강한 순환 참조는 노드를 가리키는 포인터를 삭제하더라도 노드가 메모리에 남아있는 상황을 야기할 수 있다.)  

이제 노드 클래스의 head와 tail를 추적하기 위해 `LinkedList` 클래스를 선언한다.  
```swift
public class LinkedList {
  fileprivate var head: Node?
  private var tail: Node?

  public var isEmpty: Bool {
    return head == nil
  }

  public var first: Node? {
    return head
  }

  public var last: Node? {
    return tail
  }
}
```
새로운 노드를 리스트에 추가하기 위해 `append(value:)` 함수를 구현한다.  
`tail` 노드가 있으면 `tail` 노드의 다음으로 연결하고 그렇지 않으면 `head`로 지정한다.  
```swift
public func append(value: String) {
  let newNode = Node(value: value)
  if let tailNode = tail {
    newNode.previous = tailNode
    tailNode.next = newNode
  } 
  else {
    head = newNode
  }
  tail = newNode
}
```
특정 인덱스의 노드를 탐색하기 위한 `nodeAt(index:)` 함수를 구현한다.  
```swift
public func nodeAt(index: Int) -> Node? {
  if index >= 0 {
    var node = head
    var i = index
    while node != nil {
      if i == 0 { return node }
      i -= 1
      node = node!.next
    }
  }
  return nil
}
```
특정 노드를 삭제하기 위한 `remove(node:)` 함수를 구현한다.  
삭제하고자 하는 노드의 이전 노드가 있으면 이전 노드를 `node.next`와 연결시키고, 마지막 노드를 삭제했다면 `node.next == nil`, prev 노드를 `tail`로 지정한다.  
```swift
public func remove(node: Node) -> String {
  let prev = node.previous
  let next = node.next

  if let prev = prev {
    prev.next = next
  } else { 
    head = next
  }
  next?.previous = prev

  if next == nil { 
    tail = prev
  }

  node.previous = nil 
  node.next = nil

  return node.value
}
```
## last
마지막 노드를 리턴해주는 last 프로퍼티를 연산 프로퍼티를 이용해서 다음과 같이 구현할 수도 있다.  
```swift
public var last: Node? {
  guard var node = head else {
    return nil
  }

  while let next = node.next {
    node = next
  }
  return node
}
```
## Generics
다음과 같이 제네릭을 사용해서 추상화된 연결 리스트를 구현할 수도 있다.
```swift
public class Node<T> {
  var value: T
  var next: Node<T>?
  weak var previous: Node<T>?

  init(value: T) {
    self.value = value
  }
}
```
## Big O
알고리즘이 최악인 경우의 running time을 나타내는 Big O. 연결 리스트의 Big O는 O(n)이다.  
Linked lists are flexible but many operations are O(n).
