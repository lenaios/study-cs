# Heap
The heap is a tree, and all of the nodes in the tree have 0, 1 or 2 children.  
Heap은 0, 1, 또는 2개의 자식이 있는 노드들로 구성된 트리이다.

Heap의 elements는 우선 순위에 따라 정렬되어 있는데, element의 value에 따라 다음 두 가지의 heap이 존재한다.
- maxheaps: Elements with a higher value represent higher priority.
- minheaps: Elements with a lower value represent higher priority.

## Removing the highest priority element
heap의 root node는 우선 순위가 가장 높은 value를 가지므로, 우선 순위 큐(priority queue)에 유용하다.  
하지만, 단순히 root node를 제거하는 것은 자식 노드를 잃어버리거나 두 개의 heap을 만들 수도 있다.

그래서 root node와 마지막 node를 swap 한 뒤, 마지막 node를 제거한다.  
그 후, root node를 자식 노드와 우선 순위를 비교해서 swap 한다.  
이로 인해 자식 노드들도 우선 순위에 따라 swap 한다(shift down).

## Adding a new element
새로운 element를 추가할 때는 불완전한 단계(level)의 가장 왼쪽에 추가하고, 부모의 값과 우선 순위를 비교해서 swap 한다(shift up).

자세히 보면, heap은 element들을 저장하기 위한 Node data type을 갖지 않고 array로 이루어져 있다.  
배열의 index로 node의 level을 알 수 있다.
>Every node in the heap is assigned an index. We start by assigning 0 to the root node, and then we iterate down through the levels, counting each node from left to right:  

## Implementing a Swift Heap
```swift
struct Heap<Element> {
  var elements : [Element]
  let priorityFunction : (Element, Element) -> Bool

  init(elements: [Element] = [], priorityFunction: @escaping (Element, Element) -> Bool) {
    self.elements = elements
    self.priorityFunction = priorityFunction
    buildHeap()
  }

  mutating func buildHeap() {
    for index in (0 ..< count / 2).reversed() {
      siftDown(elementAtIndex: index)
    }
  }
  var isEmpty : Bool {
    return elements.isEmpty
  }

  var count : Int {
    return elements.count
  }
  
  func peek() -> Element? {
    return elements.first
  }
  
  func isRoot(_ index: Int) -> Bool {
    return (index == 0)
  }

  func leftChildIndex(of index: Int) -> Int {
    return (2 * index) + 1
  }

  func rightChildIndex(of index: Int) -> Int {
    return (2 * index) + 2
  }

  func parentIndex(of index: Int) -> Int {
    return (index - 1) / 2
  }
  
  func isHigherPriority(at firstIndex: Int, than secondIndex: Int) -> Bool {
    return priorityFunction(elements[firstIndex], elements[secondIndex])
  }
  
  func highestPriorityIndex(of parentIndex: Int, and childIndex: Int) -> Int {
    guard childIndex < count && isHigherPriority(at: childIndex, than: parentIndex)
      else { return parentIndex }
    return childIndex
  }

  func highestPriorityIndex(for parent: Int) -> Int {
    return highestPriorityIndex(of: highestPriorityIndex(of: parent, and: leftChildIndex(of: parent)), and: rightChildIndex(of: parent))
  }
  
  mutating func swapElement(at firstIndex: Int, with secondIndex: Int) {
    guard firstIndex != secondIndex
      else { return }
    elements.swapAt(firstIndex, secondIndex)
  }
  
  mutating func enqueue(_ element: Element) {
    elements.append(element)
    siftUp(elementAtIndex: count - 1)
  }
  
  mutating func siftUp(elementAtIndex index: Int) {
    let parent = parentIndex(of: index)
    guard !isRoot(index),
      isHigherPriority(at: index, than: parent)
      else { return }
    swapElement(at: index, with: parent)
    siftUp(elementAtIndex: parent)
  }
  
  mutating func dequeue() -> Element? {
    guard !isEmpty
      else { return nil }
    swapElement(at: 0, with: count - 1)
    let element = elements.removeLast()
    if !isEmpty {
      siftDown(elementAtIndex: 0)
    }
    return element
  }
  
  mutating func siftDown(elementAtIndex index: Int) {
    let childIndex = highestPriorityIndex(for: index)
    if index == childIndex {
      return
    }
    swapElement(at: index, with: childIndex)
    siftDown(elementAtIndex: childIndex)
  }
}
```

_refers to_ https://www.raywenderlich.com/586-swift-algorithm-club-heap-and-priority-queue-data-structure  
