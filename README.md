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
    (source: gopher's slack, #performance)

- Profiling `go tool pprof -alloc_space yourtestbinary.test yourmemprofile`
    (source: gopher's slack, #performance)

- If testing feels uncomfortable, your design is bad. This seems obvious but
    worhts having in this list (source: [learn go with
    tests](https://quii.gitbook.io/learn-go-with-tests/)

- Good resource for gotchas: [50 shades of
    Go](http://devs.cloudimmunity.com/gotchas-and-common-mistakes-in-go-golang/index.html)

- Good resource for code reviews: [Go's std
    guidelines](https://github.com/golang/go/wiki/CodeReviewComments)

- Inspect assembly from go code: https://go.godbolt.org/

- Importing `internal` packages can be circumvented using `replace` directives
    in go.mod (not recommended thou)

- Tradeoff: Variadic func arguments get performance hurt by the single variable case;
    this is significant maybe in a [tight loop](https://stackoverflow.com/questions/2212973/what-is-a-tight-loop); here's an
    [example](https://github.com/golang/go/commit/e85ffec784b867f016805873eec5dc91eec1c99a)
    as variadic get converted to an slice (source: gopher's slack, #performance)
