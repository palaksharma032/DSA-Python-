# 🔍 Binary Search

> Halve the search space every step. Sorted input (or a monotonic condition) in, **O(log n)** out.

![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python&logoColor=white)
![Topic](https://img.shields.io/badge/Topic-Binary%20Search-orange)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

---

## 📌 Table of Contents

- [Core Idea](#-core-idea)
- [When to Use](#-when-to-use)
- [Complexity](#-complexity)
- [Templates](#-templates)
- [Problem Patterns](#-problem-patterns)
- [Solved Problems](#-solved-problems)
- [Common Mistakes](#-common-mistakes)
- [Python Shortcuts](#-python-shortcuts)

---

## 💡 Core Idea

Keep two pointers, `lo` and `hi`, around the region where the answer can still be. Check the middle element and throw away the half that cannot contain the answer. Repeat until the region is empty or the answer is found.

```
nums = [1, 3, 5, 7, 9, 11, 13], target = 9

lo=0, hi=6, mid=3 -> 7  < 9  -> search right half
lo=4, hi=6, mid=5 -> 11 > 9  -> search left half
lo=4, hi=4, mid=4 -> 9 == 9  -> found at index 4
```

---

## 🎯 When to Use

- Array is **sorted** (or rotated sorted)
- You need the **first / last position** of something
- The problem asks for the **minimum or maximum value** that satisfies a condition (search on answer)
- Brute force is O(n) or O(n²) and the input is large
- You can say "if X works, then everything above (or below) X also works" → the condition is **monotonic**

---

## ⏱️ Complexity

| Operation | Time | Space |
|---|---|---|
| Iterative binary search | O(log n) | O(1) |
| Recursive binary search | O(log n) | O(log n) |
| Search on answer | O(log(range) × cost of check) | O(1) |

---

## 🧩 Templates

### 1. Classic: find exact target

```python
def binary_search(nums: list[int], target: int) -> int:
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

### 2. Lower bound: first index where `nums[i] >= target`

```python
def lower_bound(nums: list[int], target: int) -> int:
    lo, hi = 0, len(nums)          # hi = len(nums): answer can be "past the end"
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if nums[mid] < target:
            lo = mid + 1
        else:
            hi = mid
    return lo
```

### 3. Upper bound: first index where `nums[i] > target`

```python
def upper_bound(nums: list[int], target: int) -> int:
    lo, hi = 0, len(nums)
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if nums[mid] <= target:
            lo = mid + 1
        else:
            hi = mid
    return lo
```

> First and last position of `target` = `lower_bound(target)` and `upper_bound(target) - 1`.

### 4. Search on answer: smallest value that satisfies a condition

```python
def min_feasible(lo: int, hi: int, feasible) -> int:
    """Smallest x in [lo, hi] where feasible(x) is True.
    feasible must be monotonic: False ... False True ... True"""
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if feasible(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo
```

Example: Koko Eating Bananas

```python
import math

def min_eating_speed(piles: list[int], h: int) -> int:
    def can_finish(speed):
        return sum(math.ceil(p / speed) for p in piles) <= h
    return min_feasible(1, max(piles), can_finish)
```

### 5. Rotated sorted array

```python
def search_rotated(nums: list[int], target: int) -> int:
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = lo + (hi - lo) // 2
        if nums[mid] == target:
            return mid
        if nums[lo] <= nums[mid]:               # left half is sorted
            if nums[lo] <= target < nums[mid]:
                hi = mid - 1
            else:
                lo = mid + 1
        else:                                   # right half is sorted
            if nums[mid] < target <= nums[hi]:
                lo = mid + 1
            else:
                hi = mid - 1
    return -1
```

---

## 🧠 Problem Patterns

| Pattern | Key signal | Template |
|---|---|---|
| Plain search | "Find target in sorted array" | #1 |
| Insert position / bounds | "First ≥ x", "where would it go" | #2, #3 |
| First & last occurrence | Duplicates present | #2 + #3 |
| Rotated array | "Sorted array rotated at some pivot" | #5 |
| Find minimum / peak | No target, just a property | Compare `mid` with a neighbor or `hi` |
| Search on answer | "Minimum X such that…", "maximize the minimum…" | #4 |
| 2D matrix | Rows/cols sorted | Treat as flattened 1D, `row = mid // cols`, `col = mid % cols` |

---

## ✅ Solved Problems

Based on the LeetCode **Binary Search** study plan. Add your solution links in the last column.

| # | Problem | Difficulty | Pattern | Solution |
|---|---|---|---|---|
| 704 | Binary Search | Easy | Plain search | [Python](./704_binary_search.py) · [📝 Notes](./704_Binary_Search/Readme.md) |
| 35 | Search Insert Position | Easy | Lower bound | [Python](./35_search_insert_position.py) · [📝 Notes](./35_Search_Insert_Position/Readme.md) |
| 278 | First Bad Version | Easy | Search on answer | [Python](./278_first_bad_version.py) |
| 69 | Sqrt(x) | Easy | Search on answer | [Python](./69_sqrt_x.py) |
| 367 | Valid Perfect Square | Easy | Search on answer | [Python](./367_valid_perfect_square.py) |
| 74 | Search a 2D Matrix | Medium | Flattened 1D | [Python](./74_search_2d_matrix.py) |
| 34 | Find First and Last Position | Medium | Lower + upper bound | [Python](./34_first_last_position.py) |
| 33 | Search in Rotated Sorted Array | Medium | Rotated array | [Python](./33_search_rotated_array.py) |
| 153 | Find Minimum in Rotated Sorted Array | Medium | Rotated array | [Python](./153_find_min_rotated.py) |
| 162 | Find Peak Element | Medium | Compare with neighbor | [Python](./162_find_peak_element.py) |
| 875 | Koko Eating Bananas | Medium | Search on answer | [Python](./875_koko_eating_bananas.py) |
| 1011 | Capacity To Ship Packages | Medium | Search on answer | [Python](./1011_ship_packages.py) |

> 📝 Edit this table to match the exact problems and filenames in your folder.

---

## 📚 Detailed Notes

In-depth notes with intuition, dry runs, edge cases, and interview Q&A:

| Problem | Pattern | Notes |
|---|---|---|
| Binary Search | Detailed Notes | [Read notes](./Binary_Search_Notes.md) |
| 704. Binary Search | Classic template | [Read notes](./704_Binary_Search/Readme.md) |
| 35. Search Insert Position | Lower bound | [Read notes](./35_Search_Insert_Position/Readme.md) |

### Suggested order to revise

1. **704**: learn the base template (`lo <= hi`, `mid ± 1`)
2. **35**: learn lower bound (`lo < hi`, `hi = mid`)
3. **34**: combine lower and upper bound
4. **278 / 69**: binary search on an answer range
5. **33 / 153**: rotated arrays
6. **875 / 1011**: "minimum feasible value" problems

---

## ⚠️ Common Mistakes

1. **Infinite loop**: using `lo = mid` with `mid = (lo + hi) // 2`. Always move past `mid` (`mid + 1` or `mid - 1`), or use the `lo < hi` / `hi = mid` form.
2. **Mixing the two loop styles**: `while lo <= hi` pairs with `hi = mid - 1`; `while lo < hi` pairs with `hi = mid`. Pick one and stay consistent.
3. **Off-by-one on `hi`**: `len(nums) - 1` for exact-match search, `len(nums)` when the answer can be "after the last element".
4. **Overflow habit**: write `mid = lo + (hi - lo) // 2`. Python won't overflow, but it's the right habit for C++ and Java interviews.
5. **Non-monotonic condition**: if the check isn't `False…False True…True`, binary search on answer will not work.
6. **Forgetting duplicates**: a plain search returns *any* match; use the bound templates for first/last.

---

## 🐍 Python Shortcuts

The standard library already has the bound functions:

```python
from bisect import bisect_left, bisect_right

nums = [1, 2, 2, 2, 5]
bisect_left(nums, 2)    # 1  -> lower_bound
bisect_right(nums, 2)   # 4  -> upper_bound
```

Use them to cross-check your own implementation, but write the manual version in interviews.

---

## 🔗 Next Up

Binary Search ✅ → *next topic coming soon*

[⬅️ Back to main DSA roadmap](../README.md)
