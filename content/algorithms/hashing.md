---

title: "Understanding Hashing: Hash Tables, Collisions, and Probing"
date: 2026-05-31
draft: false
summary: "A practical guide to hash tables, collision handling, chaining, open addressing, probing strategies, and hash function design."
tags:
  - Algorithms
  - Data Structures
  - Hashing
  - Hash Table
aliases:
  - /posts/hashing/

---

Hashing is one of the most important techniques in data structures and algorithms. It is widely used in dictionaries, spell checkers, compiler symbol tables, file-system indexing, routing tables, and many other systems that require fast data access.

The main idea of hashing is simple:

> Instead of searching for data one by one, we compute where the data should be stored.

This is why hash tables can often support search, insertion, and deletion in **O(1)** average time.

---

## 1. Why Do We Need Hashing?

Suppose we store data in a normal list or array.

If we want to find a specific item, we may need to check each element one by one.

```text
[12, 25, 37, 41, 58]
```

If we want to search for `58`, we may need to scan from the beginning until we find it.

This gives us a time complexity of:

```text
O(n)
```

For a small amount of data, this is fine. But for large systems, such as databases or dictionaries, checking items one by one is too slow.

Hashing solves this problem by using a **hash function** to compute the index directly.

---

## 2. Direct Addressing: The Simple Idea

Before understanding hashing, we should first understand direct addressing.

In direct addressing, the key itself is used as the index of an array.

For example, if the key is `5`, we store the value at index `5`.

```text
T[5] = value
```

Searching is very fast because we can directly access the position.

```text
Search key 5 → go to T[5]
```

Therefore, search, insertion, and deletion can all take:

```text
O(1)
```

### Problem with Direct Addressing

Direct addressing works well only when the key range is small.

For example, if the largest possible key is `1,000,000`, we need an array of size `1,000,001`.

But what if we only store 100 actual records?

Most of the array would be empty, which wastes a lot of memory.

So the problem is:

> Direct addressing is fast, but it can waste too much space.

This is where hashing becomes useful.

---

## 3. What Is Hashing?

Hashing is a technique that maps a key to an index in a smaller table.

Instead of storing a key directly at index `k`, we store it at index `h(k)`.

Here, `h(k)` is called a **hash function**.

A simple example is:

```text
h(k) = k mod m
```

where:

```text
k = key
m = size of hash table
```

Example:

```text
m = 10

h(15) = 15 mod 10 = 5
h(27) = 27 mod 10 = 7
h(34) = 34 mod 10 = 4
```

So the keys are stored like this:

```text
Index 4 → 34
Index 5 → 15
Index 7 → 27
```

This allows us to use a smaller table instead of creating a huge array.

---

## 4. What Is a Hash Table?

A hash table is usually implemented using an array.

It uses a hash function to decide where each key should be stored.

```text
Key → Hash Function → Index → Hash Table
```

For example:

```text
15 → h(15) → 5
```

So key `15` is stored at index `5`.

The reason hash tables are fast is that the hash function directly computes the position of the key.

In the average case, a hash table supports:

| Operation | Average Case |
| --------- | ------------ |
| Search    | O(1)         |
| Insert    | O(1)         |
| Delete    | O(1)         |

However, this performance depends on two important factors:

1. The quality of the hash function
2. How collisions are handled

---

## 5. What Is a Collision?

A collision occurs when two different keys produce the same hash table index.

Example:

```text
h(k) = k mod 10

h(15) = 5
h(25) = 5
```

Both `15` and `25` want to be stored at index `5`.

```text
Index 5 → 15
Index 5 → 25
```

This is a collision.

Collisions happen because the number of possible keys is usually much larger than the number of slots in the hash table.

Therefore, a hash table must have a strategy to handle collisions.

There are two common collision handling methods:

1. Chaining
2. Open addressing

---

## 6. Collision Handling Method 1: Chaining

In chaining, each slot of the hash table stores a linked list.

If multiple keys hash to the same index, they are stored in the same linked list.

Example:

```text
h(15) = 5
h(25) = 5
h(35) = 5
```

The hash table becomes:

```text
Index 5 → [15] → [25] → [35]
```

Instead of replacing the old value, we add the new value into the chain.

### Advantages of Chaining

Chaining is easy to implement. It is also flexible because the hash table does not become full easily. If more keys collide at the same index, we can keep adding them into the linked list.

Chaining is useful when we do not know how many keys will be inserted or deleted.

### Disadvantages of Chaining

The main disadvantage is that long chains can reduce performance.

If many keys are stored in the same chain, searching may become slow.

In the worst case, all keys may hash to the same slot:

```text
Index 5 → [k1] → [k2] → [k3] → ... → [kn]
```

Then the hash table behaves like a linked list, and searching becomes:

