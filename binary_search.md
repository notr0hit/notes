Basic binary search code
```cpp
int search(vector<int> nums, int target) {
    int lo = 0, hi = nums.size()-1;
    while (lo < hi) {
        int mid = lo + (hi-lo+1)/2;
        if (target < nums[mid]) {
            hi = mid - 1;
        } else {
            lo = mid; 
        }
    }
    return nums[lo]==target?lo:-1;
};
```

The fundamental idea
1. lo & hi
We define two variables, let's call them lo and hi . They will store array indexes and they work like a boundary such that we will only be looking at elements inside the boundary.
Normally, we would want initialize the boundary to be the entire array.

```cpp
int lo = 0, hi = nums.size()-1;
```

2. mid
The mid variable indicates the middle element within the boundary. It separates our boundary into 2 parts. Remember how I said binary search works by keep cutting the elements in half, the mid element works like a traffic police, it indicates us which side do we want to cut our boundary to.

Note when an array has even number of elements, it's your decision to use either the left mid (lower mid) or the right mid (upper mid)

```cpp
int mid = lo + (hi - lo) / 2; // left/lower mid
int mid = lo + (hi - lo + 1) / 2; // right/upper mid
```

3. Comparing the target to mid
By comparing our target to mid, we can identify which side of the boundary does the target belong. For example, If our target is greater than mid, this means it must exist in the right of mid . In this case, there is no reason to even keep a record of all the numbers to its left. And this is the fundamental mechanics of binary search - keep shrinking the boundary.

```cpp
if (target < nums[mid]) {
	hi = mid - 1
} else {
	lo = mid; 
}
```

4. Keep the loop going
Lastly, we use a while loop to keep the search going:

```cpp
while (lo < hi) { ... }
```
The while loop only exits when lo == hi, which means there's only one element left. And if we implemented everything correctly, that only element should be our answer(assume if the target is in the array).

**The pattern**
It may seem like binary search is such a simple idea, but when you look closely in the code, we are making some serious decisions that can completely change the behavior of our code.
These decisions include:

Do I use left or right mid?
Do I use < or <= , > or >=?
How much do I shrink the boundary? is it mid or mid - 1 or even mid + 1 ?
...
And just by messing up one of these decisions, either because you don't understand it completely or by mistake, it's going to break your code.
To solve these decision problems, I use the following set of rules to always keep me away from trouble, most importantly, it makes my code more consistent and predictable in all edge cases.

1. Choice of lo and hi, aka the boundary
Normally, we set the initial boundary to the number of elements in the array


```cpp
int lo = 0, hi = nums.size() - 1;
```
But this is not always the case.
We need to remember: the boundary is the range of elements we will be searching from.
The initial boundary should include ALL the elements, meaning all the possible answers should be included. Binary search can be applied to none array problems, such as Math, and this statement is still valid.

For example, In LeetCode 35, the question asks us to find an index to insert into the array.
It is possible that we insert after the last element of the array, thus the complete range of boundary becomes

```cpp
int lo = 0, hi = nums.size();
```
2. Calculate mid
Calculating mid can result in overflow when the numbers are extremely big. I ll demonstrate a few ways of calculating mid from the worst to the best.

``` cpp
int mid = (lo + hi) / 2 // worst, very easy to overflow

int mid = lo + (hi - lo) / 2 // much better, but still possible

int mid = (lo + hi) >> 1 // the best, but hard to understand
```

When we are dealing with even elements, it is our choice to pick the left mid or the right mid , and as I ll be explaining in a later section, a bad choice will lead to an infinity loop.

```cpp
int mid = lo + (hi - lo) / 2 // left/lower mid

int mid = lo + hi - lo + 1) / 2 // right/upper mid
```
3. How do we shrink boundary
I always try to keep the logic as simple as possible, that is a single pair of if...else. But what kind of logic are we using here? My rule of thumb is always use a logic that you can exclude mid.
Let's see an example:

``` cpp
if (target < nums[mid]) {
	hi = mid - 1
} else {
	lo = mid; 
}
```
Here, if the target is less than mid, there's no way mid will be our answer, and we can exclude it very confidently using hi = mid - 1. Otherwise, mid still has the potential to be the target, thus we include it in the boundary lo = mid.
On the other hand, we can rewrite the logic as:

