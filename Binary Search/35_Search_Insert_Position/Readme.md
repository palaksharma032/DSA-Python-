<h1 align=center> 35. Search Insert Position</h1>

![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen)
![Topic](https://img.shields.io/badge/Topic-Binary%20Search-orange)
![Pattern](https://img.shields.io/badge/Pattern-Lower%20Bound-blue)
![Language](https://img.shields.io/badge/Language-Python-yellow)

🔗 [LeetCode Problem](https://leetcode.com/problems/search-insert-position/)

---

## 📖 Problem in Simple Words

You are given a **sorted** list of **distinct** integers `nums` and a number `target`.

- If `target` is in the list, return its **index**.
- If it is **not** in the list, return the index **where it should be inserted** so the list stays sorted.

Required time complexity: **O(log n)**.

### Examples

| Input | Output | Why |
|---|---|---|
| `nums = [1, 3, 5, 6]`, `target = 5` | `2` | 5 is already at index 2 |
| `nums = [1, 3, 5, 6]`, `target = 2` | `1` | 2 would go between 1 and 3, i.e., index 1 |
| `nums = [1, 3, 5, 6]`, `target = 7` | `4` | 7 is bigger than everything, so it goes at the end |
| `nums = [1, 3, 5, 6]`, `target = 0` | `0` | 0 is smaller than everything, so it goes at the front |

### Constraints (the important ones)

- `nums` is sorted in **ascending** order
- All values are **distinct**
- 1 ≤ `len(nums)` ≤ 10⁴

> 💡 Clues for binary search: **"sorted"** and **"O(log n)"**. This is 704 with one twist: instead of returning `-1` when the target is missing, return the **insertion position**.

---

## 🧠 Intuition (Easy Language)

Imagine students standing in a line sorted by height, and a new student arrives. You want to find **the first position where the person standing there is the same height or taller**. That is where the new student should stand.

That sentence is the whole problem:

> **Find the first index `i` such that `nums[i] >= target`.**

- If `target` exists, the first element `>= target` is the target itself → its index.
- If `target` is missing, the first element `>= target` is the next bigger number → exactly the insertion position.
- If every element is smaller than `target`, no such index exists, so the answer is `len(nums)` (insert at the end).

This is called the **lower bound**. It is one of the most important binary search templates, and many harder problems reuse it.

### Why does binary search work here?

Looking at the condition `nums[i] >= target` across the sorted array:

```
nums:        1     3     5     6        target = 4
nums[i]>=4:  F     F     T     T
                         ^
                    first True = answer (index 2)
```

The pattern is always `False False ... False True True ... True`. We are searching for the **first True**. Because of this monotonic shape, we can safely discard half the array at each step.

---

## 🛠️ Approach (Lower Bound Template)

Search over the range `[0, len(nums)]`. Note the right end is `len(nums)`, not `len(nums) - 1`, because the answer can be "past the last element".

```
lo = 0, hi = len(nums)

while lo < hi:
    mid = lo + (hi - lo) // 2
    if nums[mid] < target:     # mid is too small, answer is to the right
        lo = mid + 1
    else:                      # mid could be the answer, keep it
        hi = mid

return lo
```

### Why `hi = mid` (not `mid - 1`)?

When `nums[mid] >= target`, `mid` **might be** the first valid position, so we must keep it in the range.

### Why `lo = mid + 1`?

When `nums[mid] < target`, `mid` is definitely **not** the answer, so we can safely skip it.

### Why `while lo < hi`?

The loop shrinks the range `[lo, hi)` until it has exactly one position left (`lo == hi`). That position is the answer, so no extra check is needed.

### Why return `lo`?

At the end `lo == hi`, and everything to the left is `< target` while everything from `lo` onward is `>= target`. So `lo` is the first valid position.

---

## 🔍 Dry Run

`nums = [1, 3, 5, 6]`

**Target = 2 (missing, goes in the middle)**

| Step | lo | hi | mid | nums[mid] | Decision |
|---|---|---|---|---|---|
| 1 | 0 | 4 | 2 | 5 | 5 >= 2 → `hi = 2` |
| 2 | 0 | 2 | 1 | 3 | 3 >= 2 → `hi = 1` |
| 3 | 0 | 1 | 0 | 1 | 1 < 2 → `lo = 1` |
| 4 | 1 | 1 | – | – | `lo == hi` → return **1** ✅ |

**Target = 5 (present)**

| Step | lo | hi | mid | nums[mid] | Decision |
|---|---|---|---|---|---|
| 1 | 0 | 4 | 2 | 5 | 5 >= 5 → `hi = 2` |
| 2 | 0 | 2 | 1 | 3 | 3 < 5 → `lo = 2` |
| 3 | 2 | 2 | – | – | `lo == hi` → return **2** ✅ |

**Target = 7 (bigger than everything)**

| Step | lo | hi | mid | nums[mid] | Decision |
|---|---|---|---|---|---|
| 1 | 0 | 4 | 2 | 5 | 5 < 7 → `lo = 3` |
| 2 | 3 | 4 | 3 | 6 | 6 < 7 → `lo = 4` |
| 3 | 4 | 4 | – | – | `lo == hi` → return **4** ✅ |

---

## 💻 Solutions (Python)

### 1. Lower bound template (preferred)

```python
class Solution:
    def searchInsert(self, nums: list[int], target: int) -> int:
        lo, hi = 0, len(nums)

        while lo < hi:
            mid = lo + (hi - lo) // 2

            if nums[mid] < target:
                lo = mid + 1
            else:
                hi = mid

        return lo
```

### 2. Classic 704 template (return `lo` at the end)

If you prefer the same template as problem 704, it also works. When the loop ends, `lo` is exactly the insertion position.

```python
class Solution:
    def searchInsert(self, nums: list[int], target: int) -> int:
        lo, hi = 0, len(nums) - 1

        while lo <= hi:
            mid = lo + (hi - lo) // 2

            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                lo = mid + 1
            else:
                hi = mid - 1

        return lo          # insertion position
```

**Why is `lo` correct here?** When the loop ends, `lo = hi + 1`. Everything at indices `<= hi` is smaller than `target`, and everything at indices `>= lo` is larger. So `lo` is exactly where `target` belongs.

### 3. Python built-in

```python
from bisect import bisect_left

class Solution:
    def searchInsert(self, nums: list[int], target: int) -> int:
        return bisect_left(nums, target)
```

`bisect_left` is the lower-bound function from the standard library. Good for checking your answer, but write the manual version in interviews.

### 4. Brute force (for comparison)

```python
class Solution:
    def searchInsert(self, nums: list[int], target: int) -> int:
        for i, num in enumerate(nums):
            if num >= target:
                return i
        return len(nums)
```

---

## ⏱️ Complexity

| Approach | Time | Space |
|---|---|---|
| Brute force (linear scan) | O(n) | O(1) |
| **Binary search (iterative)** | **O(log n)** | **O(1)** |

**Why O(log n)?** The range `[lo, hi)` halves every iteration.
**Why O(1) space?** Only `lo`, `hi`, `mid` are stored.

---

## 🧩 Pattern: Lower Bound Binary Search

> "Find the **first index** where a condition becomes true."

| | 704 (Classic) | 35 (Lower Bound) |
|---|---|---|
| Goal | Find exact target | Find first index with `nums[i] >= target` |
| `hi` starts at | `len(nums) - 1` | `len(nums)` |
| Loop condition | `lo <= hi` | `lo < hi` |
| When `nums[mid]` is big enough | `hi = mid - 1` | `hi = mid` (keep it) |
| Missing target | return `-1` | return `lo` (insertion point) |

**Pattern recognition checklist:**

- ✅ Sorted array
- ✅ "Where should it go?", "first position where…", "smallest index such that…"
- ✅ Answer might be **outside** the array (`len(nums)`)
- ✅ O(log n) required

**Same template is used in:**

| Problem | How it connects |
|---|---|
| 34. First and Last Position | Lower bound gives the first position; upper bound gives the last |
| 278. First Bad Version | "First True" in a False…True sequence |
| 744. Find Smallest Letter Greater Than Target | Upper bound on letters |
| 69. Sqrt(x) | "Largest x with x² ≤ n", a boundary search |
| 875. Koko Eating Bananas | "Smallest speed that works", a boundary on the answer range |

---

## 🧪 Edge Cases

| Case | Example | Expected |
|---|---|---|
| Target smaller than all | `[1, 3, 5, 6], 0` | `0` |
| Target larger than all | `[1, 3, 5, 6], 7` | `4` |
| Target equals first | `[1, 3, 5, 6], 1` | `0` |
| Target equals last | `[1, 3, 5, 6], 6` | `3` |
| Target in a gap | `[1, 3, 5, 6], 4` | `2` |
| Single element, smaller | `[5], 2` | `0` |
| Single element, equal | `[5], 5` | `0` |
| Single element, larger | `[5], 9` | `1` |
| Negative numbers | `[-5, -2, 0], -3` | `1` |

---

## 🎤 Interview Questions & Answers

**Q1. How is this different from the basic binary search (704)?**
704 returns `-1` when the target is missing. Here we return the position where it should be inserted, which is the **first index with `nums[i] >= target`** (lower bound).

**Q2. Why does `hi` start at `len(nums)` instead of `len(nums) - 1`?**
Because the answer can be `len(nums)` when the target is larger than every element. The range of possible answers is `[0, len(nums)]`.

**Q3. Why `hi = mid` and not `hi = mid - 1`?**
`mid` could itself be the first valid position. Removing it from the range could skip the answer.

**Q4. Can this loop run forever?**
Not with this template. `lo = mid + 1` always moves forward, and `hi = mid` always shrinks since `mid < hi` when `lo < hi`.

**Q5. What if the array had duplicates?**
Lower bound still returns the **first** index where `nums[i] >= target`, i.e., the leftmost occurrence if the target exists. If you want the position *after* all duplicates, use the upper bound (`nums[mid] <= target` → `lo = mid + 1`).

**Q6. What is the difference between lower bound and upper bound?**

| | Condition to move right | Returns |
|---|---|---|
| Lower bound | `nums[mid] < target` | First index with `nums[i] >= target` |
| Upper bound | `nums[mid] <= target` | First index with `nums[i] > target` |

**Q7. How do you check if the target actually exists after this search?**
Check `idx < len(nums) and nums[idx] == target`.

**Q8. Which is better here: the 704 template or the lower-bound template?**
Both are correct. Lower bound is cleaner for "boundary" problems (no special equality branch), while the 704 template is easier to remember. Use one consistently and know why `lo` is the answer.

**Q9. What if the array is sorted in descending order?**
Flip the comparison: move `lo = mid + 1` when `nums[mid] > target`.

**Q10. How is `bisect_left` related?**
`bisect_left(nums, target)` is exactly this problem. `bisect_right` is the upper bound.

**Q11. Can you solve this with a linear scan?**
Yes, in O(n), but it ignores the sorted property and fails the O(log n) requirement.

**Q12. What is the time complexity of inserting the element afterward?**
Finding the position is O(log n), but inserting into a Python list is O(n) because elements shift. So the total is O(n). The problem only asks for the position.

---

## ⚠️ Common Mistakes

1. Setting `hi = len(nums) - 1` with the `lo < hi` template → misses the "insert at the end" case
2. Using `hi = mid - 1` with `lo < hi` → can skip the answer
3. Returning `-1` when the target is missing
4. Returning `mid` instead of `lo` at the end
5. Using `<=` instead of `<` when deciding to move right (that turns lower bound into upper bound)

---

## 📝 Key Takeaways

- "Insert position" = **first index where `nums[i] >= target`** (lower bound)
- Search range is `[0, len(nums)]`, because the answer can be past the end
- Move right when `nums[mid] < target`; otherwise keep `mid` with `hi = mid`
- `lo` is the answer when the loop ends
- Lower bound is the foundation for 34, 278, and "search on answer" problems

---

## 🔗 Related Problems

- [704. Binary Search](./704_Binary_Search.md)
- [34. Find First and Last Position of Element in Sorted Array](./34_first_last_position.py)
- [278. First Bad Version](./278_first_bad_version.py)

[⬅️ Back to Binary Search README](./README.md)
