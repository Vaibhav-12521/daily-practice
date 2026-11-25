# 12. Integer to Roman

| Platform | Difficulty | Topic | Language | Status |
|----------|:----------:|-------|----------|:------:|
| [LeetCode](https://leetcode.com/problems/integer-to-roman/) | 🟡 Medium | Hash Table, Math, String, Greedy | JavaScript | ✅ Accepted (0 ms) |

---

## 📝 Question

Seven different symbols represent Roman numerals with the following values:  

| Symbol | Value |
|:------:|------:|
| I | 1 |
| V | 5 |
| X | 10 |
| L | 50 |
| C | 100 |
| D | 500 |
| M | 1000 |

Roman numerals are formed by appending the conversions of decimal place values from highest to lowest. Converting a decimal place value into a Roman numeral has the following rules:

- If the value does not start with 4 or 9, select the symbol of the maximal value that can be subtracted from the input, append that symbol to the result, subtract its value, and convert the remainder to a Roman numeral.
- If the value starts with 4 or 9 use the **subtractive form** representing one symbol subtracted from the following symbol, for example, 4 is 1 (`I`) less than 5 (`V`): `IV` and 9 is 1 (`I`) less than 10 (`X`): `IX`. Only the following subtractive forms are used: 4 (`IV`), 9 (`IX`), 40 (`XL`), 90 (`XC`), 400 (`CD`) and 900 (`CM`).
- Only powers of 10 (`I`, `X`, `C`, `M`) can be appended consecutively at most 3 times to represent multiples of 10. You cannot append 5 (`V`), 50 (`L`), or 500 (`D`) multiple times. If you need to append a symbol 4 times use the **subtractive form**.

Given an integer, convert it to a Roman numeral.

### Example 1

**Input:** `num = 3749`

**Output:** `"MMMDCCXLIX"`

**Explanation:**

```
3000 = MMM as 1000 (M) + 1000 (M) + 1000 (M)
 700 = DCC as 500 (D) + 100 (C) + 100 (C)
  40 = XL as 10 (X) less of 50 (L)
   9 = IX as 1 (I) less of 10 (X)
```

Note: 49 is not 1 (`I`) less of 50 (`L`) because the conversion is based on decimal places.

### Example 2

**Input:** `num = 58`

**Output:** `"LVIII"`

**Explanation:**

```
50 = L
 8 = VIII
```

### Example 3

**Input:** `num = 1994`

**Output:** `"MCMXCIV"`

**Explanation:**

```
1000 = M
 900 = CM
  90 = XC
   4 = IV
```

### Constraints

- `1 <= num <= 3999`

--- 

## 💡 What Is the Question Really Asking?

Imagine you're paying an amount using **the biggest notes first**, like giving change at a shop.

To pay 3749, you'd hand over as many 1000s as you can, then 500s, then 100s, and so on, until nothing is left. Roman numerals work exactly the same way.

The only twist is the special "4 and 9" cases (`IV`, `IX`, `XL`, `XC`, `CD`, `CM`). The easy trick: **treat them as extra notes** of their own. Then our list of "notes" becomes:

| Value | 1000 | 900 | 500 | 400 | 100 | 90 | 50 | 40 | 10 | 9 | 5 | 4 | 1 |
|-------|:----:|:---:|:---:|:---:|:---:|:--:|:--:|:--:|:--:|:-:|:-:|:-:|:-:|
| Symbol | M | CM | D | CD | C | XC | L | XL | X | IX | V | IV | I |

Now we never have to think about rules. Just keep taking the **biggest value that still fits**, and the 4s and 9s come out right automatically. This "always take the biggest one that fits" idea is called a **greedy** approach.

---

## ✅ Solution (JavaScript)

```javascript
/**
 * @param {number} num
 * @return {string}
 */
var intToRoman = function(num) {
    const valueSymbols = [
        [1000, 'M'],
        [900, 'CM'],
        [500, 'D'],
        [400, 'CD'],
        [100, 'C'],
        [90, 'XC'],
        [50, 'L'],
        [40, 'XL'],
        [10, 'X'],
        [9, 'IX'],
        [5, 'V'],
        [4, 'IV'],
        [1, 'I']
    ];

    let result = '';
    for (const [value, symbol] of valueSymbols) {
        while (num >= value) {
            result += symbol;
            num -= value;
        }
    }
    return result;
};
```

### What Each Part Does

| Code | In simple words |
|------|-----------------|
| `const valueSymbols = [...]` | Our list of "notes", from **biggest to smallest**, including the special 4s and 9s. The order matters: we must always try the bigger ones first. |
| `let result = '';` | Start with an empty answer. |
| `for (const [value, symbol] of valueSymbols)` | Go through the list one pair at a time, e.g. `1000` with `'M'`, then `900` with `'CM'`… |
| `while (num >= value)` | As long as this value still **fits** into what's left of the number… |
| `result += symbol;` | …add its symbol to the answer… |
| `num -= value;` | …and take that value away from the number. |
| `return result;` | When the number reaches 0, the answer is complete. |

We use `while` (not `if`) because a symbol can repeat, like the three `M`s in `MMM`.

---

## 🔍 Step-by-Step Dry Run

### Example: `num = 3749`

We walk down the list. At each value, we keep taking it while it fits.

| Step | Value (Symbol) | Does it fit in what's left? | Action | Number left | Result so far |
|:----:|----------------|-----------------------------|--------|:-----------:|---------------|
| 1 | 1000 (M) | 3749 ≥ 1000 ✅ | add `M`, subtract 1000 | 2749 | `M` |
| 2 | 1000 (M) | 2749 ≥ 1000 ✅ | add `M`, subtract 1000 | 1749 | `MM` |
| 3 | 1000 (M) | 1749 ≥ 1000 ✅ | add `M`, subtract 1000 | 749 | `MMM` |
| 4 | 1000 (M) | 749 ≥ 1000 ❌ | move on | 749 | `MMM` |
| 5 | 900 (CM) | 749 ≥ 900 ❌ | move on | 749 | `MMM` |
| 6 | 500 (D) | 749 ≥ 500 ✅ | add `D`, subtract 500 | 249 | `MMMD` |
| 7 | 400 (CD) | 249 ≥ 400 ❌ | move on | 249 | `MMMD` |
| 8 | 100 (C) | 249 ≥ 100 ✅ | add `C`, subtract 100 | 149 | `MMMDC` |
| 9 | 100 (C) | 149 ≥ 100 ✅ | add `C`, subtract 100 | 49 | `MMMDCC` |
| 10 | 90 (XC) | 49 ≥ 90 ❌ | move on | 49 | `MMMDCC` |
| 11 | 50 (L) | 49 ≥ 50 ❌ | move on | 49 | `MMMDCC` |
| 12 | 40 (XL) | 49 ≥ 40 ✅ | add `XL`, subtract 40 | 9 | `MMMDCCXL` |
| 13 | 10 (X) | 9 ≥ 10 ❌ | move on | 9 | `MMMDCCXL` |
| 14 | 9 (IX) | 9 ≥ 9 ✅ | add `IX`, subtract 9 | 0 | `MMMDCCXLIX` |
| 15 | 5, 4, 1 | 0 left, nothing fits | done | 0 | `MMMDCCXLIX` |

**Answer: `"MMMDCCXLIX"`** ✅ matches the expected output.

Notice step 11 → 12: when 49 was left, `L` (50) was too big, so we used `XL` (40). We **never** wrote `IL` for 49, because `IL` isn't in our list. That's exactly what the note in Example 1 is warning about, and our list handles it for free.

### Example: `num = 1994` (lots of special cases)

| Value (Symbol) | What happens | Number left | Result so far |
|----------------|--------------|:-----------:|---------------|
| 1000 (M) | fits once | 994 | `M` |
| 900 (CM) | fits once | 94 | `MCM` |
| 500, 400, 100 | too big, skip | 94 | `MCM` |
| 90 (XC) | fits once | 4 | `MCMXC` |
| 50, 40, 10, 9, 5 | too big, skip | 4 | `MCMXC` |
| 4 (IV) | fits once | 0 | `MCMXCIV` |

**Answer: `"MCMXCIV"`** ✅

### Example: `num = 58`

| Value (Symbol) | What happens | Number left | Result so far |
|----------------|--------------|:-----------:|---------------|
| 1000 … 90 | too big, skip | 58 | |
| 50 (L) | fits once | 8 | `L` |
| 40, 10, 9 | too big, skip | 8 | `L` |
| 5 (V) | fits once | 3 | `LV` |
| 4 (IV) | too big, skip | 3 | `LV` |
| 1 (I) | fits three times | 0 | `LVIII` |

**Answer: `"LVIII"`** ✅

---

## ⚠️ Common Mistakes

**1. Forgetting the special 4s and 9s in the list**

If your list only has `M, D, C, L, X, V, I`, then 4 becomes `IIII` and 9 becomes `VIIII`, which are wrong. Adding `CM, CD, XC, XL, IX, IV` to the list fixes everything without any extra `if` statements.

**2. Putting the list in the wrong order**

The list **must** go from biggest to smallest. If `1` came before `1000`, you'd get 3749 `I`s!

**3. Using `if` instead of `while`**

With `if`, each symbol could be used only once, so 3000 would give just `M` instead of `MMM`.

---

## 🔁 Other Ways to Write It

**Python** (using `divmod` to take each value as many times as it fits, in one go):

```python
class Solution:
    def intToRoman(self, num: int) -> str:
        value_symbols = [
            (1000, "M"), (900, "CM"), (500, "D"), (400, "CD"),
            (100, "C"), (90, "XC"), (50, "L"), (40, "XL"),
            (10, "X"), (9, "IX"), (5, "V"), (4, "IV"), (1, "I"),
        ]
        result = []
        for value, symbol in value_symbols:
            count, num = divmod(num, value)  # how many times it fits, and what's left
            result.append(symbol * count)
        return "".join(result)
```

**Java:**

```java
class Solution {
    public String intToRoman(int num) {
        int[] values = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        String[] symbols = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};

        StringBuilder result = new StringBuilder();
        for (int i = 0; i < values.length; i++) {
            while (num >= values[i]) {
                result.append(symbols[i]);
                num -= values[i];
            }
        }
        return result.toString();
    }
}
```

**C++:**

```cpp
class Solution {
public:
    string intToRoman(int num) {
        vector<pair<int, string>> valueSymbols = {
            {1000, "M"}, {900, "CM"}, {500, "D"}, {400, "CD"},
            {100, "C"},  {90, "XC"},  {50, "L"},  {40, "XL"},
            {10, "X"},   {9, "IX"},   {5, "V"},   {4, "IV"}, {1, "I"}
        };

        string result;
        for (auto& p : valueSymbols) {
            while (num >= p.first) {
                result += p.second;
                num -= p.first;
            }
        }
        return result;
    }
};
```

> Only the JavaScript solution was submitted on LeetCode (accepted, 0 ms). The Python, Java and C++ versions were tested locally on all three examples.

---

## ⏱️ Complexity

| | Value | Why |
|---|:-----:|-----|
| **Time** | `O(1)` | The list always has 13 entries, and since `num ≤ 3999` the answer is at most 15 characters long (`MMMDCCCLXXXVIII` = 3888). The work never grows. |
| **Space** | `O(1)` | Just the fixed list of 13 pairs and the answer string. |

---

## 🧠 Key Takeaways

- **Greedy works here:** always take the biggest value that still fits.
- Turn tricky rules into **data**: adding `CM, CD, XC, XL, IX, IV` to the list removes all the special-case `if`s.
- Order matters: biggest to smallest.
- Use `while` when something can repeat, `if` when it can happen only once.

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