```text
O(n)
```

Chaining also requires extra memory for storing links between nodes.

---

## 7. Load Factor in Chaining

The load factor measures how full the hash table is.

It is usually represented by the Greek letter alpha:

```text
α = n / m
```

where:

```text
n = number of keys
m = number of slots in the hash table
```

In chaining, the load factor represents the average number of elements stored in each chain.

The average time complexity of chaining is:

```text
O(1 + α)
```

This means:

```text
O(1) → compute the hash index
O(α) → search inside the chain
```

If the load factor is small, chains are short and performance is good.

If the load factor is large, chains become longer and search becomes slower.

---

## 8. Chaining Time Complexity

| Operation | Best Case | Average Case | Worst Case |
| --------- | --------- | ------------ | ---------- |
| Search    | O(1)      | O(1 + α)     | O(n)       |
| Insert    | O(1)      | O(1)         | O(n)       |
| Delete    | O(1)      | O(1 + α)     | O(n)       |

For insertion, the average case is usually `O(1)` if we insert directly at the head of the chain.

However, if we first check whether the key already exists, insertion may require searching the chain first.

---

## 9. Collision Handling Method 2: Open Addressing

In open addressing, all keys are stored directly inside the hash table itself.

Unlike chaining, open addressing does not use linked lists.

If a collision occurs, the algorithm searches for another available slot.

This process is called **probing**.

Example:

```text
h(15) = 5
```

If index `5` is empty, insert `15` there.

```text
Index 5 → 15
```

Now suppose:

```text
h(25) = 5
```

But index `5` is already occupied.

So we probe another slot.

```text
Index 5 → occupied
Index 6 → empty
```

Then we store `25` at index `6`.

```text
Index 6 → 25
```

In open addressing, the load factor must be less than 1:

```text
0 ≤ α < 1
```

This is because every key must be stored inside the table itself.

If the table is full, no new key can be inserted unless the table is resized.

---

## 10. Probing in Open Addressing

Probing means checking possible positions in the hash table until we find the required key or an empty slot.

For insertion:

```text
Keep probing until an empty slot is found.
```

For search:

```text
Keep probing until the key is found or an empty slot is reached.
```

For deletion:

```text
Search for the key, then mark the slot as DELETED.
```

In open addressing, deletion is more complicated than insertion and search.

If we simply remove a key and mark its slot as empty, future searches may fail.

Therefore, deleted slots are usually marked with a special value:

```text
DELETED
```

This tells the search operation:

> Do not stop here. Continue probing.

---

## 11. Linear Probing

Linear probing is the simplest probing technique.

If the target slot is occupied, we check the next slot.

The formula is:

```text
h(k, i) = (h'(k) + i) mod m
```

where:

```text
i = probe number
m = table size
```

Example:

```text
Index 5 → occupied
Index 6 → occupied
Index 7 → empty
```

So we insert the key at index `7`.

### Problem with Linear Probing

Linear probing can cause **primary clustering**.

Primary clustering means that many occupied slots become grouped together.

Example:

```text
[empty] [full] [full] [full] [full] [empty]
```

When a cluster becomes large, future insertions and searches become slower.

---

## 12. Quadratic Probing

Quadratic probing does not simply move one step forward.

Instead, it uses a quadratic formula to decide the next position.

The formula is:

```text
h(k, i) = (h'(k) + c1i + c2i²) mod m
```

Quadratic probing reduces the primary clustering problem because it jumps in a wider pattern.

However, it can still suffer from **secondary clustering**.

Secondary clustering happens when keys with the same initial hash value follow the same probing sequence.

---

## 13. Double Hashing

Double hashing uses a second hash function to decide the step size.

The formula is:

```text
h(k, i) = (h1(k) + i × h2(k)) mod m
```

Here:

```text
h1(k) = first hash function
h2(k) = second hash function
```

Double hashing is usually one of the best open addressing methods because it distributes probes more randomly compared with linear probing and quadratic probing.

It reduces clustering and often gives better performance.

---

## 14. Open Addressing Time Complexity

The performance of open addressing depends heavily on the load factor.

```text
α = n / m
```

where:

```text
n = number of stored keys
m = table size
```

In open addressing:

```text
0 ≤ α < 1
```

As the table becomes more full, the number of probes increases.

### Unsuccessful Search

If the table is very full, an unsuccessful search may need to check many slots before finding an empty one.

The expected number of probes for unsuccessful search is:

```text
O(1 / (1 - α))
```

This means when `α` gets close to 1, the search becomes slower.

For example:

```text
α = 0.5  → 1 / (1 - 0.5) = 2
α = 0.9  → 1 / (1 - 0.9) = 10
```

When the table is 90% full, the algorithm may need many more probes.

### Insert

Insertion in open addressing also depends on finding an empty slot.

