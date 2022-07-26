# Binary Search
Binary search is a searching algorithm when the elements follow a certain order/pattern

# Basic Syntax

**Iterative Binary Search**

``` python
def binary_search(low, high, nums, target):
    ans = -1
    while low <= high:
        mid = low + (high - low + 1) // 2
        midVal = nums[mid]
        if midVal < target:
            low = mid + 1
        elif midVal > target:
            high = mid - 1
        elif midVal == target:
            ans = mid
            break
    return ans
```

Space Complexity: O(1)  
Time Complexity: O(logn)


**Recursive Binary Search**

``` python
def binary_search(low, high, nums, target):
    if low < high:
        return False
    else:
        mid = low + (high - low + 1) // 2
        midVal = nums[mid]
        if midVal == target:
            return mid
        elif midVal < target:
            return binary_search(mid + 1, high, nums, target)
        else:
            return binary_search(low, mid - 1, nums, target)
```

Space Complexity: O(logn) - recursive stack space  
Time Complexity: O(logn)

<br />

# Variations of Binary Search
In the one dimensional array, we can observe the following patterns of binary search  
1. Index of a key  
2. Index the left most key (or first target key) if duplicates are present  
3. Index the right most Key (or last target key) if duplicates are present  
4. Index of the least element greater than key  
5. Index of the greatest element lesser than key  


## 1. Index of a key

use the basic syntax to find the key

``` python
def binary_search(low, high, nums, target):
    ans = -1
    # use the low <= high if we are calculating the mid using the way mentioned in code
    while low <= high:
        mid = low + (high - low + 1) // 2
        midVal = nums[mid]
        if midVal < target:
            low = mid + 1
        elif midVal > target:
            high = mid - 1
        elif midVal == target:
            ans = mid
            break
    return ans
```

Space Complexity: O(1)  
Time Complexity: O(logn)

## 2. Index the left most key (or first target key) if duplicates are present

As we want to search for the left most key or the first key, we need to decrease the right value so that out window is closer to the left side of the nums 

``` python
def binary_left_search(low, high, nums, target):
    ans = -1
    while low <= high:
        mid = low + (high - low + 1) // 2
        midVal = nums[mid]
        if midVal < target:
            low = mid + 1
        elif midVal > target:
            high = mid - 1
        elif midVal == target:
            # As we need to search for the left value, high has to be decreased
            ans = mid
            high = mid - 1
    return ans
```

Space Complexity: O(1)  
Time Complexity: O(logn)

## 3. Index the right most key (or last target key) if duplicates are present

As we want to search for the right most key or the last key, we need to increase the left value so that out window is closer to the right side of the nums 

``` python
def binary_right_search(low, high, nums, target):
    ans = -1
    while low <= high:
        mid = low + (high - low + 1) // 2
        midVal = nums[mid]
        if midVal < target:
            low = mid + 1
        elif midVal > target:
            high = mid - 1
        elif midVal == target:
            # As we need to search for the right value, left has to be increased
            ans = mid
            left = mid + 1
    return ans
```

Space Complexity: O(1)  
Time Complexity: O(logn)

## 4. Index of the least element greater than key

``` python
def binary_greater_search(low, high, nums, target):
    ans = -1
    while low <= high:
        mid = low + (high - low + 1) // 2
        midVal = nums[mid]
        if midVal < target:
            low = mid + 1
        elif midVal > target:
            # here, the current mid value is a possible answer so we update the ans to mid
            ans = mid
            high = mid - 1
        elif midVal == target:
            # we cannot write ans = mid + 1 here because what if the value we are searching for is the last element and there is no greater element than that last element, So instead we update the window to the right side
            low = mid + 1

    return ans
```

Space Complexity: O(1)  
Time Complexity: O(logn)

## 5. Index of the greatest element lesser than key

``` python
def binary_lesser_search(low, high, nums, target):
    ans = -1
    while low <= high:
        mid = low + (high - low + 1) // 2
        midVal = nums[mid]
        if midVal < target:
            # here, the current mid value is a possible answer so we update the ans to mid
            ans = mid
            low = mid + 1
        elif midVal > target:
            high = mid - 1
        elif midVal == target:
            # we cannot write ans = mid - 1 here because what if the value we are searching for is the first element and there is no lesser element than that first element, So instead we update the window to the left side
            high = mid - 1

    return ans
```

Space Complexity: O(1)  
Time Complexity: O(logn)

# TODO
- Search for more articles and add more content

# References
- [https://www.geeksforgeeks.org/binary-search/](https://www.geeksforgeeks.org/binary-search/)
- [https://www.geeksforgeeks.org/variants-of-binary-search/](https://www.geeksforgeeks.org/variants-of-binary-search/)

# Related Problems
- [https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)