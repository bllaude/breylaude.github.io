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

### Matrix Exponentiation (26x26)
Only the character's index and numbers determine each character transformation. A transition matrix `T` is created:

- For `k = 1` to `nums[i]`, `T[i][(i + k) % 26] += 1`
- The spread of characters at index `I` into other characters is shown by this matrix.

To obtain the final frequency vector, we multiply this matrix by the initial frequency vector after raising it to power `t`.

The length is then obtained by adding up all of the values in the finished vector.

### Final Solution
```python
class Solution:
    def stringLengthAfterTransformations(self, s: str, t: int, nums: list[int]) -> int:
        MOD = 10**9 + 7
        N = 26

        # Initial frequency vector
        freq = [0] * N
        for ch in s:
            freq[ord(ch) - ord('a')] += 1

        # Build transformation matrix T (26 x 26)
        T = [[0] * N for _ in range(N)]
        for i in range(N):
            for k in range(1, nums[i] + 1):
                T[i][(i + k) % N] += 1

        # Matrix multiplication
        def matmul(A, B):
            res = [[0] * N for _ in range(N)]
            for i in range(N):
                for j in range(N):
                    for k in range(N):
                        res[i][j] = (res[i][j] + A[i][k] * B[k][j]) % MOD
            return res

        # Matrix exponentiation
        def matexp(mat, power):
            res = [[int(i == j) for j in range(N)] for i in range(N)]  # Identity matrix
            while power:
                if power % 2:
                    res = matmul(res, mat)
                mat = matmul(mat, mat)
                power //= 2
            return res

        T_exp = matexp(T, t)

        # Multiply T_exp with freq vector
        final_freq = [0] * N
        for i in range(N):
            for j in range(N):
                final_freq[i] = (final_freq[i] + T_exp[j][i] * freq[j]) % MOD

        return sum(final_freq) % MOD
```

### Time Complexity
Matrix exponentiation: `O(26^3 * log t)` ≈ constant time since 26 is fixed

Total: `O(log t)` — very efficient!
