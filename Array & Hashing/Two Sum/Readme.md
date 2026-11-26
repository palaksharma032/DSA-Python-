<h1 align=center> 1. Two Sum </h1>

![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen)
![Topic](https://img.shields.io/badge/Topic-Arrays%20%26%20Hashing-orange)
![Pattern](https://img.shields.io/badge/Pattern-Complement%20Lookup%20(Hash%20Map)-blue)
![Language](https://img.shields.io/badge/Language-Python-yellow)

🔗 [LeetCode Problem](https://leetcode.com/problems/two-sum/)

---

## 📖 Problem in Simple Words

You are given a list of integers `nums` and a number `target`.

Find **two different elements** in the list whose sum equals `target`, and return their **indices** (positions), not the values.

Rules:

- Exactly **one** valid pair exists
- You cannot use the **same element twice** (the same index twice)
- You can return the two indices in **any order**

### Examples

| Input | Output | Why |
|---|---|---|
| `nums = [2, 7, 11, 15]`, `target = 9` | `[0, 1]` | 2 + 7 = 9 |
| `nums = [3, 2, 4]`, `target = 6` | `[1, 2]` | 2 + 4 = 6 (not 3 + 3, that would reuse index 0) |
| `nums = [3, 3]`, `target = 6` | `[0, 1]` | Two different elements can have the same value |

### Constraints (the important ones)

- 2 ≤ `len(nums)` ≤ 10⁴
- Values can be negative
- Array is **not sorted**
- Exactly one answer exists, so no need to handle "no solution"

> 💡 Follow-up asked by LeetCode: *can you do better than O(n²)?* That is the real question of this problem.

---

## 🧠 Intuition (Easy Language)

### Step 1: the slow way

Pick a number, then look at every other number to see if they add up to `target`. Repeat for every number. That's two nested loops → **O(n²)**.

### Step 2: the key observation

If you are standing at a number `x`, you don't need to search for "any pair". You know exactly what partner you need:

```
complement = target - x
```

So the question changes from *"which pair adds to target?"* to **"have I already seen `target - x`?"**

### Step 3: remember what you've seen

A **hash map** (Python `dict`) answers "have I seen this value, and where?" in O(1) average time.

Real-life analogy: you're at a party looking for someone to split a ₹100 bill with. Your share is ₹30, so you need a partner who pays ₹70. Instead of asking every person each time, you keep a notebook of what everyone already offered. For each new person, you just check the notebook for the matching amount.

---

## 🛠️ Approach: One-Pass Hash Map

1. Create an empty dictionary `seen` that maps `value → index`
2. Go through the array with `i` and `num`
3. Compute `complement = target - num`
4. If `complement` is already in `seen` → return `[seen[complement], i]`
5. Otherwise store `seen[num] = i` and continue

### Why check *before* storing?

This prevents using the same element twice. Example: `nums = [3, 2, 4]`, `target = 6`.

- At index 0, `num = 3`, complement is `3`.
- If we stored 3 first, we'd find it immediately and return `[0, 0]`, which is wrong (same element twice).
- By checking first, `seen` is empty, so we correctly skip and store `3` afterwards.

### Why does storing duplicates work?

For `[3, 3]`, target `6`:

- Index 0: complement `3` not in `seen` → store `{3: 0}`
- Index 1: complement `3` **is** in `seen` → return `[0, 1]` ✅

---

## 🔍 Dry Run

`nums = [2, 7, 11, 15]`, `target = 9`

| i | num | complement (9 - num) | seen before check | Found? | seen after |
|---|---|---|---|---|---|
| 0 | 2 | 7 | `{}` | No | `{2: 0}` |
| 1 | 7 | 2 | `{2: 0}` | **Yes** → return `[0, 1]` | – |

`nums = [3, 2, 4]`, `target = 6`

| i | num | complement | seen before check | Found? | seen after |
|---|---|---|---|---|---|
| 0 | 3 | 3 | `{}` | No | `{3: 0}` |
| 1 | 2 | 4 | `{3: 0}` | No | `{3: 0, 2: 1}` |
| 2 | 4 | 2 | `{3: 0, 2: 1}` | **Yes** → return `[1, 2]` | – |

---

## 💻 Solutions (Python)

### 1. Brute force: O(n²)

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        n = len(nums)
        for i in range(n):
            for j in range(i + 1, n):
                if nums[i] + nums[j] == target:
                    return [i, j]
        return []
```

### 2. One-pass hash map: O(n) ✅ (best)

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        seen = {}                      # value -> index

        for i, num in enumerate(nums):
            complement = target - num

            if complement in seen:
                return [seen[complement], i]

            seen[num] = i

        return []
```

### 3. Sorting + two pointers (when indices are preserved separately)

```python
class Solution:
    def twoSum(self, nums: list[int], target: int) -> list[int]:
        # keep original indices before sorting
        pairs = sorted((num, i) for i, num in enumerate(nums))
        lo, hi = 0, len(pairs) - 1

        while lo < hi:
            total = pairs[lo][0] + pairs[hi][0]
            if total == target:
                return [pairs[lo][1], pairs[hi][1]]
            elif total < target:
                lo += 1
            else:
                hi -= 1

        return []
```

---

## ⏱️ Complexity Comparison

| Approach | Time | Space | Notes |
|---|---|---|---|
| Brute force | O(n²) | O(1) | Simple, too slow for large input |
| Sort + two pointers | O(n log n) | O(n) | Sorting destroys indices, so we must store them |
| **One-pass hash map** | **O(n)** | **O(n)** | Trades memory for speed. Best answer |

**Why O(n) time?** One loop, and each dictionary lookup/insert is O(1) on average.
**Why O(n) space?** In the worst case we store almost every element in `seen`.

---

## 🧩 Pattern: Complement Lookup with a Hash Map

> "For each element, check if the value that completes it has already been seen."

This belongs to the **Arrays & Hashing** family. The trick is to **convert a search into a lookup**: instead of scanning for a partner (O(n)), store what you have seen and look it up (O(1)).

**Pattern recognition checklist:**

- ✅ Need to find two items related by a formula (sum, difference, pair)
- ✅ Array is unsorted, so binary search / two pointers don't apply directly
- ✅ Need the **index**, not just yes/no
- ✅ Want to beat O(n²)

**Same idea appears in:**

| Problem | How the idea is used |
|---|---|
| 167. Two Sum II (sorted input) | Sorted → two pointers is better (O(1) space) |
| 15. 3Sum | Fix one number, then do Two Sum on the rest |
| 560. Subarray Sum Equals K | Prefix sums + hash map of `prefix - k` |
| 1010. Pairs of Songs with Total Duration Divisible by 60 | Hash map of remainders |
| 454. 4Sum II | Hash map of pair sums |
| 653. Two Sum IV (BST) | Two Sum on a tree, using a set |

---

## 🧪 Edge Cases

| Case | Example | Expected |
|---|---|---|
| Duplicate values forming the pair | `[3, 3], 6` | `[0, 1]` |
| Negative numbers | `[-1, -2, -3, -4], -6` | `[1, 3]` |
| Zero involved | `[0, 4, 3, 0], 0` | `[0, 3]` |
| Pair at the very start | `[1, 2, 5], 3` | `[0, 1]` |
| Pair at the very end | `[5, 1, 2], 3` | `[1, 2]` |
| Same value usable only once | `[3, 2, 4], 6` | `[1, 2]` |

---

## 🎤 Interview Questions & Answers

**Q1. What is the brute-force solution and its complexity?**
Two nested loops checking every pair: O(n²) time, O(1) space.

**Q2. How do you optimize it?**
Use a hash map. For each number, look up `target - num` in O(1). Overall O(n) time, O(n) space.

**Q3. Why is a hash map lookup O(1)?**
Python dictionaries use hashing: the key is converted to a number that points directly to a memory bucket. Average case is O(1); worst case (many collisions) is O(n), but that's rare.

**Q4. Why check for the complement before inserting the current number?**
So the same element is never paired with itself. Inserting first would wrongly match `num` with its own index when `num * 2 == target`.

**Q5. What if there are duplicate values?**
It still works. If a duplicate is the needed complement, the earlier one is already in `seen` when the later one arrives (e.g., `[3, 3]`, target `6`).

**Q6. Why can't we just sort and use two pointers?**
Sorting shuffles positions, but the problem asks for **original indices**. You'd have to store `(value, index)` pairs, costing O(n log n) time and O(n) space, so it is worse than the hash map.

**Q7. What if the array were already sorted?**
Use two pointers (LeetCode 167): O(n) time and **O(1)** space. That is better than the hash map here.

**Q8. What if there can be multiple valid pairs and you must return all of them?**
Store every index per value (`dict[int, list[int]]`), or sort and use two pointers while skipping duplicates. Also clarify whether pairs `(i, j)` and `(j, i)` count as the same.

**Q9. What if there may be no solution?**
Return an empty list (or `None` / `[-1, -1]` depending on the contract). Here the problem guarantees one solution.

**Q10. What if the data is too large to fit in memory?**
External sort + two pointers, or partition by value range (hash partitioning) so each chunk fits in memory.

**Q11. What about integer overflow?**
Not an issue in Python. In C++/Java, use `long` if values can reach about 10⁹, since the sum may exceed 32-bit range.

**Q12. Can you do it in O(1) space?**
Not while keeping O(n) time on an unsorted array. You'd trade back to O(n²) brute force, or O(n log n) by sorting (which needs extra space to keep indices).

---

## ⚠️ Common Mistakes

1. Inserting `num` into `seen` **before** checking the complement → can return the same index twice
2. Returning **values** instead of **indices**
3. Sorting the array and returning sorted positions
4. Using a `set` instead of a `dict` → you lose the index
5. Assuming the array is sorted

---

## 📝 Key Takeaways

- Rewrite the problem as: *"Have I seen `target - x` before?"*
- A **hash map** turns an O(n) search into an O(1) lookup
- Check first, insert after
- One pass is enough: pairs are discovered when the **second** element arrives
- This is the foundation for 3Sum, Subarray Sum Equals K, and many "pair" problems

---

## 🔗 Related Problems

- 167. Two Sum II: Input Array Is Sorted
- 15. 3Sum
- 18. 4Sum
- 560. Subarray Sum Equals K
- 653. Two Sum IV: Input is a BST

[⬅️ Back to Arrays & Hashing](./README.md)
