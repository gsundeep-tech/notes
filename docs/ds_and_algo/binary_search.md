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
1. Single Value Comparison  
   1. Index of a key  
   2. Index the left most key (or first target key) if duplicates are present  
   3. Index the right most Key (or last target key) if duplicates are present  
   4. Index of the least element greater than key  
   5. Index of the greatest element lesser than key  
2. Two Values Comparison
   1. Comparing the adjacent elements - Peaks in the given Array
   2. Comparing with the end elements - Find the index where the array is rotated  
3. Three Values Comparison
   1. Finding the range - Find the start and end index of target



## 1.1 Index of a key

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

## 1.2 Index the left most key (or first target key) if duplicates are present

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

## 1.3 Index the right most key (or last target key) if duplicates are present

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

## 1.4 Index of the least element greater than key

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

## 1.5 Index of the greatest element lesser than key

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


## 2.1 Get any peak in the array

``` python
def binary_peak_search(low, high, nums, target):
    # As we are supposed to compare the current mid with the beside element, then we need to use < rather than <= 
    # Note: <= is used for one value comparison and < can be used for two values comparison (mid and mid-1)
    while low < high:
        mid = low + (high - low + 1) // 2
        if nums[mid-1] < nums[mid]:
            # this can be the peak, so we are not incrementing by 1
            low = mid
        else:
            # As we know that mid - 1 value is higher so we are in the lower end so we need to shift our right
            high = mid - 1

    return left
```

Space Complexity: O(1)  
Time Complexity: O(logn)

## 2.2 Find the index where array is rotated

``` python
def binary_rotated_idx_search(low, high, nums, target):
    """
    Example 1: Consider a rotated Array [4, 5, 6, 7, 0, 1, 2, 3] - here the rotation happened at index 3 (i.e., element 7)
    Example 2: [0, 1, 2, 3, 4, 5] - here the order is not changed, so we need to check for the condition if the first element is less than the last elemenet
    
    Here we are comparing the mid with the left element reason why we are not searching with the previous element is there might be a scenario mid will be at second half of divided array
    Example: [6, 0, 1, 2, 3, 4, 5] - in this example mid will be at 3rd index at fist pass and if we are following the previous example it will return 5 (value) but the actual answer is 6 (value)

    """
    while low <= high:
        mid = low + (high - low + 1) // 2
        if nums[low] < nums[mid]:
            # this means we didn't find the peak yet so we are moving towards the right window
            low = mid + 1
        else:
            # this can be a peak but we are not sure, so we are keeping the mid as the high
            high = mid

    return left
```

Space Complexity: O(1)  
Time Complexity: O(logn)

## 3.1 Finding the range of the target
This is similar to finding the right most target and left most target that we have discussed in the 1.2 & 1.3 sections

# TODO
- Search for more articles and add more content

# References
- [https://www.geeksforgeeks.org/binary-search/](https://www.geeksforgeeks.org/binary-search/)
- [https://www.geeksforgeeks.org/variants-of-binary-search/](https://www.geeksforgeeks.org/variants-of-binary-search/)

# Related Problems
- [https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
- [https://leetcode.com/problems/find-peak-element/](https://leetcode.com/problems/find-peak-element/) - two values comparison problem
- [https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) - find the rotated index
- [https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/) - Find the range 