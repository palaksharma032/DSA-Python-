# 704. Binary Search

![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen)
![Topic](https://img.shields.io/badge/Topic-Binary%20Search-orange)
![Pattern](https://img.shields.io/badge/Pattern-Classic%20Binary%20Search-blue)
![Language](https://img.shields.io/badge/Language-Python-yellow)

🔗 [LeetCode Problem](https://leetcode.com/problems/binary-search/)

---

## 📖 Problem in Simple Words

You are given an array of integers called `nums`. The array is already sorted in **ascending order**, and you are also given an integer called `target`.

Your task is to find the position (index) of the target in the array.

- If the target exists in the array, return its **index**.
- If the target does not exist, return **-1**.

There is one important condition: your solution must have a time complexity of **O(log n)**.

A normal left-to-right search takes O(n) time. Since the problem specifically requires O(log n), we need a faster approach: **Binary Search**.

### Examples

| Input | Output | Explanation |
|---|---|---|
| `nums = [-1, 0, 3, 5, 9, 12]`, `target = 9` | `4` | The target 9 is present at index 4. |
| `nums = [-1, 0, 3, 5, 9, 12]`, `target = 2` | `-1` | The target 2 is not present in the array. |

### Constraints (Important)

- `1 <= nums.length <= 10⁴`
- `nums` is sorted in **ascending order**.
- All integers in `nums` are **unique**.
- The target can be present or absent.

### Important Observation

Two clues in the problem immediately suggest Binary Search:

1. The array is **sorted**.
2. The required time complexity is **O(log n)**.

Whenever you see a sorted array and a requirement to search efficiently, consider Binary Search.

> 💡 Interview tip: Before writing code, explain why the sorted property allows you to eliminate half of the search space after every comparison.

---

## 🧠 Intuition (Easy Language)

Imagine searching for a word in a dictionary.

Suppose you want to find the word **"Python"**.

Would you start reading every page from the beginning?

No. You would open the dictionary somewhere in the middle.

- If the word on that page comes alphabetically before "Python", you know your word must be on the right side.
- If the word comes after "Python", you know your word must be on the left side.
- If the word is exactly "Python", you have found it.

You completely ignore the half that cannot contain your answer.

This is exactly how **Binary Search** works.

Because the array is sorted, comparing the target with the middle element tells us which half can be discarded.

### How Binary Search Works

Suppose we have:

```python
nums = [-1, 0, 3, 5, 9, 12]
target = 9
```

The array has six elements.

**Step 1: Find the middle element**

The middle index is:

```python
mid = (0 + 5) // 2
mid = 2
```

The element at index 2 is:

```python
nums[2] = 3
```

**Step 2: Compare the middle element with the target**

```python
3 < 9
```

Since 3 is smaller than 9, the target must be on the right side.

We can safely discard the left half, including index 2.

**Step 3: Search the remaining right half**

Now our search range contains:

```python
[5, 9, 12]
```

The new middle index is:

```python
mid = (3 + 5) // 2
mid = 4
```

And:

```python
nums[4] = 9
```

We found the target!

Therefore, the answer is:

```python
4
```

### Why Is Binary Search So Fast?

The biggest advantage of Binary Search is that it eliminates approximately half of the remaining elements after every comparison.

The search space keeps shrinking:

```text
n → n/2 → n/4 → n/8 → ... → 1
```

For example:

| Number of elements | Linear Search (worst case) | Binary Search (approximately) |
|---:|---:|---:|
| 1,000 | 1,000 checks | 10 checks |
| 1,000,000 | 1,000,000 checks | 20 checks |
| 1,000,000,000 | 1,000,000,000 checks | 30 checks |

This is why Binary Search has a time complexity of:

**O(log₂ n)**, commonly written as **O(log n)**.

The logarithm tells us how many times we can divide the search space by two before reaching one element.

---

## 🛠️ Approach

We will use three variables:

| Variable | Meaning |
|---|---|
| `lo` | Left boundary of the current search range |
| `hi` | Right boundary of the current search range |
| `mid` | Middle index of the current search range |

Initially, the search range covers the entire array.

```python
lo = 0
hi = len(nums) - 1
```

We use:

```python
while lo <= hi:
```

This means we continue searching as long as at least one element remains in the search range.

### Step-by-Step Algorithm

**Step 1: Initialize the boundaries**

```python
lo = 0
hi = len(nums) - 1
```

- `lo` starts at index 0.
- `hi` starts at the last valid index.

**Step 2: Find the middle index**

```python
mid = lo + (hi - lo) // 2
```

This calculates the middle index of the current search range.

**Step 3: Compare the middle element with the target**

There are three possible situations:

**Case 1: Target found**

```python
if nums[mid] == target:
    return mid
```

If the middle element equals the target, return its index immediately.

**Case 2: Target is greater than the middle element**

```python
elif nums[mid] < target:
    lo = mid + 1
```

Because the array is sorted in ascending order, every element to the left of `mid` is smaller than or equal to `nums[mid]`.

The target must be on the right side.

So we update `lo` to `mid + 1`.

**Case 3: Target is smaller than the middle element**

```python
else:
    hi = mid - 1
```

The target must be on the left side.

So we update `hi` to `mid - 1`.

**Step 4: Return -1 if the target is not found**

```python
return -1
```

If the loop finishes, `lo` has crossed `hi`. This means there are no remaining elements to search.

Therefore, the target does not exist in the array.

### Why `mid + 1` and `mid - 1` Instead of Just `mid`?

This is one of the most important concepts in Binary Search.

Suppose:

```python
lo = 0
hi = 1
mid = 0
```

If we write:

```python
lo = mid
```

Then `lo` remains 0.

The search range does not become smaller, and the same middle index may be calculated repeatedly.

This can cause an **infinite loop**.

We use:

```python
lo = mid + 1
```

and:

```python
hi = mid - 1
```

because we have already checked the middle element and confirmed that it is not the target.

There is no reason to search it again.

### Why `while lo <= hi` Instead of `while lo < hi`?

Consider:

```python
nums = [5]
target = 5
```

Initially:

```python
lo = 0
hi = 0
```

If we use:

```python
while lo < hi:
```

The condition becomes:

```python
0 < 0
```

This is false.

The loop never executes, and the algorithm incorrectly returns `-1`.

But with:

```python
while lo <= hi:
```

The condition is true, so the remaining element is checked correctly.

**Remember:**

- `lo <= hi` is used in this inclusive-boundary template.
- `lo < hi` is also used in other Binary Search patterns, such as some lower-bound problems, but the update rules must be designed accordingly.

### Why `mid = lo + (hi - lo) // 2`?

You may also see this formula:

```python
mid = (lo + hi) // 2
```

Both formulas calculate the middle index.

However:

```python
mid = lo + (hi - lo) // 2
```

is preferred in languages such as C++ and Java because adding two very large indices can cause integer overflow.

Python integers can grow beyond the usual fixed integer limits, so overflow is not normally a concern here. Still, learning the safer formula is useful when working across programming languages.

---

## 🔍 Dry Run

A dry run means executing the algorithm manually, step by step, to understand how every variable changes.

Let's understand both situations: when the target exists and when it does not.

### Case 1: Target Found

**Input:**

```python
nums = [-1, 0, 3, 5, 9, 12]
target = 9
```

Initial values:

```python
lo = 0
hi = 5
```

| Step | `lo` | `hi` | `mid` | `nums[mid]` | Decision |
|---|---:|---:|---:|---:|---|
| 1 | 0 | 5 | 2 | 3 | `3 < 9`, so `lo = mid + 1 = 3` |
| 2 | 3 | 5 | 4 | 9 | Target found! Return `4` |

**Final Output:**

```python
4
```

The target 9 is present at index 4.

### Case 2: Target Not Found

**Input:**

```python
nums = [-1, 0, 3, 5, 9, 12]
target = 2
```

Initial values:

```python
lo = 0
hi = 5
```

| Step | `lo` | `hi` | `mid` | `nums[mid]` | Decision |
|---|---:|---:|---:|---:|---|
| 1 | 0 | 5 | 2 | 3 | `3 > 2`, so `hi = mid - 1 = 1` |
| 2 | 0 | 1 | 0 | -1 | `-1 < 2`, so `lo = mid + 1 = 1` |
| 3 | 1 | 1 | 1 | 0 | `0 < 2`, so `lo = mid + 1 = 2` |
| 4 | 2 | 1 | — | — | `lo > hi`, so stop searching |

Now:

```python
lo = 2
hi = 1
```

Since `lo > hi`, the search range is empty.

The target is not present.

**Final Output:**

```python
-1
```

### What Should You Notice?

In both examples:

1. We always calculate the middle index.
2. We compare the middle element with the target.
3. We eliminate the half that cannot contain the target.
4. We repeat until the target is found or the search range becomes empty.

This is the central idea of Binary Search.

---

## 💻 Solution (Python)

### Iterative Solution (Preferred)

An iterative solution uses a loop to repeatedly reduce the search range.

```python
class Solution:
    def search(self, nums: list[int], target: int) -> int:
        lo, hi = 0, len(nums) - 1

        while lo <= hi:
            mid = lo + (hi - lo) // 2

            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                lo = mid + 1
            else:
                hi = mid - 1

        return -1
```

### Understanding the Code

- `lo, hi = 0, len(nums) - 1`: Initialize the search range.
- `while lo <= hi`: Continue while the search range is not empty.
- `mid = lo + (hi - lo) // 2`: Calculate the middle index.
- `nums[mid] == target`: If true, return the index.
- `nums[mid] < target`: Search the right half.
- Otherwise: Search the left half.
- `return -1`: The target was not found.

**Why is this solution preferred?**

- It is easy to understand and implement.
- It uses constant extra space.
- It avoids recursive function calls.
- It is an excellent template for solving other Binary Search problems.

### Recursive Solution (For Understanding)

Binary Search can also be implemented using recursion.

Recursion means a function calls itself to solve a smaller version of the same problem.

Instead of using a loop, we call a helper function with the updated search boundaries.

```python
class Solution:
    def search(self, nums: list[int], target: int) -> int:
        def helper(lo: int, hi: int) -> int:
            if lo > hi:
                return -1

            mid = lo + (hi - lo) // 2

            if nums[mid] == target:
                return mid

            if nums[mid] < target:
                return helper(mid + 1, hi)

            return helper(lo, mid - 1)

        return helper(0, len(nums) - 1)
```

### Understanding the Recursive Code

**1. Base condition**

```python
if lo > hi:
    return -1
```

When the left boundary crosses the right boundary, there are no elements left to search.

We return `-1`.

**2. Calculate the middle**

```python
mid = lo + (hi - lo) // 2
```

Find the middle index of the current search range.

**3. Check whether the target is found**

```python
if nums[mid] == target:
    return mid
```

If the middle element is the target, return its index.

**4. Search the right half**

```python
if nums[mid] < target:
    return helper(mid + 1, hi)
```

If the middle element is smaller than the target, recursively search the right half.

**5. Search the left half**

```python
return helper(lo, mid - 1)
```

Otherwise, recursively search the left half.

### Iterative vs Recursive

| Feature | Iterative | Recursive |
|---|---|---|
| Technique | Loop | Function calls itself |
| Time Complexity | O(log n) | O(log n) |
| Space Complexity | O(1) | O(log n) |
| Extra memory | Constant variables | Function call stack |
| Preferred for interviews | Usually yes | Useful for understanding recursion |

**Recommendation:** Master the iterative solution first. Understand the recursive version as an alternative implementation.

---

## ⏱️ Complexity

Time and space complexity explain how the algorithm's performance changes as the input size increases.

### Time Complexity: O(log n)

Binary Search eliminates approximately half of the remaining elements after each iteration.

For example, if the array contains 16 elements:

```text
16 → 8 → 4 → 2 → 1
```

Only a few comparisons are needed.

The number of steps grows logarithmically with the input size.

Therefore:

**Time Complexity = O(log n)**

### Space Complexity: O(1) for Iterative Solution

The iterative solution uses only a fixed number of variables:

```python
lo
hi
mid
```

The amount of extra memory does not increase with the size of the input array.

Therefore:

**Space Complexity = O(1)**

### Recursive Space Complexity: O(log n)

The recursive solution creates a new function call for each search step.

Since the search space is halved each time, the maximum recursion depth is logarithmic.

Therefore:

**Space Complexity = O(log n)**

### Complexity Comparison

| Version | Time Complexity | Space Complexity |
|---|---|---|
| Iterative | O(log n) | O(1) |
| Recursive | O(log n) | O(log n) |

---

## 🧩 Pattern: Classic Binary Search

LeetCode 704 is the foundation of the Binary Search pattern.

Once you understand this problem properly, many other Binary Search problems become easier because they use the same central idea:

**Reduce the search space by eliminating the half that cannot contain the answer.**

However, not every Binary Search problem asks you to find an exact element. Some ask you to find a boundary, minimum possible answer, maximum possible answer, or a position.

### Important Binary Search Variations

| Variation | Example Problems | What Changes |
|---|---|---|
| Find insert position / first element greater than or equal to target | 35, 34 | Search for a boundary instead of an exact match |
| Binary Search on Answer | 69, 278, 875, 1011 | Search over possible answers rather than array indices |
| Rotated Sorted Array | 33, 153 | Identify which half is sorted |
| Peak Finding | 162 | Compare the middle element with its neighbours |
| 2D Matrix Search | 74 | Use the sorted structure of a matrix |

### Pattern Recognition Checklist

Before applying Binary Search, ask yourself:

- Is the input sorted?
- Does the problem have a monotonic property? (For example, once a condition becomes true, does it remain true?)
- Can I eliminate half of the possible search space after each comparison?
- Is the problem asking me to find a value, position, boundary, minimum, or maximum?
- Does the problem require O(log n) time?

If these clues are present, Binary Search may be the right approach.

### Important Concept: Binary Search Is Not Limited to Arrays

Binary Search can also work on a range of possible answers.

For example, imagine finding the minimum speed required to finish a task within a given time.

If a particular speed is sufficient, every higher speed may also be sufficient.

This creates a monotonic condition that can be searched using Binary Search.

This idea is called **Binary Search on Answer** and appears in problems such as LeetCode 875 and 1011.

---

## ❓ Why Not Other Approaches?

There are different ways to search for an element, but not all of them are suitable for this problem.

| Approach | Time Complexity | Verdict |
|---|---|---|
| Linear Search (`for` loop) | O(n) | Correct, but does not meet the O(log n) requirement |
| `target in nums` | O(n) | Checks elements linearly for a Python list |
| `nums.index(target)` | O(n) | Also performs a linear search |
| Hash Set / Dictionary | O(n) to build, O(1) average lookup | Requires extra memory and does not directly provide the original index |
| **Binary Search** | **O(log n)** | Uses the sorted property and requires O(1) extra space iteratively |

### Why Is Linear Search Not Suitable?

A linear search checks each element one by one.

For example:

```python
nums = [-1, 0, 3, 5, 9, 12]
target = 12
```

It may need to check every element before finding the target.

In the worst case, it performs `n` comparisons.

Its time complexity is:

```text
O(n)
```

### Why Not Use a Hash Set?

A hash set can provide O(1) average-time membership checking after construction.

However:

- Building the set takes O(n) time.
- It requires O(n) additional space.
- It does not directly preserve the original array indices.
- It ignores the fact that the array is already sorted.

Binary Search is a better fit because it uses the existing sorted order without creating another data structure.

---

## 🧪 Edge Cases

An edge case is an input that may behave differently from a normal example.

Testing edge cases helps ensure that the solution works correctly for different situations.

| Case | Example | Expected Output |
|---|---|---:|
| Single element, target found | `[5], target = 5` | `0` |
| Single element, target missing | `[5], target = 3` | `-1` |
| Target is the first element | `[1, 2, 3], target = 1` | `0` |
| Target is the last element | `[1, 2, 3], target = 3` | `2` |
| Target smaller than all elements | `[1, 2, 3], target = 0` | `-1` |
| Target greater than all elements | `[1, 2, 3], target = 9` | `-1` |
| Negative numbers | `[-5, -2, 0], target = -2` | `1` |
| Target is in the middle | `[1, 2, 3, 4, 5], target = 3` | `2` |

### Why Should You Test These Cases?

- A single-element array checks whether your loop condition is correct.
- First and last elements check whether the boundaries are handled correctly.
- A missing target checks whether the algorithm terminates and returns `-1`.
- Negative numbers ensure that comparisons work correctly with negative values.

**Interview tip:** Always test at least one successful search, one unsuccessful search, and a boundary case before finalizing your solution.

---

## 🎤 Interview Questions & Answers

These questions cover the main concepts an interviewer may ask after you implement Binary Search.

### Q1. Why does Binary Search require a sorted array?

Binary Search needs sorted data because it uses the middle element to decide which half can be discarded.

For an ascending array:

- If `nums[mid] < target`, the target can only be on the right.
- If `nums[mid] > target`, the target can only be on the left.

In an unsorted array, these comparisons cannot tell us which half contains the target.

### Q2. What is the time complexity of Binary Search, and why?

The time complexity is **O(log n)**.

After every comparison, the search space is reduced to approximately half its previous size.

For an array of `n` elements, the maximum number of iterations is approximately:

```text
log₂(n) + 1
```

Therefore, the time complexity is O(log n).

### Q3. Why do we use `lo + (hi - lo) // 2` instead of `(lo + hi) // 2`?

Both formulas calculate the middle index.

However, in languages such as C++ and Java, adding two very large indices can cause integer overflow.

The safer formula is:

```python
mid = lo + (hi - lo) // 2
```

Python integers do not normally have this fixed-width overflow issue, but the safer formula is still a good habit.

### Q4. Why do we use `lo <= hi` instead of `lo < hi`?

We use `lo <= hi` because the search range includes both boundaries.

When:

```python
lo == hi
```

one element is still left to check.

Using `lo < hi` would skip that final element in this particular template.

Other Binary Search templates use `lo < hi`, but their boundary updates and stopping conditions are different.

### Q5. What happens if we write `lo = mid` or `hi = mid`?

It can cause an infinite loop because the search boundaries may not change.

For example:

```python
lo = 0
hi = 1
mid = 0
```

If we write:

```python
lo = mid
```

Then `lo` remains 0.

The same search range is processed again.

To avoid this, use:

```python
lo = mid + 1
```

or:

```python
hi = mid - 1
```

after checking the middle element.

### Q6. What if the array contains duplicate elements?

The basic Binary Search implementation can still find the target.

However, it may return any one of the matching indices.

It does not guarantee the first or last occurrence.

To find the first or last occurrence, we need a modified Binary Search approach, such as lower bound or upper bound.

See LeetCode 34: Find First and Last Position of Element in Sorted Array.

### Q7. Which is better: iterative or recursive Binary Search?

The iterative solution is usually preferred.

**Iterative:**
- Uses O(1) auxiliary space.
- Does not create recursive function calls.
- Avoids recursion-depth limitations.

**Recursive:**
- Can be easier to understand when learning recursion.
- Uses O(log n) call-stack space.

Both have O(log n) time complexity.

### Q8. Can Binary Search be applied to a linked list?

Binary Search is not efficient on a standard linked list.

In an array, we can directly access the middle element using its index in O(1) time.

In a linked list, reaching the middle element requires traversing nodes.

Finding the middle repeatedly can make the process inefficient, so Binary Search generally provides no advantage over linear traversal for a linked list.

### Q9. What if the array is sorted in descending order?

The comparison conditions must be reversed.

For an ascending array:

```python
if nums[mid] < target:
    lo = mid + 1
else:
    hi = mid - 1
```

For a descending array:

```python
if nums[mid] < target:
    hi = mid - 1
else:
    lo = mid + 1
```

This is because smaller values are on the right in an ascending array, but on the left in a descending array.

### Q10. What if the array size is unknown or extremely large?

If the array has an unknown size or we cannot directly determine its boundaries, we can use **Exponential Search**.

The idea is:

1. Start with an index of 1.
2. Keep doubling the index: 1, 2, 4, 8, 16, and so on.
3. Stop when the element at that index is greater than or equal to the target, or when the search boundary is reached.
4. Apply Binary Search within the identified range.

Exponential Search helps identify a suitable search interval before applying Binary Search.

### Q11. How can we solve this problem using Python's standard library?

Python provides the `bisect` module for working with sorted lists.

We can use `bisect_left()`:

```python
from bisect import bisect_left

def search(nums, target):
    i = bisect_left(nums, target)

    if i < len(nums) and nums[i] == target:
        return i

    return -1
```

`bisect_left()` returns the position where the target could be inserted while maintaining sorted order.

We then check whether the element at that position actually equals the target.

If it does, we return its index. Otherwise, we return `-1`.

---

## ⚠️ Common Mistakes

These are some common mistakes beginners make while implementing Binary Search.

### 1. Using `lo < hi` with the inclusive-boundary template

```python
while lo < hi:
```

This can skip the final remaining element.

For the template used in this problem, write:

```python
while lo <= hi:
```

### 2. Updating the boundary incorrectly

Incorrect:

```python
lo = mid
hi = mid
```

This can cause an infinite loop.

Correct:

```python
lo = mid + 1
hi = mid - 1
```

### 3. Initializing `hi` incorrectly

Incorrect:

```python
hi = len(nums)
```

For this inclusive-boundary template, the last valid index is:

```python
hi = len(nums) - 1
```

Remember that Python indexing starts at 0.

For an array of length 5, valid indices are:

```text
0, 1, 2, 3, 4
```

### 4. Forgetting to return `-1`

If the target is not found, the function must return `-1` after the loop.

### 5. Applying Binary Search to an unsorted array

Binary Search depends on sorted order.

If the array is not sorted, the algorithm cannot safely eliminate half of the elements.

### 6. Forgetting to update the search boundaries

Every unsuccessful comparison must reduce the search range.

If neither `lo` nor `hi` changes, the loop may never terminate.

---

## 📝 Key Takeaways

- Binary Search is used to search efficiently in sorted data.
- It reduces the search space by approximately half after every comparison.
- Its time complexity is **O(log n)**.
- The iterative implementation uses **O(1)** auxiliary space.
- `lo` represents the left boundary.
- `hi` represents the right boundary.
- `mid` represents the middle index.
- Use `while lo <= hi` with inclusive boundaries.
- Use `lo = mid + 1` when searching the right half.
- Use `hi = mid - 1` when searching the left half.
- Return `-1` when the target is not found.

### The Most Important Binary Search Template

Memorize this structure and understand why every line is written:

```python
lo, hi = 0, len(nums) - 1

while lo <= hi:
    mid = lo + (hi - lo) // 2

    if nums[mid] == target:
        return mid
    elif nums[mid] < target:
        lo = mid + 1
    else:
        hi = mid - 1

return -1
```

This is the **Classic Binary Search template**. Master it before moving on to more advanced variations.

---

## 🔗 Related Problems

After completing LeetCode 704, practice these problems to strengthen your understanding of Binary Search.

| Problem | Concept |
|---|---|
| [35. Search Insert Position](./35_search_insert_position.py) | Finding the correct insertion position |
| [34. Find First and Last Position of Element in Sorted Array](./34_first_last_position.py) | Finding the first and last occurrence |
| [278. First Bad Version](./278_first_bad_version.py) | Binary Search on a monotonic condition |
| [33. Search in Rotated Sorted Array](./33_search_rotated_array.py) | Binary Search in a rotated array |

### Before Moving to the Next Problem

Make sure you can:

- [ ] Explain Binary Search without looking at the notes.
- [ ] Explain why the array must be sorted.
- [ ] Write the iterative solution from memory.
- [ ] Explain the purpose of `lo`, `hi`, and `mid`.
- [ ] Explain why we use `lo <= hi`.
- [ ] Explain why we update boundaries using `mid + 1` and `mid - 1`.
- [ ] Dry-run the algorithm for both found and missing targets.
- [ ] Explain its time and space complexity.
- [ ] Solve LeetCode 704 independently.

---

[⬅️ Back to Binary Search README](./README.md)
