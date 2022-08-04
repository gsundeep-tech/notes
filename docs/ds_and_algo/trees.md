# Variations of Trees

1. Segment Tree
2. Binary Index Tree - Fenwick Tree 
3. AVL Trees
4. Red black trees


## Segment Tree

Let's understand the segment trees and Binary index trees usage by a problem statement

Problem: Given a array of integers, User would perform two operations
1. update an index in the array 
2. request the sum of a range in the array

Leetcode Problem: [https://leetcode.com/problems/range-sum-query-mutable/](https://leetcode.com/problems/range-sum-query-mutable/)

The brute force way of solving the above problem is 

```python

class Nums:
    def __init__(self, nums):
        self.nums = nums
    
    def update_index(self, idx, value):
        self.nums[idx] = value
    
    def query_range(self, start_idx, end_idx):
        total = 0
        for i in range(start_idx, end_idx + 1):
            total += self.nums[i]
```

Time Complexity: 
- Update Index: O(1)
- Query Range: O(n)

Space Complexity: O(1)

If there are many queries, i.e., n queries total time complexity for getting query range is n^2 


Using the prefix sum we can fetch the query range values to O(1). Following is the implementation

```python

class Nums:
    def __init__(self, nums):
        self.nums = nums
        self.prefix = []
        total = 0
        for num in self.nums:
            total += num
            self.prefix.append(total)
    
    def update_index(self, idx, value):
        diff = value - self.nums[idx]
        self.nums[idx] = value
        for i in range(idx, len(self.nums)):
            self.nums[i] += diff
    
    def query_range(self, start_idx, end_idx):
        if start_idx == 0:
            return self.prefix[end_idx]
        return self.prefix[end_idx] - self.prefix[start_idx]

```

Time Complexity: 
- Update Index: O(n)
- Query Range: O(1)

Space Complexity: O(n)

The disadvantage of using the above solution is, if there are many updates (n update operations), then the time complexity would be n^2


For both large update operation and large range query operations, we can solve it via Segment trees and Binary Index Trees.

Following is the implementation of Segment Tree

```python

class Nums:
    """
    This implementation of segment tree is based on the recursion 

    References: 
    1. https://leetcode.com/articles/a-recursive-approach-to-segment-trees-range-sum-queries-lazy-propagation/
    2. https://www.hackerearth.com/practice/data-structures/advanced-data-structures/segment-trees/tutorial/
    3. https://leetcode.com/problems/range-sum-query-mutable/discuss/1280869/Python%3A-All-possible-methods-Explained-with-time-and-space-complexity

    """
    def __init__(self, nums):
        self.nums = nums 
        self.nums_length = len(nums)
        self.tree = [0] * (4 * self.nums_length) # 0 to n -1 would be root nodes and n to 2 * n would be the input numbers which will be child nodes, for recursive solutions 4*n nodes are required
        self._build_tree(0, 0, self.nums_length - 1)
        self.lazy = [0] * (4 * self.nums_length) # this would be used for lazily updating the range queries

    def _build_tree(self, tree_idx, start, end):

        """
        Consider the example nums array: [2,4,7,11]
        tree array initialization would be: [0, 0, 0, 0, 0, 0, 0, 0] 

        following is the stack trace

        _build_tree(0, 0, 3) - [24, 6, 18, 2, 4, 7, 11, 0] step 7
            _build_tree(1, 0, 1) - [0, 6, 0, 2, 4, 0, 0, 0] step 3
                _build_tree(3, 0, 0) - [0, 0, 0, 2, 0, 0, 0, 0] step 1
                _build_tree(4, 1, 1) - [0, 0, 0, 2, 4, 0, 0, 0] step 2
            _build_tree(2, 2, 3) - [0, 6, 18, 2, 4, 7, 11, 0] step 6
                _build_tree(5, 2, 2) - [0, 6, 0, 2, 4, 7, 0, 0] step 4
                _build_tree(6, 3, 3) - [0, 6, 0, 2, 4, 7, 11, 0] step 5
        """

        if start == end:
            self.tree[tree_idx] = self.nums[start]
            return

        mid = start + (end - start) // 2
        self._build_tree(2*tree_idx + 1, start, mid)
        self._build_tree(2*tree_idx + 2, mid + 1, end)
        self.tree[tree_idx] = self._merge(self.tree[2*tree_idx + 1], self.tree[2*tree_idx + 2])

    def _merge(self, val1, val2):
        """
        This is for merging of two nodes, left and right child. In this case we are 
        """
        return val1 + val2

    def update_index(self, tree_idx, start_idx, end_idx, arr_idx, value):
        # this place arry_idx will also be equal
        if start_idx == end_idx:
            self.tree[tree_idx] = value
            return
        
        mid = start_idx + (end_idx - start_idx) // 2
        if arr_idx > mid:
            self.update_index(2*tree_idx + 2, mid + 1, end_idx, arr_idx, value)
        elif arr_idx <= mid:
            self.update_index(2*tree_idx + 1, start_idx, mid, arr_idx, value)
        
        self.tree[tree_idx] = self._merge(self.tree[2*tree_idx + 1], self.tree[2*tree_idx + 2])
        
    
    def query_range(self, tree_idx, start_idx, end_idx, query_start, query_end):
        # outside of the overlapping range
        if start_idx > query_end or end_idx < query_start:
            return 0
        
        """
        start and end are witin the query_start and query_end which means 

        assuming start = 3, end = 5 and query_start = 1, query_end = 7 

        [3, 5] overlaps with [1, 7] we can directly return the value for [3, 5] and we can search for [1, 2] and [6, 7]
        """
        if query_start <= start_idx and query_end >= end_idx:
            return self.tree[tree_idx]
        

        # if partial overlap
        mid = start_idx + (end_idx - start_idx) // 2

        if query_start > mid:
            return self.query_range(2*tree_idx + 2, mid + 1, end_idx, query_start, query_end)
        elif query_end <= mid:
            return self.query_range(2*tree_idx + 1, start_idx, mid, query_start, query_end)
        
        """
        if above both scenario's are not valid which means query_start and query_end values are near to start_idx and end_idx

        Example: start = 2, end = 7 and query_start=1, query_end=6

        mid = start + (end - start) // 2 = 2 + (7-2) // 2 =  4

        mid = 4 and it does not follow any of the if else conditions above, in this case we split into two group and process the two queries

        """
        leftQuery = self.query_range(2*tree_idx + 1, start_idx, mid, query_start, mid)
        rightQuery = self.query_range(2*tree_idx + 2, mid + 1, end_idx, mid + 1, query_end)

        return self._merge(leftQuery, rightQuery)

    def _merge_lazy(self, start, end, tree_idx):
        """
        Writing this method for the range sum queries and updates, but calculating the min and max in the range, it would be different

        reason for multiplying the (end - start + 1) is as we are summing those many nodes with the value, we are directly calculating the product and updating the value in the node, if both start and end are equal then value will be inserted otherwise for every other root node, we'll update by the number of child nodes
        """
        return (end - start + 1) * self.lazy[tree_idx] 


    def update_index_lazily(self, tree_idx, start, end, start_range, end_range, val):
        """
        Lazy update will be helpful for the range of updates, rather than the single node update.

        Example: 
        1. Update [0, 4] with 9
        2. Update [2, 3] with -8
        3. Update [3, 4] with 2

        In the above range updates, element 3 is being updated 3 times, 
        rather than updating at the same time which would increase our time complexity to nlogn for single range update 
        """
        if self.lazy[tree_idx] != 0: # this means we have some pending updates
            
            self.tree[tree_idx] += self._merge_lazy(start, end, tree_idx) # udpating the value to the current node 

            if start != end: # if we are not updating to the child node, then pass the lazy value to the child nodes
                self.lazy[2*tree_idx + 1] += self.lazy[tree_idx]
                self.lazy[2*tree_idx + 2] += self.lazy[tree_idx]
            
            # once the lazy value is udpated to the node and its children then we can reset the value back to zero
            self.lazy[tree_idx] = 0
        
        if start > end or start_range > end or end_range < start:
            "this will occur when there is no overlapping window"
            return 
        
        if start_range <= start and end >= end_range: # if the segment is fully within the range
            self.tree[tree_idx] += (end - start + 1) * val # updating the tree index value

            if start != end: # if we are not updating to the child node, then pass the lazy value to the child nodes
                self.lazy[2*tree_idx + 1] += val
                self.lazy[2*tree_idx + 2] += val
            
            return
        
        mid = start + (end-start) // 2
        self.update_index_lazily(2*tree_idx + 1, start, mid, start_range, end_range, val)
        self.update_index_lazily(2*tree_idx + 2, mid + 1, end, start_range, end_range, val)
            
        self.tree[tree_idx] = self._merge(self.tree[2*tree_idx + 1], self.tree[2*tree_idx + 2])
    

    def query_lazily(self, tree_idx, start, end, query_start, query_end):

        # outside of the overlapping range
        if start_idx > query_end or end_idx < query_start:
            return 0
        
        if self.lazy[tree_idx] != 0: # this means we have some pending updates
            
            self.tree[tree_idx] += self._merge_lazy(start, end, tree_idx) # udpating the value to the current node 

            if start != end: # if we are not updating to the child node, then pass the lazy value to the child nodes
                self.lazy[2*tree_idx + 1] += self.lazy[tree_idx]
                self.lazy[2*tree_idx + 2] += self.lazy[tree_idx]
            
            # once the lazy value is udpated to the node and its children then we can reset the value back to zero
            self.lazy[tree_idx] = 0

        """
        start and end are witin the query_start and query_end which means 
        assuming start = 3, end = 5 and query_start = 1, query_end = 7 
        [3, 5] overlaps with [1, 7] we can directly return the value for [3, 5] and we can search for [1, 2] and [6, 7]
        """
        if query_start <= start_idx and query_end >= end_idx:
            return self.tree[tree_idx]

        # if partial overlap
        mid = start + (end - start) // 2

        if query_start > mid:
            return self.query_lazily(2*tree_idx + 2, mid + 1, end_idx, query_start, query_end)
        elif query_end <= mid:
            return self.query_lazily(2*tree_idx + 1, start_idx, mid, query_start, query_end)
        
        """
        if above both scenario's are not valid which means query_start and query_end values are near to start_idx and end_idx
        Example: start = 2, end = 7 and query_start=1, query_end=6
        mid = start + (end - start) // 2 = 2 + (7-2) // 2 =  4

        mid = 4 and it does not follow any of the if else conditions above, in this case we split into two group and process the two queries

        """
        leftQuery = self.query_lazily(2*tree_idx + 1, start_idx, mid, query_start, mid)
        rightQuery = self.query_lazily(2*tree_idx + 2, mid + 1, end_idx, mid + 1, query_end)

        return self._merge(leftQuery, rightQuery) # this operation is dependent on the question, here we are doing sum

```

The above program is a recursive way, we can write the same in non recursive way as well. Following is the code example

```python

class Nums:
    """
    This is a non recursive way of implementing the segment tree, Much efficient way is described in this https://www.geeksforgeeks.org/segment-tree-efficient-implementation/
    References: 
    1. Editorial section of https://leetcode.com/problems/range-sum-query-mutable/solution/ 
    2. https://github.com/jakobkogler/Algorithm-DataStructures/blob/master/RangeQuery/SegmentTree.py (https://leetcode.com/problems/range-sum-query-mutable/discuss/646774/Segment-tree-recursive-iterative-Binary-index-iterative-explained)
    """
    def __init__(self, nums):
        self.nums = nums 
        self.nums_length = len(nums)
        self.tree = [0] * (2 * self.nums_length) # 0 to n -1 would be root nodes and n to 2 * n would be the input numbers which will be child nodes
        self._build_tree()
    
    def _merge(self, val1, val2):
        """
        Here we are trying to sum the values, for min segment tree, we need to find the min between the values
        """
        return val1 + val2

    def _build_tree(self):
        # child nodes start from the nums_length index
        for i in range(self.nums_length, 2*self.nums_length):
            self.tree[i] = self.nums[i-self.nums_length]
        
        # parent nodes, will occupy the starting positions, total number of nodes is (2*n - 1)
        for i in range(self.nums_length -1, -1, 0):
            self.tree[i] = self._merge(self.tree[2*i], self.tree[2*i + 1])
    
    def update_idx(self, idx, value):

        """
        Lets understand by taking an example
        nums = [2,4,7,11]
        tree = [0, 24, 6, 18, 2, 4, 7, 11]

        udpate idx = 3, val = 5
        step 1 : [0, 24, 6, 18, 2, 4, 7, 5] # updating the value at idx + nums_length
                  idx = 7
        Go into the loop

        Step 2: 
                left = 6, right = 7
                tree[7//2] = tree[3] = tree[6] + tree[7] = 7 + 5 = 12
                [0, 24, 6, 12, 2, 4, 7, 5] # updating the value at 3rd idx
        
        step 3: idx = 3
                right = 3, left = 2
                tree[1] = tree[3] + tree[2] = 18
                [0, 18, 6, 12, 2, 4, 7, 5] # updating the value at 3rd idx
        
        In the next iteration idx is 1 so loop will stop
        """

        idx += self.nums_length # map index to the second half to upate the node 
        self.tree[idx] = value

        while idx > 1:
            left = idx
            right = idx
            if idx % 2 == 0:
                right = idx + 1
            else:
                left = idx - 1
            self.tree[idx // 2] = self.tree[left] + self.tree[right]
            idx //= 2
    
    def query_range(self, start, end):
        """
        let's understand with the example
        nums = [2,4,7,11]
        tree = [0, 24, 6, 18, 2, 4, 7, 11]

        Query range = [1, 3] // starting with zero index, so final answer should be 4+7+11=22
        step1:
            start = 5, end = 8
        step 2:
            total += tree[start] => total += 4 => total = 4, update start += 1 => start = 6 
            start = 3, end = 4
        step 3:
            total += tree[start] => total += 18 => total = 22, start = 4
            start = 2, end = 2
        As the start not less than end, loop will get terminated

        """
        start += self.nums_length
        end += (self.nums_length + 1)

        total = 0
        while start < end:
            if start % 2 == 1:
                total += self.tree[start]
                start += 1
            
            if end % 2 == 1:
                end -= 1
                total += self.tree[end]
                
            start //= 2
            end //= 2
        return total
```

Complete Working Code can be found from [here](https://leetcode.com/submissions/detail/765114614/)

## Binary Index Tree - Fenwick Tree 

Binary Index Tree uses the binary representation of the indexes to store the values in the tree. Following the sample implementation. Binary Index Tree is also used for solving the range queries

Reference: https://leetcode.com/problems/range-sum-query-mutable/discuss/75753/Java-using-Binary-Indexed-Tree-with-clear-explanation