# Fenwick Tree / Binary Indexed Tree(BIT)

Good Video: 
https://www.youtube.com/watch?v=kPaJfAUwViY

Let, $f$ be some _reversible_ function and $A$ be an array of integers of length $N$.

Fenwick tree is a data structure which:

* calculates the value of function $f$ in the given range $[l, r]$ (i.e. $f(A_l, A_{l+1}, \dots, A_r)$) in $O(\log N)$ time;
* updates the value of an element of $A$ in $O(\log N)$ time;
* requires $O(N)$ memory, or in other words, exactly the same memory required for $A$;
* is easy to use and code, especially, in the case of multidimensional arrays.

Fenwick tree is also called **Binary Indexed Tree**, or just **BIT** abbreviated.

The most common application of Fenwick tree is _calculating the sum of a range_ (i.e. $f(A_1, A_2, \dots, A_k) = A_1 + A_2 + \dots + A_k$).

## Description

### One-based indexing approach

We want $bit[i]$ to store the sum of A$[g(i)+1 : i]$. here i is index of array A.

```python
def sum(int r):
    res = 0
    while (r > 0):
        res += t[r]
        r = g(r)
    return res

def increase(int i, int delta):
    for all j with g(j) < i <= j:
        t[j] += delta
```

The computation of $g(i)$ is defined as:
toggling of the last set $1$ bit in the binary representation of $i$.

$$\begin{align}
g(7) = g(111_2) = 110_2 &= 6 \\\\
g(6) = g(110_2) = 100_2 &= 4 \\\\
g(4) = g(100_2) = 000_2 &= 0 \\\\
\end{align}$$

The last set bit can be extracted using $i ~\&~ (-i)$, so the operation can be expressed as:

$$g(i) = i - (i ~\&~ (-i)).$$

And it's not hard to see, that you need to change all values $T[j]$ in the sequence $i,~ h(i),~ h(h(i)),~ \dots$ when you want to update $A[j]$, where $h(i)$ is defined as:

$$h(i) = i + (i ~\&~ (-i)).$$

As you can see, the main benefit of this approach is that the binary operations complement each other very nicely.

The following implementation can be used like the other implementations, however it uses one-based indexing internally.

```cpp
struct FenwickTreeOneBasedIndexing {
    vector<int> bit;  // binary indexed tree
    int n; // size of array A

    FenwickTreeOneBasedIndexing(int n) {
        this->n = n + 1;
        bit.assign(n + 1, 0);
    }

    FenwickTreeOneBasedIndexing(vector<int> a)
        : FenwickTreeOneBasedIndexing(a.size()) {
        for (size_t i = 0; i < a.size(); i++)
            update(i, a[i]);
    }

    int query(int idx) {
        ++idx; // ++idx for one based indexing
        int ret = 0;
        while (idx > 0) {
            ret += bit[idx];
            idx -= (idx & -idx);
        }
        return ret;
    }

    int query(int l, int r) {
        return query(r) - query(l - 1);
    }

    void update(int idx, int val) {
        ++idx; // ++idx for one based indexing
        while(idx <= n) {
            bit[idx] += val;
            idx += (idx & -idx);  
        }
    }
};
```

### Finding minimum of $[0, r]$ in one-dimensional array
It is obvious that there is no easy way of finding minimum of range $[l, r]$ using Fenwick tree, as Fenwick tree can only answer queries of type $[0,r]$. Additionally, each time a value is updated, the new value has to be smaller than the current value (because the  function is not reversible). These, of course, are significant limitations.

``` cpp
struct FenwickTreeMin {
    vector<int> bit;
    int n;
    const int INF = (int)1e9;

    FenwickTreeMin(int n) {
        this->n = n+1;
        bit.assign(n+1, INF);
    }

    FenwickTreeMin(vector<int> a) : FenwickTreeMin(a.size()) {
        for (size_t i = 0; i < a.size(); i++)
            update(i, a[i]);
    }

    int query_min(int r) {
        ++r;
        int ret = INF;
        while (r > 0) {
            ret = min(bit[r], ret);
            r -= (r & -r);
        }
        return ret;
    }

    void update(int idx, int val) {
        ++idx; // ++idx for one based indexing
        while(idx <= n) {
            bit[idx] = min(bit[idx],val);
            idx += (idx & -idx);  
        }
    }
};
```

