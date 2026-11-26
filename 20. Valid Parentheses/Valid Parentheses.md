# 20. Valid Parentheses

| Platform | Difficulty | Topic | Language | Status |
|----------|:----------:|-------|----------|:------:|
| [LeetCode](https://leetcode.com/problems/valid-parentheses/) | 🟢 Easy | String, Stack, Hash Table | JavaScript | ✅ Accepted (0 ms) |

---
 
## 📝 Question  

Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.

### Example 1

**Input:** `s = "()"`

**Output:** `true`

### Example 2

**Input:** `s = "()[]{}"`

**Output:** `true`

### Example 3

**Input:** `s = "(]"`

**Output:** `false`

### Example 4

**Input:** `s = "([])"`

**Output:** `true`

### Example 5

**Input:** `s = "([)]"`

**Output:** `false`

### Constraints

- `1 <= s.length <= 10^4`
- `s` consists of parentheses only `'()[]{}'`.

---

## 💡 What Is the Question Really Asking?

Think of brackets like **boxes inside boxes**. 📦

- `(` opens a box, `)` closes it.
- You can put a box inside another box: `([])` ✅
- But you must close the **inner** box before the **outer** one. `([)]` tries to close the outer `(` while the inner `[` is still open. ❌

So the rule is:

> **Every closing bracket must match the most recent bracket that is still open.**

### The tool for "most recent": a Stack 🥞

A **stack** is like a pile of plates: you can only add to the top and take from the top. The last plate you put on is the first one you take off.

That's exactly what we need:

- See an **opening** bracket? Put it on the pile.
- See a **closing** bracket? Take the top one off the pile. It **must** be its matching partner.
- At the end, the pile must be **empty**. Otherwise some brackets were never closed.

---

## ✅ Solution (JavaScript)

```javascript
/**
 * @param {string} s
 * @return {boolean}
 */
var isValid = function(s) {
    const stack = [];
    const map = {
        ')': '(',
        '}': '{',
        ']': '['
    };

    for (const char of s) {
        if (char in map) {
            if (stack.pop() !== map[char]) {
                return false;
            }
        } else {
            stack.push(char);
        }
    }

    return stack.length === 0;
};
```

### What Each Part Does

| Code | In simple words |
|------|-----------------|
| `const stack = [];` | Our pile of plates. It holds the opening brackets that are still waiting to be closed. |
| `const map = { ')': '(', ... }` | For each **closing** bracket, which **opening** bracket it needs. |
| `for (const char of s)` | Look at the brackets one by one, left to right. |
| `if (char in map)` | Is this a **closing** bracket? (Only closing brackets are keys in `map`.) |
| `stack.pop() !== map[char]` | Take the top opening bracket off the pile. If it's not the right partner, the string is broken… |
| `return false;` | …so stop right away. |
| `stack.push(char);` | It's an **opening** bracket, so put it on the pile and wait for its partner. |
| `return stack.length === 0;` | After checking everything, the pile must be empty. Leftover brackets were never closed. |

**Neat detail:** if a closing bracket comes when the pile is **empty** (like `")"`), `stack.pop()` gives `undefined`, which is never equal to `'('`. So the code correctly returns `false` without needing an extra check.

---

## 🔍 Step-by-Step Dry Run

### Example 4: `s = "([])"` → expected `true`

| Step | Char | Opening or closing? | Action | Stack after |
|:----:|:----:|---------------------|--------|-------------|
| 1 | `(` | opening | push `(` | `(` |
| 2 | `[` | opening | push `[` | `( [` |
| 3 | `]` | closing, needs `[` | pop → `[` ✅ match | `(` |
| 4 | `)` | closing, needs `(` | pop → `(` ✅ match | *(empty)* |

End: stack is **empty** → return **`true`** ✅

In plain words: we opened `(`, then opened `[` inside it. `]` closed the inner `[` first, then `)` closed the outer `(`. Every box was closed in the right order.

### Example 5: `s = "([)]"` → expected `false`

| Step | Char | Opening or closing? | Action | Stack after |
|:----:|:----:|---------------------|--------|-------------|
| 1 | `(` | opening | push `(` | `(` |
| 2 | `[` | opening | push `[` | `( [` |
| 3 | `)` | closing, needs `(` | pop → `[` ❌ **not** `(` | — |

Return **`false`** right away ✅

In plain words: when `)` arrived, the most recent open box was `[`, not `(`. You can't close the outer box while the inner one is still open.

### Example 3: `s = "(]"` → expected `false`

| Step | Char | Action | Stack after |
|:----:|:----:|--------|-------------|
| 1 | `(` | push `(` | `(` |
| 2 | `]` | closing, needs `[` → pop → `(` ❌ mismatch | — |

Return **`false`** ✅

### Example 2: `s = "()[]{}"` → expected `true`

| Step | Char | Action | Stack after |
|:----:|:----:|--------|-------------|
| 1 | `(` | push | `(` |
| 2 | `)` | pop `(` ✅ | *(empty)* |
| 3 | `[` | push | `[` |
| 4 | `]` | pop `[` ✅ | *(empty)* |
| 5 | `{` | push | `{` |
| 6 | `}` | pop `{` ✅ | *(empty)* |

End: stack is **empty** → **`true`** ✅

### Two extra tricky cases

| Input | What happens | Result |
|-------|--------------|:------:|
| `"(("` | Both are pushed, nothing closes them. Stack ends as `( (`, **not empty**. | `false` |
| `")"` | Closing bracket with an empty stack. `pop()` gives `undefined` ≠ `(`. | `false` |

---

## ⚠️ Common Mistakes

**1. Just counting brackets**

Counting "number of `(` = number of `)`" isn't enough. `"([)]"` has one of each type, but it's still invalid because the **order** is wrong. You need a stack to check the order.

**2. Forgetting the final empty check**

```javascript
return true; // ❌ Wrong
```

`"(("` never hits a mismatch, so the loop finishes fine, but nothing was closed. Always return `stack.length === 0`.

**3. Not handling a closing bracket on an empty stack**

In JavaScript, `pop()` on an empty array safely returns `undefined`. In **Java or C++**, popping an empty stack **throws an error or crashes**, so you must check `isEmpty()` first (see the versions below).

**4. Putting opening brackets as the map keys**

If the map is `{ '(': ')' }`, then `char in map` finds **opening** brackets instead, and the logic flips. Keep closing brackets as the keys.

---

## 🔁 Other Ways to Write It

**Python:**

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        pairs = {")": "(", "}": "{", "]": "["}

        for char in s:
            if char in pairs:
                if not stack or stack.pop() != pairs[char]:
                    return False
            else:
                stack.append(char)

        return not stack
```

**Java:**

```java
class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        Map<Character, Character> pairs = Map.of(')', '(', '}', '{', ']', '[');

        for (char c : s.toCharArray()) {
            if (pairs.containsKey(c)) {
                if (stack.isEmpty() || stack.pop() != pairs.get(c)) {
                    return false;
                }
            } else {
                stack.push(c);
            }
        }
        return stack.isEmpty();
    }
}
```

**C++:**

```cpp
class Solution {
public:
    bool isValid(string s) {
        stack<char> st;
        unordered_map<char, char> pairs = {{')', '('}, {'}', '{'}, {']', '['}};

        for (char c : s) {
            if (pairs.count(c)) {
                if (st.empty() || st.top() != pairs[c]) {
                    return false;
                }
                st.pop();
            } else {
                st.push(c);
            }
        }
        return st.empty();
    }
};
```

> Only the JavaScript solution was submitted on LeetCode (accepted, 0 ms). The Python, Java and C++ versions were tested locally on all five examples plus `")"`, and the Python version was also checked against a simple "keep removing `()`, `[]`, `{}`" checker on 5,000 random strings.

---

## ⏱️ Complexity

| | Value | Why |
|---|:-----:|-----|
| **Time** | `O(n)` | We look at each character once. Push and pop are instant. |
| **Space** | `O(n)` | In the worst case (like `"(((((("`), every character goes on the stack. |

**Bonus shortcut:** if the length is **odd**, the answer is always `false`, because brackets come in pairs. Adding `if (s.length % 2 !== 0) return false;` at the top skips the work for those inputs.

---

## 🧠 Key Takeaways

- **"Match the most recent open thing"** → use a **stack** (last in, first out).
- Opening bracket → **push**. Closing bracket → **pop** and check it's the partner.
- At the end, the stack must be **empty**.
- Counting alone can't check **order**. The stack does.
- This same idea shows up in HTML tag matching, code editors highlighting brackets, and calculators reading `(2 + 3) × 4`.

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
