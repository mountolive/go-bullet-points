# (GO) BULLET POINTS
Some shiny or curious facts about Go (and maybe something else), that I've found
in the wild.

- `Array`s get copied entirely when using the two variables version of `range`,
    while slices won't (simple reference vs value semantics). For that matter, 
    if using `range` to iterate an array use the following form:
    ```go
    for i, some := &theArray {
      // Do something
    }

    // OR, convert it to a slice

    for i, some := theArray[:] {
      // Do something
    }
    ```
    Iterating over a slice copies only the header of the slice.

    The one variable form for arrays doesn't need to copy it because the array
    can't be changed from the variable.

- Profiling `go tool pprof -alloc_space yourtestbinary.test yourmemprofile`
