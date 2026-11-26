# 19. Remove Nth Node From End of List

| Platform | Difficulty | Topic | Language | Status |
|----------|:----------:|-------|----------|:------:|
| [LeetCode](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) | 🟡 Medium | Linked List, Two Pointers | JavaScript | ✅ Accepted (0 ms) |

---

## 📝 Question

Given the `head` of a linked list, remove the `n`th node from the end of the list and return its head.

### Example 1

```
Before:  1 ──► 2 ──► 3 ──► [4] ──► 5        (4 is 2nd from the end)

                     ⬇

After:   1 ──► 2 ──► 3 ──────────► 5
```

**Input:** `head = [1,2,3,4,5], n = 2`

**Output:** `[1,2,3,5]`

### Example 2

**Input:** `head = [1], n = 1`

**Output:** `[]`

### Example 3

**Input:** `head = [1,2], n = 1`

**Output:** `[1]`

### Constraints

- The number of nodes in the list is `sz`.
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`

---

## 💡 What Is the Question Really Asking?

A **linked list** is like a train 🚂: each carriage (node) only knows the carriage **right behind it**. You can't jump to the middle, and you can't walk backwards. You can only start at the front and move forward one by one.

We need to **unhook one carriage**, the one that is `n`th **from the back**.

To unhook carriage `4`, we have to stand on carriage **`3`** (the one just before it) and connect `3` straight to `5`:

```
3.next = 3.next.next      // 3 now skips over 4 and points to 5
```

So the real question is: **how do we find the carriage just before the target?**

### The slow way: walk the train twice

1. Walk to the end and count the carriages (5).
2. The target is at position `5 − 2 + 1 = 4`, so walk again and stop at position 3.

It works, but it needs **two trips**.

### The smart way: two pointers with a gap 📏

Imagine two people walking along the train, **`fast`** and **`slow`**, holding a rope of a fixed length between them.

1. `fast` gets a head start of **`n + 1`** carriages.
2. Then both walk together, one step at a time, keeping the same gap.
3. When `fast` walks **off the end** of the train, `slow` is standing exactly on the carriage **just before** the target.

One trip, done. ✅

### Why the `dummy` node?

What if we need to remove the **first** carriage (like Example 2, or `[1,2]` with `n = 2`)? There's no carriage before it to stand on!

So we add a fake carriage called **`dummy`** at the very front. Now **every** real carriage, even the first one, has someone before it. At the end, we return `dummy.next`, which is the real head.

---

## ✅ Solution (JavaScript)

```javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} head
 * @param {number} n
 * @return {ListNode}
 */
var removeNthFromEnd = function(head, n) {
    let dummy = new ListNode(0, head);
    let fast = dummy;
    let slow = dummy;

    for (let i = 0; i <= n; i++){
        fast = fast.next;
    }
    while (fast !== null) {
        fast = fast.next;
        slow = slow.next;
    }
    slow.next = slow.next.next;

    return dummy.next;
};
```

### What Each Part Does

| Code | In simple words |
|------|-----------------|
| `let dummy = new ListNode(0, head);` | Add a fake node at the front that points to the real head. Now even the first node has a node before it. |
| `let fast = dummy; let slow = dummy;` | Both walkers start on the fake node. |
| `for (let i = 0; i <= n; i++)` | Runs **`n + 1`** times (from 0 up to and including `n`)… |
| `fast = fast.next;` | …so `fast` gets a head start of `n + 1` steps. |
| `while (fast !== null)` | Keep going until `fast` walks off the end of the list. |
| `fast = fast.next; slow = slow.next;` | Move both one step, so the gap between them stays the same. |
| `slow.next = slow.next.next;` | `slow` is now just **before** the target. Point it past the target, which removes the target from the list. |
| `return dummy.next;` | The real head. (If the old head was removed, this is the new head automatically.) |

**Why `n + 1` and not `n`?** With a gap of `n`, `slow` would stop **on** the target. But to unhook a node you must stand on the node **before** it. One extra step of gap puts `slow` exactly there.

---

## 🔍 Step-by-Step Dry Run

### Example 1: `head = [1,2,3,4,5]`, `n = 2` → remove `4`

With the dummy node added:

```
D ──► 1 ──► 2 ──► 3 ──► 4 ──► 5 ──► null
```

**Start:** `fast = D`, `slow = D`

#### Phase 1: give `fast` a head start of `n + 1 = 3` steps

| `i` | `fast` moves to |
|:---:|:---------------:|
| 0 | 1 |
| 1 | 2 |
| 2 | 3 |

```
D ──► 1 ──► 2 ──► 3 ──► 4 ──► 5 ──► null
▲                 ▲
slow              fast        (gap of 3)
```

#### Phase 2: move both until `fast` is `null`

| Round | `fast` | `slow` |
|:-----:|:------:|:------:|
| start | 3 | D |
| 1 | 4 | 1 |
| 2 | 5 | 2 |
| 3 | **null** | **3** ← stop here |

```
D ──► 1 ──► 2 ──► 3 ──► 4 ──► 5 ──► null
                  ▲                  ▲
                  slow               fast
