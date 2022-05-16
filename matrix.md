**Problem:**
Get list of diagonals and antidognals in N * M Matrix

INPUT:
```
1 2 3
4 5 6
7 8 9
```

OUTPUT:
```
Diagonal: [
            [3],
            [2, 6],
            [1, 5, 9], 
            [7, 8],
            [7]
          ]
Anti-Diagonal:
          [
            [1],
            [2, 4],
            [3, 5, 7],
            [6, 8], 
            [9]
          ]
```

**Approach 1:**
Iterate over all diagnals

**Approach 2:**
Much simpler and concise  (Same time Complexity)

In this approach, we will make the use of sum of indices of any element in a matrix.   Let indices of any element be represented by i (row) an j (column).

If we find the sum of indices of any element in  a (N * M) matrix, we will observe that the sum of indices for any element lies between 0 (when i = j = 0) and N+M–2 (when i = N-1 and j = M-1). 

So we will follow the following steps: 

* Declare a vector of vectors of size N+M–1 for holding unique sums from sum = 0 to sum = N+M-2.
* Now we will loop through the vector and pushback the elements of similar sum to same row in that vector of vectors.

```
i+j: ANTI-DIAGONAL
    [                     [
    [0+0, 0+1, 0+2],       [0,1,2]
    [1+0, 1+1, 1+2],  ==   [1,2,3]
    [2+0, 2+1, 2+2]        [2,3,4]
    ]                     ]

i-j: DIAGONAL
    [                     [
    [0-0, 0-1, 0-2],       [0,-1,-2]
    [1-0, 1-1, 1-2],  ==   [1, 0,-1]
    [2-0, 2-1, 2-2]        [2, 1, 0]
    ]                     ]
    Here We'll add M-1 to make indices non-negative
```

CODE:
``` cpp
vector<vector<int>> diag(N + M -1);
vector<vector<int>> anti(N + M -1);

for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        diag[i + j].push_back(A[i][j]);// anti-diagonal
        anti[i + M - 1 - j].push_back(A[i][j]);  //diagonal
    }
}

```
``` cpp
// Print the diagonals
for (int diagonal: diag) {
    for (int x: diagonal)
        cout << x << " ";
}

```

Note: If there is a matrix of N * M then there are N+M-1 diagonals and N+M-1 anti-diagonals.


