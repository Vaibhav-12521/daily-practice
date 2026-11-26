# 17. Letter Combinations of a Phone Number

| Platform | Difficulty | Topic | Language | Status |
|----------|:----------:|-------|----------|:------:|
| [LeetCode](https://leetcode.com/problems/letter-combinations-of-a-phone-number/) | 🟡 Medium | Hash Table, String, Backtracking, Recursion | JavaScript | ✅ Accepted (0 ms) |

---

## 📝 Question

Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent. Return the answer in **any order**.

A mapping of digits to letters (just like on the telephone buttons) is given below. Note that `1` does not map to any letters.

```
┌─────────┬─────────┬─────────┐
│    1    │    2    │    3    │
│         │   abc   │   def   │
├─────────┼─────────┼─────────┤
│    4    │    5    │    6    │
│   ghi   │   jkl   │   mno   │
├─────────┼─────────┼─────────┤
│    7    │    8    │    9    │
│  pqrs   │   tuv   │  wxyz   │
├─────────┼─────────┼─────────┤
│    *    │    0    │    #    │
└─────────┴─────────┴─────────┘
```

### Example 1

**Input:** `digits = "23"`

**Output:** `["ad","ae","af","bd","be","bf","cd","ce","cf"]`

### Example 2

**Input:** `digits = "2"` 

**Output:** `["a","b","c"]` 
 
### Constraints

- `1 <= digits.length <= 4`
- `digits[i]` is a digit in the range `['2', '9']`. 

---   

## 💡 What Is the Question Really Asking?
  
Remember old phones where you typed messages using number keys? Each key had 3 or 4 letters on it.
 
If someone presses `2` then `3`, they could mean **any** letter from `2` (a, b, c) followed by **any** letter from `3` (d, e, f). We need to list **every** possible word. 

Think of it like ordering a meal combo:

> **Pick 1 starter from {a, b, c}, then pick 1 main from {d, e, f}.**
> List every possible meal.

That's 3 × 3 = **9** combos.

### How do we list them all? Backtracking 🌳

We build each word **one letter at a time**, like walking down a tree:

```
                    ""
          ┌─────────┼─────────┐
          a         b         c          ← pick a letter for "2"
        ┌─┼─┐     ┌─┼─┐     ┌─┼─┐
       ad ae af  bd be bf  cd ce cf      ← pick a letter for "3"
```

- At each level, we try **every** letter for the current digit.
- When the word is as long as the input, we've reached the bottom, so we **save** it.
- Then we go back up and try the next letter. Going back up is why it's called **backtracking**.

---

## ✅ Solution (JavaScript)

```javascript
/**
 * @param {string} digits
 * @return {string[]}
 */
var letterCombinations = function(digits) {
    if (digits.length === 0) return [];

    const map = {
        '2': 'abc',
        '3': 'def',
        '4': 'ghi',
        '5': 'jkl',
        '6': 'mno',
        '7': 'pqrs',
        '8': 'tuv',
        '9': 'wxyz'
    };

    const result = [];

    const backtrack = (index, path) => {
        if (index === digits.length) {
            result.push(path);
            return;
        }

        const letters = map[digits[index]];
        for (let char of letters) {
            backtrack(index + 1, path + char);
        }
    };

    backtrack(0, '');
    return result;
};
```

### What Each Part Does

| Code | In simple words |
|------|-----------------|
| `if (digits.length === 0) return [];` | Safety check: no digits means no words. |
| `const map = { '2': 'abc', ... }` | The phone keypad: which letters belong to which number. |
| `const result = [];` | A list to collect every finished word. |
| `const backtrack = (index, path) => {` | A helper that builds words. `index` = which digit we're on now. `path` = the word built so far. |
| `if (index === digits.length)` | We've picked a letter for **every** digit, so the word is complete… |
| `result.push(path); return;` | …save it, then go back up the tree. |
| `const letters = map[digits[index]];` | Look up the letters for the current digit, e.g. `'3'` → `'def'`. |
| `for (let char of letters)` | Try each of those letters, one by one. |
| `backtrack(index + 1, path + char);` | Add the letter to the word and move on to the **next** digit. |
| `backtrack(0, '');` | Start at the first digit with an empty word. |

`path + char` makes a **new** string each time, so we never have to "undo" anything by hand. When the function returns, `path` is still exactly what it was before.

---

## 🔍 Step-by-Step Dry Run

### Example: `digits = "23"`

- Digit `'2'` → letters `"abc"`
- Digit `'3'` → letters `"def"`
- We need words of length **2**.

**Start:** `backtrack(0, "")`

Here is every call, in the order the code makes them:

| # | Call | What happens | `result` after |
|:-:|------|--------------|----------------|
| 1 | `backtrack(0, "")` | index 0 → digit `'2'` → try `a`, `b`, `c` | `[]` |
| 2 | ↳ `backtrack(1, "a")` | index 1 → digit `'3'` → try `d`, `e`, `f` | `[]` |
| 3 | ↳↳ `backtrack(2, "ad")` | index 2 = length → **save "ad"** | `["ad"]` |
| 4 | ↳↳ `backtrack(2, "ae")` | **save "ae"** | `["ad","ae"]` |
| 5 | ↳↳ `backtrack(2, "af")` | **save "af"** | `["ad","ae","af"]` |
| | ⬆️ back to call 1 | done with `a`, now try `b` | |
| 6 | ↳ `backtrack(1, "b")` | digit `'3'` → try `d`, `e`, `f` | |
| 7 | ↳↳ `backtrack(2, "bd")` | **save "bd"** | `[…,"bd"]` |
| 8 | ↳↳ `backtrack(2, "be")` | **save "be"** | `[…,"be"]` |
| 9 | ↳↳ `backtrack(2, "bf")` | **save "bf"** | `[…,"bf"]` |
| | ⬆️ back to call 1 | done with `b`, now try `c` | |
| 10 | ↳ `backtrack(1, "c")` | digit `'3'` → try `d`, `e`, `f` | |
| 11 | ↳↳ `backtrack(2, "cd")` | **save "cd"** | `[…,"cd"]` |
| 12 | ↳↳ `backtrack(2, "ce")` | **save "ce"** | `[…,"ce"]` |
| 13 | ↳↳ `backtrack(2, "cf")` | **save "cf"** | `[…,"cf"]` |
| | ⬆️ back to call 1 | tried all of `a`, `b`, `c` → done | |

**Answer:** `["ad","ae","af","bd","be","bf","cd","ce","cf"]` ✅ matches the expected output.

### In plain words

1. Start with an empty word.
2. Pick `a`. Then try every letter of `3`: `ad`, `ae`, `af`. Save all three.
3. Go back, and pick `b` instead. Try `d`, `e`, `f` again: `bd`, `be`, `bf`.
4. Go back, and pick `c`: `cd`, `ce`, `cf`.
5. No more letters for `2`, so we're done. 9 words in total.

### Example: `digits = "2"`

- `backtrack(0, "")` → digit `'2'` → try `a`, `b`, `c`
- Each of `backtrack(1, "a")`, `backtrack(1, "b")`, `backtrack(1, "c")` has index 1 = length → save it.

**Answer:** `["a","b","c"]` ✅

---

## 📏 How Many Words?

Multiply the number of letters on each key:

| Input | Calculation | Words |
|-------|-------------|:-----:|
| `"2"` | 3 | 3 |
| `"23"` | 3 × 3 | 9 |
| `"79"` | 4 × 4 (`pqrs` × `wxyz`) | 16 |
| `"7979"` | 4 × 4 × 4 × 4 | **256** (the most possible, since length ≤ 4) |

---

## ⚠️ Common Mistakes

**1. Using nested loops**

```javascript
for (const a of map[digits[0]])
    for (const b of map[digits[1]])   // ❌ only works for exactly 2 digits
```

The input can have 1, 2, 3 or 4 digits, so you'd need a different number of loops each time. Recursion handles **any** length with one function.

**2. Forgetting that `7` and `9` have 4 letters**

`7` = `pqrs` and `9` = `wxyz`. If you assume every key has 3 letters, you'll miss words.

**3. Forgetting to stop**

Without `if (index === digits.length) { ... return; }`, the function keeps going past the last digit and breaks (`map[undefined]`).

**4. Changing a shared path without undoing it**

If you use an array or `StringBuilder` for the path (like the Java version below), you must **remove** the last letter after the recursive call. Otherwise letters pile up: `ad`, `ade`, `adef`… Our JavaScript avoids this by using `path + char`, which makes a fresh string.

---

## 🔁 Other Ways to Write It

**Python:**

```python
class Solution:
    def letterCombinations(self, digits: str) -> List[str]:
        if not digits:
            return []

        phone = {"2": "abc", "3": "def", "4": "ghi", "5": "jkl",
                 "6": "mno", "7": "pqrs", "8": "tuv", "9": "wxyz"}
        result = []

        def backtrack(index, path):
            if index == len(digits):
                result.append(path)
                return
            for char in phone[digits[index]]:
                backtrack(index + 1, path + char)

        backtrack(0, "")
        return result
```

**Java** (choose → explore → undo, with a `StringBuilder`):

```java
class Solution {
    private static final String[] PHONE = {
        "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"
    };

    public List<String> letterCombinations(String digits) {
        List<String> result = new ArrayList<>();
        if (digits.isEmpty()) return result;
        backtrack(digits, 0, new StringBuilder(), result);
        return result;
    }

    private void backtrack(String digits, int index, StringBuilder path, List<String> result) {
        if (index == digits.length()) {
            result.add(path.toString());
            return;
        }
        String letters = PHONE[digits.charAt(index) - '0'];
        for (char c : letters.toCharArray()) {
            path.append(c);                              // choose
            backtrack(digits, index + 1, path, result);  // explore
            path.deleteCharAt(path.length() - 1);        // undo (backtrack)
        }
    }
}
```

**C++:**

```cpp
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        vector<string> result;
        if (digits.empty()) return result;
        string path;
        backtrack(digits, 0, path, result);
        return result;
    }

private:
    const vector<string> phone = {
        "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"
    };

    void backtrack(const string& digits, int index, string& path, vector<string>& result) {
        if (index == (int)digits.size()) {
            result.push_back(path);
            return;
        }
        for (char c : phone[digits[index] - '0']) {
            path.push_back(c);                           // choose
            backtrack(digits, index + 1, path, result);  // explore
            path.pop_back();                             // undo (backtrack)
        }
    }
};
```

> Only the JavaScript solution was submitted on LeetCode (accepted, 0 ms). The Python, Java and C++ versions were tested locally on both examples, and the Python version was also checked against `itertools.product` on 300 random inputs.

---

## ⏱️ Complexity

Let `n` = number of digits (at most 4).

| | Value | Why |
|---|:-----:|-----|
| **Time** | `O(4ⁿ × n)` | There can be up to `4ⁿ` words (when every key has 4 letters), and building each word of length `n` takes `n` steps. |
| **Space** | `O(n)` extra | The recursion goes `n` levels deep. (The output list itself holds up to `4ⁿ` words.) |

Since `n ≤ 4`, the worst case is just **256 words**, so it's very fast.

---

## 🧠 Key Takeaways

- **"List all combinations" → think backtracking.**
- Build the answer **one piece at a time**. When it's complete, save it, then go back and try the next option.
- Recursion handles **any** input length. Nested loops only work for a fixed length.
- If you modify a shared path (array / `StringBuilder`), always **undo** after the recursive call: **choose → explore → undo**.
- Total results = **multiply** the choices at each step (3 × 3 = 9).

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
