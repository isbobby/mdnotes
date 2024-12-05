Go slice type are analogous to arrays in other languages, but have some unusual properties.

# Array in Go
The slice type is built on top of Go's [[Array]] type, which holds a **fixed length** of elements in a contiguous memory region. The length of an array is **part of its type**, so
```
var a [4]int
var b [5]int
reflect.TypeOf(a) == reflect.TypeOf(b) // False
```

Go's Arrays are **values**, so it denotes the entire array, not a pointer to the first element of the array (unlike some other languages). When we do assignment, or passing it as a function parameter, we will make a copy of its contents.

```
a := [2]int{1,0}
b := a
b[0] = 0
fmt.Pritnln(a, b) // [1,0], [0,0]
```

We can also pass the pointer to the array
```
c := &a
c[0] = 0
fmt.Println(a, c) // [1,0] &[0,0]
```

One way to understand array is to think of it as a `struct` or `object`, where its attribute are indexed instead of named
```
a := [2]int{1,0} // or

type a struct {
	0 : 1,
	1 : 0,
}
```

# Slice in Go
Unlike arrays, slices are everywhere, they do not require length as part of their declaration. The type specification for slice is simply `[]T`.

We can create a slice with `:=`, or 
```
func make([]T, len, cap) []T // cap is optional
```

When the above function is called, `make` allocates an array, and returns a slice that refers to the array. Both the length and capacity of a slice can be inspected with the built-in `len` and `cap` functions.

A slice can also be formed by doing slicing with `:`, for example
```
b := []byte{'g', 'o', 'l', 'a', 'n', 'g'}
// b[:2] == []byte{'g', 'o'} len: 2, cap 6
// b[2:] == []byte{'l', 'a', 'n', 'g'} len: 4, cap 4
// b[:] == b len 6, cap 6
```

# Slice Internals
Think of slice as a descriptor, or a view object over an array segment. It consists of a pointer to the array, length of the segment, and its capacity.

The **length** denotes the number of elements referenced by the slice. The **capacity** is the number of elements in the underlying array, beginning from the element referenced by the slice pointer.

```
a := [10]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
b := a[3:5]
fmt.Println(b, len(b), cap(b)) // len is 5-3=2, cap is 10-3=7

b = append(b, -1)
fmt.Println(b, len(b), cap(b)) // 3,4,-1
fmt.Println(a) // 3,4,-1,6
```

Since slice references the underlying array, any modification done through slice will also reflect in the underlying array. So if we have two slices referencing the same underlying array, their writes may affect the value read by the other slice variable.

```
b := a[2:5] // 2,3,4
c := a[:]
c[3] = -1
fmt.Println(b) // 2,-1,4
```

# Slice Resizing
Slice provides dynamic resizing, which requires the creation of a new, larger slice and copy the contents of the original slice into it. In dynamic array resizing, we need to loop through the elements to assign from old to new array, this is made easier by `copy` built-in function.
```
func copy(dst, src []T) int
```

The copy takes slices of various length, but will only copy up to the smaller number of elements. `copy` also handle source and destination slices that share the same underlying array.
