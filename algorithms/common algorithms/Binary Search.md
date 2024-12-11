Binary search operates by dividing the search interval in half repeatedly, and has a time complexity of $O(log\ n)$.

```
// Taken and modified from src/sort/search.go
func SearchInts(n int) int {
	i, j := 0, n
	for i < j {
		h := int(uint(i+j) >> 1)
		if x > h[i] {
			i = h + 1 
		} else {
			j = h 
		}
	}
	return i
}
```

The algorithm seems simple, but it requires good understanding to explain exactly why
1. updates are `=h`, and `=h+1`
2. terminate condition is `i < j`
3. why is the condition `a[h] >= x` and not `>`

Binary search has the following important [invariants](Invariants) which guarantee the correctness and termination:
1. Search array is sorted in ascending order
2. When the range becomes invalid - `l > r` - the target cannot exist in the array
3. The target is always within the search range, which is maintained by the algorithm narrowing the range based on the middle element

We can use the loop invariant to show the binary search is correct 
1. invariants are true at the beginning of the loop
2. invariants remain through at the end of the loop
3. after loop exits and the loop invariants hold, the loop must have achieved what is desired
4. if we need to show total correctness, we need to show some quantity strictly decreases

`h := int(uint(l+r)) >> 1` : we know the `h` or `middle` is the average of `l` and `r` rounded down, therefore, we know the target must be in either `arr[l...m]` or `arr[m+1...r]`.

If target is in `arr[l...m]`, then we must have `k <= arr[m]` to consider the last element in the array, the new values $l'$ and $r'$ will be $l$ and $m$ respectively, since $k \in arr[l,m]$, then $k \in arr[l',r']$. 

if target is in `arr[m+1...r]`, then we must have `k > arr[m]`, and the new values $l'$ and $r'$ at the end of the loop will be $m+1$ and $r$, since $k \in arr[m+1, r]$, $k \in [l',r']$.

```
func Search(n int, f func(int) bool) int {
	// Define f(-1) == false and f(n) == true.
	// Invariants: 
	//   f(i-1) == false, f(j) == true.
	//   arr is sorted    
	i, j := 0, n
	for i < j {
		h := int(uint(i+j) >> 1)
		// i ≤ h < j
		if !f(h) {
			i = h + 1 // preserves f(i-1) == false
		} else {
			j = h // preserves f(j) == true
		}
	}
	// i == j, f(i-1) == false, and f(j) (= f(i)) == true  =>  answer is i.
	return i
}
```

Given `[1,2,3,3,4,4,5]`, using `SearchInts(arr, 4)` yields 4, the first occurrence.
```
// m = avg(0, 7) = 3
Search(): [1,2,3,3,4,4,5] -> [1,2,3,3], [4,4,5], arr[h] == 3
	f(m) { arr[m] == 3 } -> false -> l' = m + 1

// m = avg(4, 7) = 5
Search(): [4,4,5] -> [4,4],[5]
	f(m) { arr[m] == 4} -> true -> r = m

// m = avg(4,5) = 4
Search(): [4,4] -> [4], [4]
	f(m) { arr[m] == 4} -> true -> r = m

l < r == false, invariant breached
```
