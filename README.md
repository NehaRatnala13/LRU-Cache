# LRU-Cache
Design a data structure that follows the constraints of a **Least Recently Used (LRU) cache**.
# LeetCode 146: LRU Cache

## 📝 Problem Description
Design a data structure that follows the constraints of a **Least Recently Used (LRU) cache**.

Implement the `LRUCache` class:
* **`LRUCache(int capacity)`** Initialize the LRU cache with positive size `capacity`.
* **`int get(int key)`** Return the value of the `key` if the key exists, otherwise return `-1`.
* **`void put(int key, int value)`** Update the value of the `key` if the key exists. Otherwise, add the `key-value` pair to the cache. If the number of keys exceeds the `capacity` from this operation, **evict** the least recently used key.

The functions `get` and `put` must each run in **O(1)** average time complexity.

---

## 💡 Intuition & Design
To achieve **O(1)** time complexity for both lookups and updates/evictions, we combine two data structures:
1. **Hash Map (Dictionary):** Provides O(1) lookup to check if a key exists and find its corresponding node.
2. **Doubly Linked List (DLL):** Maintains the execution order (recency). 
   * **Most Recently Used (MRU)** nodes are moved/added to the **head**.
   * **Least Recently Used (LRU)** nodes reside at the **tail**, making eviction an O(1) pointer update.

---

## 💻 Implementation (Python)

```python
class Node:
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}  # maps key to node
        
        # Dummy head and tail to avoid edge cases
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node: Node):
        """Remove an existing node from the linked list."""
        prev_node = node.prev
        next_node = node.next
        prev_node.next = next_node
        next_node.prev = prev_node

    def _insert_at_head(self, node: Node):
        """Insert a node right after the dummy head (MRU position)."""
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node

    def get(self, key: int) -> int:
        if key in self.cache:
            node = self.cache[key]
            self._remove(node)
            self._insert_at_head(node)  # Move to MRU position
            return node.value
        return -1

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            # Update existing key
            node = self.cache[key]
            node.value = value
            self._remove(node)
            self._insert_at_head(node)
        else:
            # Add new key
            if len(self.cache) >= self.capacity:
                # Evict LRU node (at the tail)
                lru_node = self.tail.prev
                self._remove(lru_node)
                del self.cache[lru_node.key]
            
            new_node = Node(key, value)
            self.cache[key] = new_node
            self._insert_at_head(new_node)
```

---

## 📊 Complexity Analysis

| Operation | Time Complexity | Space Complexity | Details |
| :--- | :--- | :--- | :--- |
| **`get()`** | $O(1)$ | $O(1)$ | Hash map lookup + pointer re-assignment inside DLL. |
| **`put()`** | $O(1)$ | $O(1)$ | Node creation, hash map insertion, and DLL head assignment. |
| **Total Space** | — | $O(C)$ | Where $C$ is the capacity of the cache (storing up to $C$ nodes). |
