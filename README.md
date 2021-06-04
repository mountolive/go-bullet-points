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

- _I am looking to execute a code block in a thread safe way but only for the
    same userID or uniqueID. The code block can be executed asynchronously
    across userIDs, but requests for the same userID should execute one by one.
    How do I achieve this in Go?_. Answer:
    [singleflight](https://pkg.go.dev/golang.org/x/sync/singleflight) (source:
    gopher's slack, #general)

- Some weird splatting return: https://play.golang.org/p/VfwDP0h4W7l

- os.Stdout is threadsafe as long as the OS permitted (# of concurrent
    operations in a File), as multiple writes to the same file descriptor are
    atomic... But logger has its own lock (I couldn't follow the complete
    discussion: [this discussion](https://gophers.slack.com/archives/C0VP8EF3R/p1613069941471300)

- Type-safe prometheus metrics: https://github.com/cabify/gotoprom

- Interesting examples of behaviour: https://unexpected-go.com/

- https://golang.org/pkg/net/http/httputil/#DumpRequest To debug http requests

- Benchmarking: https://golang.org/pkg/testing/#Benchmark Also, an example:
    https://gist.github.com/peterbourgon/9721bc4984579e474aac7da8ba387a69 .
    Also, check: https://egonelbre.com/project/hrtime/

- Stupid slice ranging heads-up:

    ```go
    // range iteration over slice copies the value (so changing it doesn't change the original one)
	  x := make([]int, 3)

	  x[0], x[1], x[2] = 1, 2, 3

	  for i, val := range x {
		  fmt.Println(&x[i], "vs.", &val)
	  }
	
	  // This is pretty much the same for assignation
	  for i := 0; i < len(x); i++ {
		  v := x[i]
		  v = 100
		  fmt.Println(v, "vs.", x[i])
	  }
    ```

- Related to the previous one. Watch out:
    ```go
    // allPkgs is a slice of structs
	  pkgs := make([]*domain.Package, len(allPkgs))
	  for i, pkg := range allPkgs {
      // pkg will always point to the last one
      // so you'll end up with a slice of pointers all pointing to the last element
		  pkgs[i] = &pkg
	  }
    ```
- Nice article about `HTTP` heads-up in Go: https://martin.baillie.id/wrote/gotchas-in-the-go-network-packages-defaults/

- Keeping a `struct` in cache as `[]byte`: What's the best way to encoding?
    - `json` would be flexible to changes in the struct, but `gob` would be
        faster
    - You could use `gob` and a version field in it, so that you can keep track
        of the changes in the `struct`(and revalidate the cache with the new
        version)
    - If having it as `[]byte` is not required, then there are alternatives that
        can save encoding/decoding time using `interface{}`: https://github.com/dgryski/trifles/blob/master/cachetest/clock/clock.go
    source: [gopher-slack](https://gophers.slack.com/archives/C0VP8EF3R/p1622720579258500) (check thread)
