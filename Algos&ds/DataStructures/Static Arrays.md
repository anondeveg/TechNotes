
static arrays are saved in consecutive order in RAM

![[Pasted image 20250531085809.png]]

# Time Complexity


| Operation           | [[Big O Complexity]] |
| ------------------- | -------------------- |
| r / w i-th element  | O(1)                 |
| Insert / Remove End | O(1)                 |
| Insert Middle       | O(n)                 |
| Remove Middle       | O(n)                 |

- items has to get shifted when inserting or deleting from the middle

## go example
```go
StaticArray := [2]int{X,Y}
```
