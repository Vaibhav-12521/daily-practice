# Java Datatypes

| Platform | Track | Difficulty | Topic | Language |
|----------|-------|:----------:|-------|----------|
| [HackerRank](https://www.hackerrank.com/challenges/java-datatypes/problem) | Prepare › Java › Introduction | 🟢 Easy | Primitive Data Types, Exception Handling | Java 15 |

---

## 📝 Question

Java has 8 primitive data types: `char`, `boolean`, `byte`, `short`, `int`, `long`, `float`, and `double`. For this exercise, we'll work with the primitives used to hold **integer** values (`byte`, `short`, `int`, and `long`):

- A `byte` is an 8-bit signed integer.
- A `short` is a 16-bit signed integer.
- An `int` is a 32-bit signed integer.
- A `long` is a 64-bit signed integer.

Given an input integer, you must determine which primitive data types are capable of properly storing that input.

**Reference:** [Oracle Docs: Primitive Data Types](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)

### Input Format

The first line contains an integer, `T`, denoting the number of test cases.

Each test case is comprised of a single line with an integer, `n`, which can be **arbitrarily large or small**.

### Output Format

For each input variable `n` and appropriate primitive `dataType`, you must determine if the given primitives are capable of storing it. If yes, then print:

```
n can be fitted in:
* dataType
```

If there is more than one appropriate data type, print each one on its own line and order them by size (i.e. `byte < short < int < long`).

If the number cannot be stored in one of the four aforementioned primitives, print the line:

```
n can't be fitted anywhere.
```

### Sample Input

```
5
-150
150000
1500000000
213333333333333333333333333333333333
-100000000000000
```

### Sample Output

```
-150 can be fitted in:
* short
* int
* long
150000 can be fitted in:
* int
* long
1500000000 can be fitted in:
* int
* long
213333333333333333333333333333333333 can't be fitted anywhere.
-100000000000000 can be fitted in:
* long
```

### Explanation

- `-150` can be stored in a `short`, an `int`, or a `long`.
- `213333333333333333333333333333333333` is very large and is outside of the allowable range of values for the primitive data types discussed in this problem.

---

## 💡 What Is the Question Really Asking?

Think of `byte`, `short`, `int` and `long` as **four boxes of different sizes**. For each number, we ask:

> **"Which of these boxes is big enough to hold this number?"**

Print every box that fits, smallest first. If the number is too big even for the biggest box (`long`), say it can't be fitted anywhere.

Here are the box sizes:

| Type | Size | Smallest value | Largest value | Roughly |
|------|:----:|---------------:|--------------:|---------|
| `byte` | 8 bits | -128 | 127 | ± 100 |
| `short` | 16 bits | -32,768 | 32,767 | ± 32 thousand |
| `int` | 32 bits | -2,147,483,648 | 2,147,483,647 | ± 2 billion |
| `long` | 64 bits | -9,223,372,036,854,775,808 | 9,223,372,036,854,775,807 | ± 9 quintillion |

The boxes are **nested**: if a number fits in a `short`, it also fits in an `int` and a `long`. So the answer is always "this box and every bigger one".

---

## ✅ Solution (Java)

```java
import java.io.*;
import java.util.*;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int t = sc.nextInt();

        for (int i = 0; i < t; i++) {
            try {
                long x = sc.nextLong();
                System.out.println(x + " can be fitted in:");
                if (x >= Byte.MIN_VALUE && x <= Byte.MAX_VALUE) {
                    System.out.println("* byte");
                }
                if (x >= Short.MIN_VALUE && x <= Short.MAX_VALUE) {
                    System.out.println("* short");
                }
                if (x >= Integer.MIN_VALUE && x <= Integer.MAX_VALUE) {
                    System.out.println("* int");
                }
                if (x >= Long.MIN_VALUE && x <= Long.MAX_VALUE) {
                    System.out.println("* long");
                }
            } catch (Exception e) {
                System.out.println(sc.next() + " can't be fitted anywhere.");
            }
        }
        sc.close();
    }
}
```

### What Each Part Does
 
| Code | In simple words |
|------|-----------------|
| `int t = sc.nextInt();` | Read how many numbers we need to check. |
| `long x = sc.nextLong();` | Try to read the number as a `long`, the **biggest box**. If it's too big even for a `long`, Java throws an error here and we jump straight to `catch`. |
| `System.out.println(x + " can be fitted in:");` | We only reach this line if reading worked, so the number fits in **at least** a `long`. |
| `x >= Byte.MIN_VALUE && x <= Byte.MAX_VALUE` | Is the number between -128 and 127? Then it fits in a `byte`. Java gives us these limits for free, so we don't have to remember them. |
| Same check for `Short`, `Integer`, `Long` | Check each bigger box, in order from small to big, and print every one that fits. |
| `catch (Exception e)` | The number was too big for a `long`, so `nextLong()` failed. |
| `sc.next()` | `nextLong()` failed but **didn't eat** the number, it's still waiting in the input. `sc.next()` reads it as plain text so we can print it. |

---

## 🔍 Step-by-Step Dry Run

Input:

```
5
-150
150000
1500000000
213333333333333333333333333333333333
-100000000000000
```

First, `t = 5`, so the loop runs 5 times.

### Number 1: `-150`

- `nextLong()` reads `-150` easily. ✅ Print `-150 can be fitted in:`
- **byte?** Is -150 between -128 and 127? No, it's smaller than -128. ❌
- **short?** Is -150 between -32,768 and 32,767? Yes. ✅ Print `* short`
- **int?** Yes, obviously. ✅ Print `* int`
- **long?** Yes. ✅ Print `* long`

### Number 2: `150000`

- `nextLong()` reads it. ✅ Print `150000 can be fitted in:`
- **byte?** 150,000 is way more than 127. ❌
- **short?** 150,000 is more than 32,767. ❌
- **int?** 150,000 is less than about 2 billion. ✅ Print `* int`
- **long?** ✅ Print `* long`

### Number 3: `1500000000`

- `nextLong()` reads it. ✅ Print `1500000000 can be fitted in:`
- **byte?** ❌ &nbsp; **short?** ❌
- **int?** 1.5 billion is less than 2,147,483,647 (about 2.1 billion). Just fits! ✅ Print `* int`
- **long?** ✅ Print `* long`

### Number 4: `213333333333333333333333333333333333`

- `nextLong()` tries to read it, but this number has **36 digits**. The biggest `long` has only 19 digits.
- Java throws an error 💥, so we skip all the `if` checks and jump to `catch`.
- `sc.next()` reads the number as text: `"213333333333333333333333333333333333"`
- Print `213333333333333333333333333333333333 can't be fitted anywhere.`

### Number 5: `-100000000000000`

- `nextLong()` reads it (-100 trillion, still well inside a `long`). ✅ Print `-100000000000000 can be fitted in:`
- **byte?** ❌ &nbsp; **short?** ❌
- **int?** -100 trillion is far below -2 billion. ❌
- **long?** ✅ Print `* long`

### Final Output

```
-150 can be fitted in:
* short
* int
* long
150000 can be fitted in:
* int
* long
1500000000 can be fitted in:
* int
* long
213333333333333333333333333333333333 can't be fitted anywhere.
-100000000000000 can be fitted in:
* long
```

This matches the expected output. 🎉

### Quick Summary Table

| Number | byte | short | int | long | Result |
|--------|:----:|:-----:|:---:|:----:|--------|
| `-150` | ❌ | ✅ | ✅ | ✅ | short, int, long |
| `150000` | ❌ | ❌ | ✅ | ✅ | int, long |
| `1500000000` | ❌ | ❌ | ✅ | ✅ | int, long |
| `2133...333` (36 digits) | ❌ | ❌ | ❌ | ❌ | can't be fitted anywhere |
| `-100000000000000` | ❌ | ❌ | ❌ | ✅ | long |

---

## ⚠️ Common Mistakes

**1. Reading the number as an `int`**

```java
int x = sc.nextInt();   // ❌ Wrong
```

Then any number bigger than 2 billion crashes, even though it fits in a `long`. Always read with `nextLong()`, the **biggest** type, and then check the smaller ones.

**2. Forgetting `sc.next()` in the `catch` block**

When `nextLong()` fails, the big number **stays in the input**. If you don't read it with `sc.next()`, the next loop tries to read the same number again and fails again, and every remaining answer goes wrong.

**3. Hard-coding the limits**

Writing `x >= -128 && x <= 127` works, but it's easy to make a typo with numbers like `-9223372036854775808`. Use Java's built-in `Byte.MIN_VALUE`, `Short.MAX_VALUE`, etc. instead.

---

## 🧪 Edge Cases (tested)

| Input | Output | Why |
|-------|--------|-----|
| `127` | byte, short, int, long | 127 is the **largest** byte, so it still fits. |
| `-129` | short, int, long | One below the smallest byte (-128). |
| `9223372036854775807` | long | The **largest** possible `long`. |
| `9223372036854775808` | can't be fitted anywhere | Just **one** more than the largest `long`. |

---

## 🧠 Key Takeaways

- Java's integer types are boxes of increasing size: `byte` (8-bit) < `short` (16-bit) < `int` (32-bit) < `long` (64-bit).
- Every wrapper class gives you its limits: `Byte.MIN_VALUE`, `Short.MAX_VALUE`, `Integer.MAX_VALUE`, `Long.MIN_VALUE`, and so on.
- Read input into the **biggest** type first, then check the smaller ones.
- `try / catch` lets your program handle bad input without crashing.
- If a `Scanner` read fails, the input is **not consumed**. Read it again with `next()`.

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