### Finding sum in two-dimensional array from $[0,0]$ to $[x, y]$
As claimed before, it is very easy to implement Fenwick Tree for multidimensional array.

``` cpp
struct FenwickTree2D {
    vector<vector<int>> bit;
    int n, m;

    // init(...) { ... }

    int query(int x, int y) {
        int ret = 0;
        for (int i = x+1; i > 0; i -= (i & -i))
            for (int j = y+1; j > 0; j -= (j & -j))
                ret += bit[i][j];
        return ret;
    }

    void update(int x, int y, int val) {
        for (int i = x+1; i <= n; i += (i & -i))
            for (int j = y+1; j <= m; j += (j & -j))
                bit[i][j] += val;
    }
};
```

## Range operations

A Fenwick tree can support the following range operations:

1. Point Update and Range Query
2. Range Update and Point Query
3. Range Update and Range Query

### 1. Point Update and Range Query

This is just the ordinary Fenwick tree as explained above.

### 2. Range Update and Point Query

Using simple tricks we can also do the reverse operations: increasing ranges and querying for single values.

Let the Fenwick tree be initialized with zeros.
Suppose that we want to increment the interval $[l, r]$ by $x$.
We make two point update operations on Fenwick tree which are `add(l, x)` and `add(r+1, -x)`.

If we want to get the value of $A[i]$, we just need to take the prefix sum using the ordinary range sum method.
To see why this is true, we can just focus on the previous increment operation again.
If $i < l$, then the two update operations have no effect on the query and we get the sum $0$.
If $i \in [l, r]$, then we get the answer $x$ because of the first update operation.
And if $i > r$, then the second update operation will cancel the effect of first one.

The following implementation uses one-based indexing.

```cpp
void add(int idx, int val) {
    for (++idx; idx < n; idx += idx & -idx)
        bit[idx] += val;
}

void range_add(int l, int r, int val) {
    add(l, val);
    add(r + 1, -val);
}

int point_query(int idx) {
    int ret = 0;
    for (++idx; idx > 0; idx -= idx & -idx)
        ret += bit[idx];
    return ret;
}
```

Note: of course it is also possible to increase a single point $A[i]$ with `range_add(i, i, val)`.

### 3. Range Updates and Range Queries

To support both range updates and range queries we will use two BITs namely $B_1[]$ and $B_2[]$, initialized with zeros.

Suppose that we want to increment the interval $[l, r]$ by the value $x$.
Similarly as in the previous method, we perform two point updates on $B_1$: `add(B1, l, x)` and `add(B1, r+1, -x)`.
And we also update $B_2$. The details will be explained later.

```python
def range_add(l, r, x):
    add(B1, l, x)
    add(B1, r+1, -x)
    add(B2, l, x*(l-1))
    add(B2, r+1, -x*r))
```
After the range update $(l, r, x)$ the range sum query should return the following values:

$$
sum[0, i]=
\begin{cases}
0 & i < l \\\\
x \cdot (i-(l-1)) & l \le i \le r \\\\
x \cdot (r-l+1) & i > r \\\\
\end{cases}
$$

We can write the range sum as difference of two terms, where we use $B_1$ for first term and $B_2$ for second term.
The difference of the queries will give us prefix sum over $[0, i]$.

$$\begin{align}
sum[0, i] &= sum(B_1, i) \cdot i - sum(B_2, i) \\\\
&= \begin{cases}
0 \cdot i - 0 & i < l\\\\
x \cdot i - x \cdot (l-1) & l \le i \le r \\\\
0 \cdot i - (x \cdot (l-1) - x \cdot r) & i > r \\\\
\end{cases}
\end{align}
$$

The last expression is exactly equal to the required terms.
Thus we can use $B_2$ for shaving off extra terms when we multiply $B_1[i]\times i$.

We can find arbitrary range sums by computing the prefix sums for $l-1$ and $r$ and taking the difference of them again.

```python
def add(b, idx, x):
    while idx <= N:
        b[idx] += x
        idx += idx & -idx

def range_add(l,r,x):
    add(B1, l, x)
    add(B1, r+1, -x)
    add(B2, l, x*(l-1))
    add(B2, r+1, -x*r)

def sum(b, idx):
    total = 0
    while idx > 0:
        total += b[idx]
        idx -= idx & -idx
    return total

def prefix_sum(idx):
    return sum(B1, idx)*idx -  sum(B2, idx)

def range_sum(l, r):
    return prefix_sum(r) - prefix_sum(l-1)
```