``` cpp
if (target > nums[mid]) {
	lo = mid + 1; // mid is excluded
} else {
	hi = mid; // mid is included
}
```
4. while loop
To keep the logic simple, I always use

``` cpp
while(lo < hi) { ... }
```
Why? Because this way, the only condition the loop exits is lo == hi. I know they will be pointing to the same element, and I know that element always exists.

5. Avoid infinity loop
Remember I said a bad choice of left or right mid will lead to an infinity loop? Let's tackle this down.
Example:

``` cpp
int mid = lo + ((hi - lo) / 2); // Bad! We should use right/upper mid!
if (target < nums[mid]) {
	hi = mid - 1
} else {
	lo = mid; 
}
```
Now, imagine when there are only 2 elements left in the boundary. If the logic fell into the else statement, since we are using the left/lower mid, it's simply not doing anything. It just keeps shrinking itself to itself, and the program got stuck.
We have to keep in mind that, the choice of mid and our shrinking logic has to work together in a way that every time, at least 1 element is excluded.

```cpp
int mid = lo + ((hi - lo + 1) / 2); // Bad! We should use left/lower mid!

if (target > nums[mid]) {
	lo = mid + 1; // mid is excluded
} else {
	hi = mid; // mid is included
}
```
So when your binary search is stuck, think of the situation when there are only 2 elements left. Did the boundary shrink correctly?

TD;DR
My rule of thumb when it comes to binary search:

1. **Include ALL possible answers when initialize lo & hi**
2. **Don't overflow the mid calculation**
3. **Shrink boundary using a logic that will exclude mid**
4. **Avoid infinity loop by picking the correct mid and shrinking logic**
5. **Always think of the case when there are 2 elements left**

TIP: If we are setting ```lo = mid then``` choose right mid and vice versa.

SEARCH IN 2D MATRIX CODE
``` cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        // we will select which row has target
        int lo = 0, hi = matrix.size()-1;
        while (lo < hi) {
            int mid = lo + (hi-lo+1)/2; //right mid
            if (target<matrix[mid][0]) { // hi contains target < matrix[mid][0] and 
                                         // lo contains target >= matrix[mid][0]
                hi = mid - 1; // exclude mid
            } else {
                lo = mid; //include mid
            }
        }
        if (matrix[lo][0] == target) return true;
        int Row = hi;
        // Then we will search the row
        lo = 0, hi = matrix[0].size()-1;
        while (lo < hi) {
            int mid = lo + (hi-lo)/2; //left mid
            if (target>matrix[Row][mid]) {
                lo = mid + 1; // exclude mid
            } else {
                hi = mid; //include mid
            }
        }
        int Col = hi;
        // cout << Row << " " << Col;
        return matrix[Row][Col] == target;
    }
};
```

1.   KOKO EATING Bananas
```cpp
class Solution {
    bool good(vector<int> &piles, int k ,int h) {
        for (int &pile: piles) {
            h -= ((pile-1)/k + 1);
            if (h<0) return false;
        }
        return true;
    }
public:
    int minEatingSpeed(vector<int>& piles, int h) {
        // We will binary search k
        int lo = 1, hi = *max_element(piles.begin(), piles.end());
        while (lo < hi) {
            int mid = lo + (hi - lo)/2; // left mid
            if (good(piles, mid, h)) {
                hi = mid; // include mid
                
                // Here Invariant is that hi contiains only good k's
                // and since pointers are converging we'll get minimum k
            } else {
                lo = mid + 1; // exclude mid
            }
        }
        return hi;
    }

};
```

Find Min Element in Sorted Rotated Array
like = [4,5,6,7,0,1,2]
```cpp
class Solution {
public:
    int findMin(vector<int> &num) {
        int lo = 0, hi = num.size() - 1;
        while (lo < hi) {
            int mid = lo + (hi-lo)/2; // left mid
            if (num[lo] < num[hi]) // when low and high pointers converge
                return num[lo];
            
            if (num[mid] >= num[lo]) { // If nums[mid] >= num[lo] means mid is still in 1st sorted half
                lo = mid + 1; // so we move lo forward
            } else {
                hi = mid; // similarly we move high backwards
            }
        }
        return num[lo];
    }
};
```
In above example [4,5,6,7,0,1,2]
finally low and high pointer will point to index 4 and 5 

