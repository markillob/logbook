# Arrays

## Operations with arrays

### Left rotation

In an array of size N and a left rotation of B times it is possible to rotate
the array by using a iterator i. First save array[i] to a temporary variable,
then replace the value of array[i] with array[i+B], array[i+B] with array[i+2B]
value, array[i+2B] with array[i+3B], and so on until the iterator overflows, in
this case remove the size of the array to the overflown iterator and continue
the process until the iterator is back at i. At this point replace the previous
position of the iterator with the temporary value. Continue this process and 
stop when i is equal to N and B greatest common divisor (GCD).

Runtime complexity: O(n)  
Space complexity: O(1)

#### Example: Array of size 5 with a left rotation of 2. GCD = 1.

```
+---+---+---+---+---+
| 0 | 1 | 2 | 3 | 4 |
+---+---+---+---+---+
```

Step one: Save a[0] to a temporary variable and replace a[0] with a[2]

```
+---+---+---+---+---+
| 2 | 1 | 2 | 3 | 4 |
+---+---+---+---+---+
```

Step two: Replace a[2] with a[4]

```
+---+---+---+---+---+
| 2 | 1 | 4 | 3 | 4 |
+---+---+---+---+---+
```

Step three: There is no a[6] so instead subtract the size of the array to the
            iterator and replace a[4] with a[1]

```
+---+---+---+---+---+
| 2 | 1 | 4 | 3 | 1 |
+---+---+---+---+---+
```

Step four: Replace a[1] with a[3]

```
+---+---+---+---+---+
| 2 | 3 | 4 | 3 | 1 |
+---+---+---+---+---+
```

Step five: There is no a[5], subtract the size of the array to the iterator and
           it will be 0, so back when it all started and it is done. Last step
           is to copy the temporary value to a[3]

```
+---+---+---+---+---+
| 2 | 3 | 4 | 0 | 1 |
+---+---+---+---+---+
```


#### Example: Array of size 6 with a left rotation of 3. GCD = 3.

```
+---+---+---+---+---+---+
| 0 | 1 | 2 | 3 | 4 | 5 |
+---+---+---+---+---+---+
```

Step one: Save a[0] to a temporary variable and replace a[0] with a[3]

```
+---+---+---+---+---+---+
| 3 | 1 | 2 | 3 | 4 | 5 |
+---+---+---+---+---+---+
```

Step two: There is no a[6] so subtract the size of the array to the iterator
          and it will be 0, back to the start. Replace a[3] with the temporary
          variable.

```
+---+---+---+---+---+---+
| 3 | 1 | 2 | 0 | 4 | 5 |
+---+---+---+---+---+---+
```

Step three: Start the process with a[1], save a[1] to a temporary variable and
            replace a[1] with a[4].

```
+---+---+---+---+---+---+
| 3 | 4 | 2 | 0 | 4 | 5 |
+---+---+---+---+---+---+
```

Step four: a[7] doesn't exist so subtract the size of the array to the iterator
           and it is back to 1. Replace a[4] with the temporary variable.

```
+---+---+---+---+---+---+
| 3 | 4 | 2 | 0 | 1 | 5 |
+---+---+---+---+---+---+
```

Step five: Continue with a[2]. Save a[2] to a temporary variable and replace
           a[2] with a[5].

```
+---+---+---+---+---+---+
| 3 | 4 | 5 | 0 | 1 | 5 |
+---+---+---+---+---+---+
```

Step six: The iterator overflows on a[8], so subtract the size of the array and
          it is back at 2. Replace a[5] with the temporary variable.

```
+---+---+---+---+---+---+
| 3 | 4 | 5 | 0 | 1 | 2 |
+---+---+---+---+---+---+
```