| Case         | Time Complexity |
| ------------ | --------------- |
| Best Case    | O(1)            |
| Average Case | O(1 / (1 - α))  |
| Worst Case   | O(n)            |

### Search

| Case         | Time Complexity |
| ------------ | --------------- |
| Best Case    | O(1)            |
| Average Case | O(1)            |
| Worst Case   | O(n)            |

### Delete

Deletion requires searching for the key first and then marking it as `DELETED`.

So its time complexity follows the search operation.

| Case         | Time Complexity |
| ------------ | --------------- |
| Best Case    | O(1)            |
| Average Case | O(1)            |
| Worst Case   | O(n)            |

---

## 15. What Makes a Good Hash Function?

A good hash function should distribute keys evenly across the hash table.

If the hash function distributes keys poorly, many keys may collide in the same area.

This causes long chains in chaining or many probes in open addressing.

A good hash function should:

1. Be fast to compute
2. Distribute keys uniformly
3. Reduce collisions
4. Avoid obvious patterns

There are two common hash function methods:

1. Division method
2. Multiplication method

---

## 16. Division Method

The division method uses the remainder after division.

```text
h(k) = k mod m
```

Example:

```text
m = 10

h(25) = 25 mod 10 = 5
```

The choice of `m` is important.

If `m` is poorly chosen, many keys may collide.

A prime number is often preferred because it helps reduce patterns in the distribution of keys.

---

## 17. Multiplication Method

The multiplication method works by multiplying the key by a constant value.

The general formula is:

```text
h(k) = floor(m × frac(kA))
```

where:

```text
0 < A < 1
frac(x) = fractional part of x
m = table size
```

A commonly suggested value for `A` is:

```text
0.618033988
```

The process is:

1. Multiply the key `k` by a constant `A`
2. Extract the fractional part
3. Multiply it by `m`
4. Take the floor value

This gives an index between:

```text
0 and m - 1
```

---

## 18. Chaining vs Open Addressing

| Feature            | Chaining               | Open Addressing                         |
| ------------------ | ---------------------- | --------------------------------------- |
| Storage            | Uses linked lists      | Stores everything inside the table      |
| Load Factor        | Can be greater than 1  | Must be less than 1                     |
| Collision Handling | Add to chain           | Probe for another slot                  |
| Memory Usage       | Extra memory for links | No extra linked list memory             |
| Deletion           | Easier                 | More complicated                        |
| Cache Performance  | Usually worse          | Usually better                          |
| When to Use        | Unknown number of keys | Fixed-size table with good load control |

Both methods are useful.

Chaining is easier to implement and more flexible.

Open addressing can be more memory-efficient and cache-friendly, but it requires careful handling of probing, deletion, and resizing.

---

## 19. Final Time Complexity Summary

### Chaining

| Operation | Average Case | Worst Case |
| --------- | ------------ | ---------- |
| Search    | O(1 + α)     | O(n)       |
| Insert    | O(1)         | O(n)       |
| Delete    | O(1 + α)     | O(n)       |

### Open Addressing

| Operation | Average Case   | Worst Case |
| --------- | -------------- | ---------- |
| Search    | O(1)           | O(n)       |
| Insert    | O(1 / (1 - α)) | O(n)       |
| Delete    | O(1)           | O(n)       |

The worst case happens when collisions are severe or when the table becomes too full.

---

## 20. Hashing in Programming Languages

Hashing is not only a theory topic. It is used in many real programming languages.

Examples:

| Language   | Hash Table Implementation |
| ---------- | ------------------------- |
| Java       | HashMap                   |
| Python     | dict                      |
| C++        | unordered_map             |
| JavaScript | Map                       |

Example in Java:

```java
import java.util.HashMap;

public class Main {
    public static void main(String[] args) {
        HashMap<String, Integer> scores = new HashMap<>();

        scores.put("Alice", 90);
        scores.put("Bob", 85);

        System.out.println(scores.get("Alice"));
    }
}
```

In this example, `"Alice"` is the key.

Java uses hashing internally to find where the value `90` is stored.

This is why `HashMap` can retrieve values very quickly on average.

---

## Conclusion

Hashing is a powerful technique for storing and retrieving data efficiently.

The main idea is to use a hash function to convert a key into an index.

This allows hash tables to support search, insertion, and deletion in **O(1)** average time.

However, collisions can happen when multiple keys map to the same index.

To handle collisions, we can use:

1. Chaining
2. Open addressing

Chaining stores colliding keys in linked lists, while open addressing stores all keys inside the table and uses probing to find available slots.

A good hash function and a proper load factor are essential for keeping hash table operations efficient.

Hashing is widely used in real-world software systems, including dictionaries, databases, compilers, file systems, and programming language libraries such as Java `HashMap`.
