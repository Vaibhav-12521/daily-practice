# Java End-of-file

| Platform | Track | Difficulty | Topic | Language |
|----------|-------|:----------:|-------|----------|
| [HackerRank](https://www.hackerrank.com/challenges/java-end-of-file/problem) | Prepare › Java › Introduction | 🟢 Easy | Input Handling, Scanner, Loops | Java 15 |

---

## 📝 Question

> "In computing, End Of File (commonly abbreviated EOF) is a condition in a computer operating system where no more data can be read from a data source." ([Wikipedia: End-of-file](https://en.wikipedia.org/wiki/End-of-file))

The challenge here is to read `n` lines of input until you reach EOF, then number and print all `n` lines of content.

**Hint:** Java's `Scanner.hasNext()` method is helpful for this problem.

### Input Format

Read some unknown `n` lines of input from stdin (`System.in`) until you reach EOF; each line of input contains a non-empty String.

### Output Format

For each line, print the line number, followed by a single space, and then the line content received as input.

### Sample Input

```
Hello world
I am a file
Read me until end-of-file.
```

### Sample Output

```
1 Hello world
2 I am a file
3 Read me until end-of-file.
```

---

## 💡 What Is the Question Really Asking?

Imagine reading a notebook where **nobody tells you how many pages it has**. You just keep turning pages until there are none left, and write a page number next to each one as you go.

That's this problem:

> **"Keep reading lines until there are no more, and put a number in front of each one."**

The tricky part is that, unlike most problems, we are **not** told how many lines are coming. So we can't use a normal `for` loop that runs a fixed number of times. Instead, we keep asking Java **"is there another line?"** and stop when the answer is no. That moment, when the input runs out, is called **End Of File (EOF)**.

---

## ✅ Solution (Java)

```java
import java.io.*;
import java.util.*;

public class Solution {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int lineNumber = 1;
        while (scanner.hasNextLine()) {
            String line = scanner.nextLine();
            System.out.println(lineNumber + " " + line);
            lineNumber++;
        }
        scanner.close();
    }
}
```

### What Each Part Does

| Code | In simple words |
|------|-----------------|
| `Scanner scanner = new Scanner(System.in);` | Create a reader that reads what the user (or HackerRank) types in. |
| `int lineNumber = 1;` | Our page counter. The first line is line **1**, not 0. |
| `while (scanner.hasNextLine())` | Ask: **"Is there another line to read?"** If yes, go inside the loop. If no (EOF), stop. `hasNextLine()` only **checks**, it doesn't read anything. |
| `String line = scanner.nextLine();` | Now actually **read** the whole line, spaces included. |
| `System.out.println(lineNumber + " " + line);` | Print the number, one space, then the line. |
| `lineNumber++;` | Add 1 to the counter for the next line. |
| `scanner.close();` | We're done reading, so close the scanner. |

### Why `hasNextLine()` and not `hasNext()`?

The hint mentions `hasNext()`, but that one works **word by word**. Our lines have spaces in them (`Hello world`), so we need to read **whole lines**. `hasNextLine()` goes with `nextLine()`, and `hasNext()` goes with `next()`. Always use them as a matching pair.

---

## 🔍 Step-by-Step Dry Run

Input:

```
Hello world
I am a file
Read me until end-of-file.
```

**Start:** `lineNumber = 1`

### Round 1

- `hasNextLine()`: is there another line? **Yes** ✅
- `nextLine()` reads → `"Hello world"`
- Print → `1 Hello world`
- `lineNumber` goes from 1 to **2**

### Round 2

- `hasNextLine()`: is there another line? **Yes** ✅
- `nextLine()` reads → `"I am a file"`
- Print → `2 I am a file`
- `lineNumber` goes from 2 to **3**

### Round 3

- `hasNextLine()`: is there another line? **Yes** ✅
- `nextLine()` reads → `"Read me until end-of-file."`
- Print → `3 Read me until end-of-file.`
- `lineNumber` goes from 3 to **4**

### Round 4

- `hasNextLine()`: is there another line? **No** ❌, we've reached **EOF**.
- The loop stops. Close the scanner. Done!

### Quick Summary Table

| Round | `hasNextLine()` | Line read | Printed | `lineNumber` after |
|:-----:|:---------------:|-----------|---------|:------------------:|
| 1 | ✅ true | `Hello world` | `1 Hello world` | 2 |
| 2 | ✅ true | `I am a file` | `2 I am a file` | 3 |
| 3 | ✅ true | `Read me until end-of-file.` | `3 Read me until end-of-file.` | 4 |
| 4 | ❌ false (EOF) | — | — | stop |

### Final Output

```
1 Hello world
2 I am a file
3 Read me until end-of-file.
```

This matches the expected output. 🎉

---

## ⚠️ Common Mistakes

**1. Using `hasNext()` with `nextLine()`**

```java
while (scanner.hasNext()) {          // ❌ checks word by word
    String line = scanner.nextLine(); //    but reads whole lines
```

Mixing the pairs can give strange results, for example with blank lines at the end. Use `hasNextLine()` with `nextLine()`.

**2. Using `next()` instead of `nextLine()`**

`next()` stops at the first space, so `Hello world` would be printed as two separate lines: `1 Hello` and `2 world`. Wrong!

**3. Starting the counter at 0**

The output must start at `1`, so start `lineNumber` at 1 (or print `lineNumber + 1`).

**4. Trying to count the lines first**

There's no way to know how many lines are coming, and you don't need to. Just keep going until `hasNextLine()` says no.

---

## 🧪 Tested

| Input | Output |
|-------|--------|
| The sample (3 lines) | Matches the sample output exactly |
| `a b` then `c` with **no** newline at the very end | `1 a b` and `2 c`: the last line is still read correctly |

---

## ⏱️ Complexity

| | Value | Why |
|---|:-----:|-----|
| **Time** | `O(n)` | We read and print each of the `n` lines once. |
| **Space** | `O(1)` | Only one line is kept in memory at a time. |

---

## 🧠 Key Takeaways

- **EOF** = the input has run out. Use it when you don't know how many lines are coming.
- `while (scanner.hasNextLine())` is the standard way to "read until the end" in Java.
- `hasNextLine()` **checks**, `nextLine()` **reads**. Keep the pairs matched: `hasNextLine` ↔ `nextLine`, `hasNext` ↔ `next`.
- `nextLine()` reads the whole line including spaces. `next()` reads only one word.

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
