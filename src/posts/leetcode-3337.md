---
title: 3337. Total Characters in String After Transformations II
description: 
permalink: posts/{{ title | slug }}/index.html
date: '2025-05-14'
tags: [leetcode, dp]
---

After `t` transformations, where each character c changes into `nums[ord(c) - ord('a')]` next characters in the alphabet (wrapping around if necessary), we are required to determine the length of the transformed string.

### Naive approach won't work
Simulating the transformation directly would be too slow and memory-heavy:

- `s.length` can be `10^5`
- `t` can be up to `10^9`
- Each character can expand, making the string exponentially large

### Key approach
We only need to monitor how the length increases; we don't need to construct the string itself.

Let's define `dp[i] = result length following i transformations`.

By utilizing the number of characters that each character produces, we can utilize a frequency count of characters to model how the overall length increases with each transformation.

### Strategy
1. Keep track of character frequency: `freq[c] = number of times character c appears`
2. In every transformation:
It generates `nums[c]` new characters (length-wise) for each character `c`.
3. Since `t` can be very large (up to `10^9`), we efficiently simulate `t` transformations using dynamic programming or matrix exponentiation.

This resembles a linear recurrence:
- The number of characters is what matters to us, not their character identities.

So simulate:
```sql
Next total length = sum of current character counts * nums[char]
```

### Optimized DP approach
This is how we model it:

Let `f[i] be the total number of characters following i transformations`.

- Initially, `f[0] = len(s)`
- For each transformation:
    - Each character will generate a certain number of characters according to nums.
    - Therefore, we keep a vector of character frequencies (size 26) to model it.

A frequency array can be used to simulate transformations, and it can be updated for each transformation.

However, we compute the final state using matrix exponentiation for large `t`.


