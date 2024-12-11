Sliding window is a powerful technique for solving problems involving arrays or strings. It is especially useful for problems requiring analysis of contiguous subarrays or substrings.

It's best used in cases where
1. we need to find or optimise properties of **contiguous** subarrays or substrings
2. solve problems involving a fixed, or variable length sub-array

# Algorithm Framework
We can broadly categorise sliding window into two categories - fixed and variable length sliding window.
## Fixed Window Size
The goal would normally be finding a window of fixed size `k`, which satisfy certain behaviour - be it maximum, minimum, or sum. It will reduce the time complexity from $O(n \times k)$ to just $O(n)$ 

```
1. define pointer start, end
2. include elements into the window by moving the end pointer
3. when the window reaches the required size k, compute result
4. increment both the start and end index
5. remove the start element and include the new end element
```

Base template
```
var currWindow = 0
for end < k {
	currWindow.update(input[end])
	end ++
}

start := 0
for end < len(input) {
	currWindow.remove(input[start])
	currWindow.update(input[end])

	start ++
	end ++
}
```

Its applications are
1. Signal processing and stream data analysis
2. Solving **range queries** in arrays 
3. Real time monitoring, such as moving averages, stock prices
4. Text processing, sub-string problems
## Variable Window Size
In some cases, we can allow the window size to dynamically adjust. The key here becomes growing or shrinking the window based on specific condition related to the problem at hand, ensuring the window size is optimal at every step (min-maxed).

The key steps are
1. expanding the window by moving the right pointer, to include more elements until the property of the window becomes invalid, or the desired condition is satisfied
2. move the left pointer to shrink the window to restore validity, or optimise window size.
3. during the adjustment process, update the result whenever the conditions are met.

```
start = 0
result = []

currentWindow = 0

for end < len(arr) {
	currWindow.update(arr[end])

	for !currWindow.IsValid() {
		currentWindow.remove(arr[start])
		start ++
	}

	if currWindow.IsValid() {
		result.append(arr[start:end])
	}
}

return result
```


