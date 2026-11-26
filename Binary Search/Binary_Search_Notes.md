<h1 align=center> 📘 Binary Search: Detailed Notes </h1>


## 📌 Table of Contents

1. [What is Binary Search?](#1-what-is-binary-search)
2. [Prerequisites](#2-prerequisites)
3. [How It Works](#3-how-it-works)
4. [Why It's O(log n)](#4-why-its-olog-n)
5. [The Mental Model: First True / Last True](#5-the-mental-model-first-true--last-true)
6. [Templates](#6-templates)
7. [Patterns & How to Spot Them](#7-patterns--how-to-spot-them)
8. [Search on Answer: Step-by-Step Framework](#8-search-on-answer-step-by-step-framework)
9. [Choosing the Right Template](#9-choosing-the-right-template)
10. [Common Mistakes](#10-common-mistakes)
11. [Interview Questions & Answers](#11-interview-questions--answers)
12. [Cheat Sheet](#12-cheat-sheet)
13. [Practice Roadmap](#13-practice-roadmap)

---

## 1. What is Binary Search?

Binary search finds something in a **sorted** (or **monotonic**) space by repeatedly checking the **middle** and discarding the half that cannot contain the answer.

**Real-life examples**

- Looking up a word in a dictionary: open in the middle, decide left or right
- Guessing a number between 1 and 100 with "higher / lower" hints: always guess 50 first
- Finding the first buggy commit with `git bisect`

---

## 2. Prerequisites

Binary search works only if you can answer this question every time you look at `mid`:

> "Is the answer to the **left** or to the **right** of `mid`?"

That is possible when:

| Situation | Why it works |
|---|---|
| Sorted array | Comparing with `mid` tells you the side |
| Rotated sorted array | One half is always sorted, so you can reason about it |
| A yes/no condition that flips only once (`False…False True…True`) | The flip point is the answer (**monotonic**) |

If the data is unsorted and the condition isn't monotonic, binary search does **not** apply.

---

## 3. How It Works

```
nums = [2, 5, 8, 12, 16, 23, 38, 56, 72, 91], target = 23

Step 1: lo=0, hi=9, mid=4  -> 16 < 23  -> discard left half, lo = 5
Step 2: lo=5, hi=9, mid=7  -> 56 > 23  -> discard right half, hi = 6
Step 3: lo=5, hi=6, mid=5  -> 23 == 23 -> found at index 5
```

10 elements, 3 steps. A linear scan would need 6.

### The three pointers

- `lo`: left end of the region that can still contain the answer
- `hi`: right end of that region
- `mid = lo + (hi - lo) // 2`: the element we inspect

### The loop invariant (the idea that keeps you correct)

> **The answer, if it exists, is always inside the current `[lo, hi]` range.**

Every update must keep this true. If you ever write an update that might throw the answer away, the code is wrong. This single sentence explains almost every off-by-one bug.

---

## 4. Why It's O(log n)

Each step halves the range:

```
n -> n/2 -> n/4 -> ... -> 1
```

Number of steps = how many times you can halve `n` = **⌊log₂ n⌋ + 1**.

| n | Max steps |
|---|---|
| 10 | 4 |
| 1,000 | 10 |
| 1,000,000 | 20 |
| 1,000,000,000 | 30 |

Even for a billion elements, you need only about 30 comparisons.

| Variant | Time | Space |
|---|---|---|
| Iterative | O(log n) | O(1) |
| Recursive | O(log n) | O(log n) (call stack) |
| Search on answer | O(log(range) × cost of check) | O(1) |

---

## 5. The Mental Model: First True / Last True

Most binary search problems become easy when you rewrite them as:

> There is a sequence of `False`/`True` values, shaped `F F F F T T T T`. Find the **first True** (or the **last False/True**).

```
index:      0   1   2   3   4   5
nums:       1   3   5   6   8   9
nums>=6?:   F   F   F   T   T   T
                        ^ first True = lower bound = index 3
```

| Problem | The condition |
|---|---|
| Search Insert Position (35) | `nums[i] >= target` |
| First Bad Version (278) | `isBadVersion(i)` |
| Sqrt(x) (69) | `i * i <= x`: find the **last** True |
| Koko Eating Bananas (875) | `can_finish(speed)` |

> If you can write the condition, you can binary search.

---

## 6. Templates

### Template A: Exact match (`lo <= hi`)

Use when: finding a specific value, return `-1` if missing. (LeetCode 704)

```python
def binary_search(nums, target):
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

### Template B: Lower bound / first True (`lo < hi`)

Use when: first index with `nums[i] >= target`, insert position, "smallest X such that…". (35, 34, 278, 875)

```python
def lower_bound(nums, target):
    lo, hi = 0, len(nums)            # hi = len(nums): answer may be "past the end"
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if nums[mid] < target:       # mid is too small
            lo = mid + 1
        else:                        # mid might be the answer, keep it
            hi = mid
    return lo
```

### Template C: Upper bound (first index with `nums[i] > target`)

```python
def upper_bound(nums, target):
    lo, hi = 0, len(nums)
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if nums[mid] <= target:      # only difference: <= instead of <
            lo = mid + 1
        else:
            hi = mid
    return lo
```

> Count of `target` in a sorted array = `upper_bound(target) - lower_bound(target)`.

### Template D: Last True (maximum value that satisfies a condition)

Use when: "largest X such that…". (69 Sqrt)

```python
def max_feasible(lo, hi, feasible):
    """Largest x in [lo, hi] where feasible(x) is True (feasible must be True...True False...False)."""
    while lo < hi:
        mid = lo + (hi - lo + 1) // 2   # upper mid, otherwise infinite loop!
        if feasible(mid):
            lo = mid                    # mid might be the answer, keep it
        else:
            hi = mid - 1
    return lo

def my_sqrt(x):
    return max_feasible(0, x, lambda m: m * m <= x)
```

> ⚠️ When the update is `lo = mid`, you **must** use the upper mid `(lo + hi + 1) // 2`. With `lo = 0, hi = 1`, the lower mid is `0`, and `lo = mid` never moves → infinite loop.

### Template E: Search on answer (smallest feasible value)

```python
def min_feasible(lo, hi, feasible):
    """Smallest x in [lo, hi] where feasible(x) is True (False...False True...True)."""
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if feasible(mid):
            hi = mid
        else:
            lo = mid + 1
    return lo
```

---

## 7. Patterns & How to Spot Them

### Pattern 1: Classic search
**Signal:** sorted array, find a value.
**Problems:** 704
**Idea:** Template A.

### Pattern 2: Boundaries (lower / upper bound)
**Signal:** "first position", "insert position", "first and last occurrence", duplicates.
**Problems:** 35, 34
**Idea:** Templates B and C. `first = lower_bound(t)`, `last = upper_bound(t) - 1`.

### Pattern 3: Search on answer (binary search on the result)
**Signal:** "minimum X such that…", "maximum of the minimum…", "smallest capacity / speed / time". There may be no sorted array at all.
**Problems:** 69, 278, 367, 875, 1011, 410
**Idea:** Binary search over the **range of possible answers**, using a `feasible(x)` check.

Example: Capacity to Ship Packages Within D Days (1011)

```python
def ship_within_days(weights, days):
    def feasible(cap):
        needed, load = 1, 0
        for w in weights:
            if load + w > cap:
                needed += 1
                load = 0
            load += w
        return needed <= days

    return min_feasible(max(weights), sum(weights), feasible)
```

- Lowest possible capacity = `max(weights)` (must fit the heaviest package)
- Highest possible capacity = `sum(weights)` (ship everything in one day)
- Bigger capacity never hurts → the condition is monotonic ✅

### Pattern 4: Rotated sorted array
**Signal:** "sorted array rotated at an unknown pivot".
**Problems:** 33, 81, 153, 154
**Idea:** At least one half around `mid` is always sorted. Find which one, then decide.

Find minimum (153):

```python
def find_min(nums):
    lo, hi = 0, len(nums) - 1
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if nums[mid] > nums[hi]:     # minimum is in the right half
            lo = mid + 1
        else:                        # minimum is mid or in the left half
            hi = mid
    return nums[lo]
```

Why compare with `nums[hi]`? Because `hi` is always inside the "right-sorted" part, so it gives a reliable reference.

### Pattern 5: Peak / local property
**Signal:** no target, but you can tell which side holds a peak.
**Problems:** 162, 852
**Idea:** Compare `mid` with its neighbor and move **uphill**.

```python
def find_peak(nums):
    lo, hi = 0, len(nums) - 1
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if nums[mid] > nums[mid + 1]:   # descending: a peak is at mid or left
            hi = mid
        else:                           # ascending: a peak is to the right
            lo = mid + 1
    return lo
```

### Pattern 6: 2D matrix
**Signal:** matrix with sorted rows (and first element of each row > last of the previous).
**Problems:** 74
**Idea:** Treat as a flattened 1D array of `m * n` elements.

```python
def search_matrix(matrix, target):
    m, n = len(matrix), len(matrix[0])
    lo, hi = 0, m * n - 1
    while lo <= hi:
        mid = lo + (hi - lo) // 2
        val = matrix[mid // n][mid % n]     # row = mid // n, col = mid % n
        if val == target:
            return True
        elif val < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return False
```

---

## 8. Search on Answer: Step-by-Step Framework

When a problem says "find the **minimum/maximum** value such that something is possible":

| Step | Question to ask | Example (Koko Eating Bananas) |
|---|---|---|
| 1 | What is the **answer range**? | speed from `1` to `max(piles)` |
| 2 | Can I write `feasible(x)` that answers yes/no? | "Can Koko finish within `h` hours at speed `x`?" |
| 3 | Is it **monotonic**? | Faster speed never hurts → `F F F T T T` ✅ |
| 4 | First True or last True? | Minimum speed → first True |
| 5 | Plug into the template | `min_feasible(1, max(piles), can_finish)` |

> The check function is usually O(n), so the total is **O(n log(range))**.

---

## 9. Choosing the Right Template

| You need… | Use | Loop | Update on "go right" / "go left" |
|---|---|---|---|
| Exact value or `-1` | A | `lo <= hi` | `lo = mid + 1` / `hi = mid - 1` |
| First index `>= x` | B | `lo < hi` | `lo = mid + 1` / `hi = mid` |
| First index `> x` | C | `lo < hi` | `lo = mid + 1` / `hi = mid` |
| Last True / maximum feasible | D | `lo < hi` | `lo = mid` / `hi = mid - 1` (upper mid) |
| Smallest feasible value | E | `lo < hi` | `lo = mid + 1` / `hi = mid` |

**Rule of thumb**

- `while lo <= hi` pairs with `mid ± 1` on both sides.
- `while lo < hi` pairs with `hi = mid` (or `lo = mid` with upper mid).
- Pick one style per problem and don't mix.

---

## 10. Common Mistakes

| # | Mistake | Fix |
|---|---|---|
| 1 | Infinite loop with `lo = mid` | Use upper mid `(lo + hi + 1) // 2`, or use `mid + 1` |
| 2 | Mixing `lo <= hi` with `hi = mid` | Match the loop condition to the update style |
| 3 | `hi = len(nums) - 1` when the answer can be past the end | Use `hi = len(nums)` for bounds |
| 4 | Using `(lo + hi) // 2` in C++/Java | Use `lo + (hi - lo) // 2` to avoid overflow |
| 5 | Non-monotonic check function | Verify the `F…F T…T` shape before coding |
| 6 | Forgetting duplicates | Use lower/upper bound instead of exact match |
| 7 | Wrong answer range in search-on-answer | Low = smallest valid value, high = largest valid value |
| 8 | Not checking `nums[idx] == target` after a bound search | Check `idx < len(nums) and nums[idx] == target` |
| 9 | Searching an unsorted array | Sort first, or use a hash map if appropriate |

---

## 11. Interview Questions & Answers

**Q1. What is binary search and when can you use it?**
A search algorithm that halves the search space each step. It needs sorted data or a monotonic condition, so that comparing with `mid` tells which half to keep.

**Q2. What is the time and space complexity?**
Time O(log n). Space O(1) iteratively, O(log n) recursively.

**Q3. Why `mid = lo + (hi - lo) // 2`?**
It avoids integer overflow in fixed-width languages when `lo + hi` exceeds the integer limit. Python has no overflow, but the habit is expected.

**Q4. Difference between `lo <= hi` and `lo < hi`?**
`lo <= hi` checks every candidate and returns inside the loop; each side shrinks by `mid ± 1`. `lo < hi` converges to one candidate and returns `lo` after the loop; used with `hi = mid`.

**Q5. What are lower bound and upper bound?**
Lower bound = first index with value `>= x`. Upper bound = first index with value `> x`. Python: `bisect_left`, `bisect_right`.

**Q6. How do you find the first and last position of a target?**
`first = lower_bound(target)`, `last = upper_bound(target) - 1`. Check that `first < len(nums)` and `nums[first] == target`.

**Q7. What is "binary search on answer"?**
Instead of searching an array, you search the range of possible answers using a monotonic yes/no check. Example: smallest ship capacity, minimum eating speed.

**Q8. How do you search in a rotated sorted array?**
At each step one half is sorted. Check whether the target lies within the sorted half; if yes, go there; otherwise go to the other half.

**Q9. What changes if there are duplicates in a rotated array (33 → 81)?**
When `nums[lo] == nums[mid] == nums[hi]`, you can't tell which half is sorted, so shrink both ends by one. The worst case degrades to O(n).

**Q10. Can you binary search a linked list?**
Not efficiently. Reaching `mid` takes O(n), so there is no benefit over a linear scan.

**Q11. How to search in an infinite or unknown-size sorted array?**
Exponential search: double the right bound (1, 2, 4, 8, …) until it passes the target, then binary search inside that range.

**Q12. How would you find the square root of a number without `sqrt`?**
Binary search for the largest `m` with `m * m <= x` (Template D).

**Q13. How do you binary search on floating-point answers?**
Run a fixed number of iterations (e.g., 100) instead of `lo < hi`, shrinking the range each time, or loop until `hi - lo < 1e-6`.

**Q14. Why must the check function be monotonic?**
If feasibility flips back and forth, a single probe can't tell which half to discard, and the invariant breaks.

**Q15. Binary search vs hash map for lookups?**
Hash map: O(1) average lookup, O(n) extra space, no ordering. Binary search: O(log n), no extra space, supports range queries and "closest value" lookups.

**Q16. How would you check whether a value exists using `bisect`?**

```python
from bisect import bisect_left
i = bisect_left(nums, x)
exists = i < len(nums) and nums[i] == x
```

---

## 12. Cheat Sheet

```
Exact match        -> while lo <= hi,  mid±1          -> return mid or -1
First index >= x   -> while lo <  hi,  hi = mid       -> return lo
First index >  x   -> same, but nums[mid] <= x moves right
Largest feasible   -> while lo <  hi,  lo = mid, upper mid = (lo+hi+1)//2
Smallest feasible  -> while lo <  hi,  hi = mid       -> return lo
```

**Before coding, ask yourself:**

1. Is the data sorted or the condition monotonic?
2. What is the **range** of possible answers? (`hi` = `n` or `n - 1`?)
3. Am I looking for **first True** or **last True**?
4. Does my update ever **discard the answer**?
5. Can the loop **fail to shrink**?

---

## 13. Practice Roadmap

| Level | Problems | Focus |
|---|---|---|
| 🟢 Basic | 704, 35, 278, 69, 367 | Core templates |
| 🟡 Boundaries | 34, 744, 1351 | Lower / upper bound |
| 🟡 Matrix | 74, 240 | Flatten / staircase search |
| 🟡 Rotated | 33, 153, 81, 154 | Reasoning about sorted halves |
| 🟡 Peak | 162, 852 | Move uphill |
| 🟠 Search on answer | 875, 1011, 410, 1482 | Feasibility check |
| 🔴 Hard | 4 (Median of Two Sorted Arrays), 719 | Binary search on partition / answer |

**Tracker**

- [x] 704. Binary Search
- [x] 35. Search Insert Position
- [ ] Add your notes here as you revise each problem

---

[⬅️ Back to Binary Search README](./README.md)