```

#### Phase 3: unhook the target

`slow` is on `3`, and `slow.next` is `4` (our target).

```
slow.next = slow.next.next   →   3.next = 5
```

```
D ──► 1 ──► 2 ──► 3 ─────────► 5 ──► null
```

Return `dummy.next` = `1` → **`[1,2,3,5]`** ✅

### Example 2: `head = [1]`, `n = 1` → remove the only node

```
D ──► 1 ──► null
```

- **Head start** of `n + 1 = 2` steps: `fast` goes `D → 1 → null`.
- **While loop:** `fast` is already `null`, so it doesn't run. `slow` stays on **`D`**.
- **Unhook:** `D.next = 1.next = null`.

```
D ──► null
```

Return `dummy.next` = `null` → **`[]`** ✅

This is exactly where the **dummy** saves us: the node we removed was the head, and `slow` could still stand on `D` before it.

### Example 3: `head = [1,2]`, `n = 1` → remove `2`

```
D ──► 1 ──► 2 ──► null
```

- **Head start** of 2 steps: `fast` goes `D → 1 → 2`.
- **While loop:** one round → `fast = null`, `slow = 1`.
- **Unhook:** `1.next = 2.next = null`.

Return **`[1]`** ✅

---

## ⚠️ Common Mistakes

**1. No dummy node**

Without a dummy, removing the **head** (e.g. `[1]` with `n = 1`) has no node before it, and `slow.next.next` crashes on `null`. The dummy removes this special case completely.

**2. Head start of `n` instead of `n + 1`**

Then `slow` stops **on** the target instead of the node before it, and you'd remove the wrong node.

**3. Returning `head` instead of `dummy.next`**

If the original head was removed, `head` still points to the deleted node. `dummy.next` always points to the correct new head.

**4. Trying to walk backwards**

A singly linked list only has `next`, never `previous`. That's why we use the gap trick instead of counting from the back.

---

## 🔁 Other Ways to Write It

**Python:**

```python
class Solution:
    def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
        dummy = ListNode(0, head)
        fast = slow = dummy

        for _ in range(n + 1):      # give fast a head start of n + 1 steps
            fast = fast.next

        while fast:                 # move both until fast falls off the end
            fast = fast.next
            slow = slow.next

        slow.next = slow.next.next  # skip the target node
        return dummy.next
```

**Java:**

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0, head);
        ListNode fast = dummy, slow = dummy;

        for (int i = 0; i <= n; i++) {
            fast = fast.next;
        }
        while (fast != null) {
            fast = fast.next;
            slow = slow.next;
        }
        slow.next = slow.next.next;
        return dummy.next;
    }
}
```

**C++** (also frees the removed node's memory):

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode dummy(0, head);
        ListNode* fast = &dummy;
        ListNode* slow = &dummy;

        for (int i = 0; i <= n; i++) {
            fast = fast->next;
        }
        while (fast != nullptr) {
            fast = fast->next;
            slow = slow->next;
        }

        ListNode* target = slow->next;
        slow->next = target->next;
        delete target;              // free the removed node's memory
        return dummy.next;
    }
};
```

> Only the JavaScript solution was submitted on LeetCode (accepted, 0 ms). The JavaScript was also tested locally on all three examples, extra edge cases (removing the head of `[1,2]`, removing the first of 5) and 2,000 random lists. The Python, Java and C++ versions were tested on all three examples.

---

## ⏱️ Complexity

Let `L` = number of nodes in the list.

| | Value | Why |
|---|:-----:|-----|
| **Time** | `O(L)` | `fast` walks the list once from front to end. That's a **single pass**. |
| **Space** | `O(1)` | Just three pointers (`dummy`, `fast`, `slow`), no extra arrays. |

---

## 🧠 Key Takeaways

- **"Nth from the end" in one pass** → two pointers with a fixed gap.
- Give `fast` a head start of **`n + 1`** so `slow` lands on the node **before** the target.
- A **dummy node** in front removes the special case of deleting the head.
- To remove a node from a linked list: `prev.next = prev.next.next`.
- Always return **`dummy.next`**, not the old `head`.

---

## 🤝 Connect With Me

<a href="https://vaibhav1.me" target="_blank">
  <img src="https://img.shields.io/badge/🌐_Portfolio-vaibhav1.me-00D4FF?style=for-the-badge" alt="Portfolio"/>
</a>
<a href="https://www.linkedin.com/in/vaibhavsingh125/" target="_blank">
  <img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn"/>
</a>
<a href="mailto:singh12521vaibhav@gmail.com">
  <img src="https://img.shields.io/badge/Email-EA4335?style=for-the-badge&logo=gmail&logoColor=white" alt="Email"/>
</a>
<a href="https://leetcode.com/u/vaibhav125s/" target="_blank">
  <img src="https://img.shields.io/badge/LeetCode-FFA116?style=for-the-badge&logo=leetcode&logoColor=black" alt="LeetCode"/>
</a>

[⬅️ Back to all problems](../README.md)
