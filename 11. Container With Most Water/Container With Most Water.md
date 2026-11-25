# 11. Container With Most Water

| Platform | Difficulty | Topic | Language | Status |
|----------|:----------:|-------|----------|:------:|
| [LeetCode](https://leetcode.com/problems/container-with-most-water/) | 🟡 Medium | Array, Two Pointers, Greedy | JavaScript | ✅ Accepted (0 ms) |
 
---

## 📝 Question 

You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `i`th line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return *the maximum amount of water a container can store*.

**Notice** that you may not slant the container.

### Example 1

```
  8 |    █              █
  7 |    █~~~~~~~~~~~~~~█~~~~~█
  6 |    █~~█~~~~~~~~~~~█~~~~~█
  5 |    █~~█~~~~~█~~~~~█~~~~~█
  4 |    █~~█~~~~~█~~█~~█~~~~~█
  3 |    █~~█~~~~~█~~█~~█~~█~~█
  2 |    █~~█~~█~~█~~█~~█~~█~~█
  1 | █  █~~█~~█~~█~~█~~█~~█~~█
    +---------------------------
      0  1  2  3  4  5  6  7  8
```

**Input:** `height = [1,8,6,2,5,4,8,3,7]`

**Output:** `49`

**Explanation:** The above vertical lines are represented by array `[1,8,6,2,5,4,8,3,7]`. In this case, the max area of water (the `~` section) the container can contain is `49`.

### Example 2

**Input:** `height = [1,1]`

**Output:** `1`

### Constraints

- `n == height.length`
- `2 <= n <= 10^5`
- `0 <= height[i] <= 10^4`

---

## 💡 What Is the Question Really Asking?

Pick **any two lines** and imagine pouring water between them. How much water stays in?

Two things decide it:

- **Width** = the distance between the two lines (`right - left`).
- **Height** = the **shorter** of the two lines. Water spills over the short side, so the tall line doesn't help beyond the short one.

> **Water = width × shorter line**

We need the pair of lines that holds the **most** water.

### The slow way

Try every possible pair. With 100,000 lines, that's about **5 billion** pairs. Way too slow.

### The smart way: Two Pointers 👈👉

1. Start with one finger on the **first** line and one on the **last** line. This gives the **widest** container possible.
2. Work out the water, and remember it if it's the best so far.
3. Now move one finger inward. **Which one? Always the shorter line.**
4. Repeat until the fingers meet.

**Why move the shorter line?** Every time we move a finger inward, the width shrinks by 1. The only way to get **more** water is to find a **taller** line.

- If we move the **taller** line, the water level is still capped by the short line that stayed, and the width got smaller. So it **can never get better**. Pointless.
- If we move the **shorter** line, we're throwing away the thing that was holding us back. We **might** find a taller line and get more water.

So moving the shorter line is the only move that has a chance to improve the answer.

---

## ✅ Solution (JavaScript)

```javascript
/**
 * @param {number[]} height
 * @return {number}
 */
var maxArea = function(height) {
    let left = 0;
    let right = height.length - 1;
    let maxWater = 0;

    while (left < right) {
        const width = right - left;
        const currentHeight = Math.min(height[left], height[right]);
        const currentArea = width * currentHeight;

        if (currentArea > maxWater) {
            maxWater = currentArea;
        }

        if (height[left] < height[right]) {
            left++;
        } else {
            right--;
        }
    }
    return maxWater;
};
```

### What Each Part Does

| Code | In simple words |
|------|-----------------|
| `let left = 0; let right = height.length - 1;` | Put one finger on the first line and one on the last line. |
| `let maxWater = 0;` | The best answer we've seen so far. Starts at 0. |
| `while (left < right)` | Keep going until the two fingers meet. |
| `const width = right - left;` | How far apart the two lines are. |
| `Math.min(height[left], height[right])` | The **shorter** line decides how high the water can go. |
| `const currentArea = width * currentHeight;` | How much water this pair holds. |
| `if (currentArea > maxWater)` | Is this the best so far? If yes, remember it. |
| `if (height[left] < height[right]) left++;` | The left line is shorter, so move it inward to look for a taller one. |
| `else right--;` | Otherwise the right line is shorter (or equal), so move that one inward. |
| `return maxWater;` | The biggest amount of water we found. |

---

## 🔍 Step-by-Step Dry Run

```
index:   0  1  2  3  4  5  6  7  8
height: [1, 8, 6, 2, 5, 4, 8, 3, 7]
```

**Start:** `left = 0`, `right = 8`, `maxWater = 0`

### Step 1: `left = 0`, `right = 8`

- Lines: height **1** (left) and height **7** (right)
- Width = 8 − 0 = **8**
- Shorter line = min(1, 7) = **1**
- Water = 8 × 1 = **8** → best so far = **8**
- Left (1) is shorter → move **left** to 1

### Step 2: `left = 1`, `right = 8`

- Lines: height **8** and height **7**
- Width = 8 − 1 = **7**
- Shorter line = min(8, 7) = **7**
- Water = 7 × 7 = **49** → new best = **49** 🎉
- Right (7) is shorter → move **right** to 7

### Step 3: `left = 1`, `right = 7`

- Lines: height **8** and height **3**
- Width = **6**, shorter = **3**
- Water = 6 × 3 = **18** → not better, best stays **49**
- Right (3) is shorter → move **right** to 6

### Step 4: `left = 1`, `right = 6`

- Lines: height **8** and height **8**
- Width = **5**, shorter = **8**
- Water = 5 × 8 = **40** → not better, best stays **49**
- Both equal → our code moves **right** to 5 (either one is fine)

### Step 5: `left = 1`, `right = 5`

- Lines: **8** and **4** → width **4**, shorter **4**
- Water = **16** → best stays **49**
- Move **right** to 4

### Step 6: `left = 1`, `right = 4`

- Lines: **8** and **5** → width **3**, shorter **5**
- Water = **15** → best stays **49**
- Move **right** to 3

### Step 7: `left = 1`, `right = 3`

- Lines: **8** and **2** → width **2**, shorter **2**
- Water = **4** → best stays **49**
- Move **right** to 2

### Step 8: `left = 1`, `right = 2`

- Lines: **8** and **6** → width **1**, shorter **6**
- Water = **6** → best stays **49**
- Move **right** to 1

### Stop

Now `left = 1` and `right = 1`, so the fingers have met and the loop ends.

### Quick Summary Table

| Step | left | right | Heights | Width | Shorter | Water | Best so far | Move |
|:----:|:----:|:-----:|:-------:|:-----:|:-------:|:-----:|:-----------:|------|
| 1 | 0 | 8 | 1, 7 | 8 | 1 | 8 | 8 | left → |
| 2 | 1 | 8 | 8, 7 | 7 | 7 | **49** | **49** | ← right |
| 3 | 1 | 7 | 8, 3 | 6 | 3 | 18 | 49 | ← right |
| 4 | 1 | 6 | 8, 8 | 5 | 8 | 40 | 49 | ← right |
| 5 | 1 | 5 | 8, 4 | 4 | 4 | 16 | 49 | ← right |
| 6 | 1 | 4 | 8, 5 | 3 | 5 | 15 | 49 | ← right |
| 7 | 1 | 3 | 8, 2 | 2 | 2 | 4 | 49 | ← right |
| 8 | 1 | 2 | 8, 6 | 1 | 6 | 6 | 49 | ← right |

**Answer: `49`** ✅ matches the expected output.

Only **8 checks** instead of the **36** pairs the slow way would try. For 100,000 lines, that's about 100,000 checks instead of 5 billion.

### Example 2: `height = [1, 1]`

- `left = 0`, `right = 1` → width **1**, shorter **1** → water **1**
- Fingers meet → stop.

**Answer: `1`** ✅

---

## ⚠️ Common Mistakes

**1. Using the taller line for the height**

```javascript
const currentHeight = Math.max(height[left], height[right]); // ❌ Wrong
```

Water spills over the **shorter** side. Always use `Math.min`.

**2. Moving the taller line**

Moving the taller line can never give more water (the short line still caps the height, and the width got smaller). You'll skip the real answer.

**3. Checking every pair with two nested loops**

```javascript
for (let i = 0; i < n; i++)
    for (let j = i + 1; j < n; j++)   // ❌ O(n²): Time Limit Exceeded
```

It gives the right answer on small inputs, but with `n = 100,000` it's far too slow.

**4. Forgetting that width is `right - left`, not `right - left + 1`**

The lines sit **on** positions 0, 1, 2…, so the distance between line 1 and line 8 is 7, not 8.

---

## 🔁 Other Ways to Write It

**Python:**

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        left, right = 0, len(height) - 1
        max_water = 0

        while left < right:
            width = right - left
            current_area = width * min(height[left], height[right])
            max_water = max(max_water, current_area)

            if height[left] < height[right]:
                left += 1
            else:
                right -= 1

        return max_water
```

**Java:**

```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0, right = height.length - 1;
        int maxWater = 0;

        while (left < right) {
            int width = right - left;
            int currentArea = width * Math.min(height[left], height[right]);
            maxWater = Math.max(maxWater, currentArea);

            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }
        return maxWater;
    }
}
```

**C++:**

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int left = 0, right = height.size() - 1;
        int maxWater = 0;

        while (left < right) {
            int width = right - left;
            int currentArea = width * min(height[left], height[right]);
            maxWater = max(maxWater, currentArea);

            if (height[left] < height[right]) {
                left++;
            } else {
                right--;
            }
        }
        return maxWater;
    }
};
```

> Only the JavaScript solution was submitted on LeetCode (accepted, 0 ms). The Python, Java and C++ versions were tested locally on both examples, and the Python version was also checked against a brute-force solution on 2,000 random inputs.

---

## ⏱️ Complexity

| | Value | Why |
|---|:-----:|-----|
| **Time** | `O(n)` | Each step moves one finger inward by 1, so there are at most `n − 1` steps. |
| **Space** | `O(1)` | Only a few variables (`left`, `right`, `maxWater`), no extra arrays. |

The max possible answer is `10^5 × 10^4 = 10^9`, which still fits in a normal 32-bit `int` (limit ≈ 2.1 × 10^9), so no overflow in Java or C++.

---

## 🧠 Key Takeaways

- **Water = width × shorter line.** The short side always decides the level.
- **Two pointers:** start at both ends (widest container), then move inward.
- **Always move the shorter line.** Moving the taller one can never help.
- This turns an `O(n²)` "try every pair" problem into a single `O(n)` pass.

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