## Practice Problems

* [UVA 12086 - Potentiometers](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=24&page=show_problem&problem=3238)
* [LOJ 1112 - Curious Robin Hood](http://www.lightoj.com/volume_showproblem.php?problem=1112)
* [LOJ 1266 - Points in Rectangle](http://www.lightoj.com/volume_showproblem.php?problem=1266 "2D Fenwick Tree")
* [Codechef - SPREAD](http://www.codechef.com/problems/SPREAD)
* [SPOJ - CTRICK](http://www.spoj.com/problems/CTRICK/)
* [SPOJ - MATSUM](http://www.spoj.com/problems/MATSUM/)
* [SPOJ - DQUERY](http://www.spoj.com/problems/DQUERY/)
* [SPOJ - NKTEAM](http://www.spoj.com/problems/NKTEAM/)
* [SPOJ - YODANESS](http://www.spoj.com/problems/YODANESS/)
* [SRM 310 - FloatingMedian](https://community.topcoder.com/stat?c=problem_statement&pm=6551&rd=9990)
* [SPOJ - Ada and Behives](http://www.spoj.com/problems/ADABEHIVE/)
* [Hackerearth - Counting in Byteland](https://www.hackerearth.com/practice/data-structures/advanced-data-structures/fenwick-binary-indexed-trees/practice-problems/algorithm/counting-in-byteland/)
* [DevSkill - Shan and String (archived)](http://web.archive.org/web/20210322010617/https://devskill.com/CodingProblems/ViewProblem/300)
* [Codeforces - Little Artem and Time Machine](http://codeforces.com/contest/669/problem/E)
* [Codeforces - Hanoi Factory](http://codeforces.com/contest/777/problem/E)
* [SPOJ - Tulip and Numbers](http://www.spoj.com/problems/TULIPNUM/)
* [SPOJ - SUMSUM](http://www.spoj.com/problems/SUMSUM/)
* [SPOJ - Sabir and Gifts](http://www.spoj.com/problems/SGIFT/)
* [SPOJ - The Permutation Game Again](http://www.spoj.com/problems/TPGA/)
* [SPOJ - Zig when you Zag](http://www.spoj.com/problems/ZIGZAG2/)
* [SPOJ - Cryon](http://www.spoj.com/problems/CRAYON/)
* [SPOJ - Weird Points](http://www.spoj.com/problems/DCEPC705/)
* [SPOJ - Its a Murder](http://www.spoj.com/problems/DCEPC206/)
* [SPOJ - Bored of Suffixes and Prefixes](http://www.spoj.com/problems/KOPC12G/)
* [SPOJ - Mega Inversions](http://www.spoj.com/problems/TRIPINV/)
* [Codeforces - Subsequences](http://codeforces.com/contest/597/problem/C)
* [Codeforces - Ball](http://codeforces.com/contest/12/problem/D)
* [GYM - The Kamphaeng Phet's Chedis](http://codeforces.com/gym/101047/problem/J)
* [Codeforces - Garlands](http://codeforces.com/contest/707/problem/E)
* [Codeforces - Inversions after Shuffle](http://codeforces.com/contest/749/problem/E)
* [GYM - Cairo Market](http://codeforces.com/problemset/gymProblem/101055/D)
* [Codeforces - Goodbye Souvenir](http://codeforces.com/contest/849/problem/E)
* [SPOJ - Ada and Species](http://www.spoj.com/problems/ADACABAA/)
* [Codeforces - Thor](https://codeforces.com/problemset/problem/704/A)
* [Latin American Regionals 2017 - Fundraising](http://matcomgrader.com/problem/9346/fundraising/)

## Other sources

* [Fenwick tree on Wikipedia](http://en.wikipedia.org/wiki/Fenwick_tree)
* [Binary indexed trees tutorial on TopCoder](https://www.topcoder.com/community/data-science/data-science-tutorials/binary-indexed-trees/)
* [Range updates and queries ](https://programmingcontests.quora.com/Tutorial-Range-Updates-in-Fenwick-Tree)


