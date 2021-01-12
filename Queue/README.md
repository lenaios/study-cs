# Queue
queue는 데이터를 가장 뒤에 insert하고, 가장 앞의 데이터를 remove하는 리스트이다.  
따라서, 가장 먼저 enqueue한 데이터를 가장 먼저 dequeue 할 수 있다. FIFO!

## Queue Implementation
queue를 구현하기 위해 [LinkedList](https://github.com/lenaios/swift-algorithm/tree/main/Linked%20List)를 사용한다.
```swift
public struct Queue {

  fileprivate var list = LinkedList<Int>()

  public mutating func enqueue(_ element: Int) {
    list.append(element)
  }
  
  public mutating func dequeue() -> Int? {
    guard !list.isEmpty, let element = list.first else { return nil }

    list.remove(element)

    return element.value
  }
  
    public func peek() -> Int? {
      return list.first?.value
  }
  
    public var isEmpty: Bool {
      return list.isEmpty
  }
}
```
## Generic Queue Implementation
제네릭 타입으로 추상화된 queue를 구현할 수도 있다.
```swift
public struct Queue<T> {

  fileprivate var list = LinkedList<T>()

  public var isEmpty: Bool {
    return list.isEmpty
  }
  
  public mutating func enqueue(_ element: T) {
    list.append(element)
  }

  public mutating func dequeue() -> T? {
    guard !list.isEmpty, let element = list.first else { return nil }

    list.remove(element)

    return element.value
  }

  public func peek() -> T? {
    return list.first?.value
  }
}
```
