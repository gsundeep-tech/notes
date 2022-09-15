1. Merge Sort
2. Heap Sort
3. Quick Sort
4. Quick Select
5. Tim Sort



## 4. Quick Select
Quick select is used to sort the first k elements. Any problem to return the kth highest or smallest element we can use the quick select

```python

def partition(numbers, left, right, idx):
    numbers[idx], numbers[right] = numbers[right], numbers[idx]
    
    swap_idx = left
    for i in range(left, right):
        # swap all the lesser elements towards the left side
        if numbers[i] < numbers[right]:
            numbers[i], numbers[swap_idx] = numbers[swap_idx], numbers[i]
            swap_idx += 1
    numbers[swap_idx], numbers[right] = numbers[right], numbers[swap_idx]
    return swap_idx 

def quickselect(numbers, left, right, k):
    if left == right:
        return numbers[left]
    
    # any random index can be selected, here we are considering the mid element
    idx = (left + right) // 2

    idx = partition(numbers, left, right, idx)
    if idx == k:
        return numbers[idx]

    if idx > k:
        return quickselect(numbers, left, idx - 1, k)
    else:
        return quickselect(numbers, idx + 1, right, k)

# numbers are sorted from smallest to the largest
# if we want the second smallest then we can give k-1 as the argument value
# if we want the second largest then we can give len(numbers) - k as the argument value
quickselect(numbers, 0, len(numbers)-1, k-1)
```

Note: Median of Medians is also a selection algorithm we can use as an alternative for quickselect, but from the interviews perspective quickselect would be a good option