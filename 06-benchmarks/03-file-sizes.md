# File sizes

While the impact to build times is negligible, this page investigates how generics affect the file sizes of compiled code:

* [**The example**](#the-example): an exemplar case
* [**The benchmark**](#the-benchmark): how does it stack up?
* [**Key takeaways**](#key-takeaways): what it all means


## The example

This page describes how to run `BenchmarkGoBuild`, a Go-based benchmark that inspects the file sizes of:

* a package archive
* a binary executable

Each build varies based on the:

* list type, ex. boxed, generic, typed
* number of distinct type definitions

The packages used by this benchmark are:

* **`./lists/boxed`**: defines `type List []interface{}`
* **`./lists/typed`**: defines zero to many list types based on build tags
* **`./lists/generic`**: defines `type List[T any] []T`

The `typed` and `generic` packages are also subject to the following build tags:

* **`int`**: activates that package's list of `int`
* **`int8`**: activates that package's list of `int8`
* **`int16`**: activates that package's list of `int16`
* **`int32`**: activates that package's list of `int32`
* **`int64`**: activates that package's list of `int64`


## The benchmark

Run the benchmark with the following command:

```bash
docker run -it --rm go-generics-the-hard-way \
  go test -bench GoBuild -run GoBuild -count 1 -v ./06-benchmarks
```

The following table was generated by piping the above command to `hack/b2md.py -t filesize`:

| Artifact type | Number of types | File size (bytes) - typed | File size (bytes) - generic | Increase (bytes) | Increase (%) |
|:-------------:|:---------------:|:-------------------------:|:---------------------------:|:----------------:|:------------:|
| pkg | 0 | 826 | 1202 | 376 | 45.52 |
|  | 1 | 5980 | 11606 | 5626 | 94.08 |
|  | 2 | 10516 | 20332 | 9816 | 93.34 |
|  | 3 | 15086 | 29098 | 14012 | 92.88 |
|  | 4 | 19656 | 37862 | 18206 | 92.62 |
|  | 5 | 24226 | 46542 | 22316 | 92.12 |
| bin | 0 | 1119426 | 1119426 | 0 | 0.00 |
|  | 1 | 1119426 | 1119458 | 32 | 0.00 |
|  | 2 | 1119426 | 1119474 | 48 | 0.00 |
|  | 3 | 1119426 | 1119506 | 80 | 0.01 |
|  | 4 | 1119426 | 1119522 | 96 | 0.01 |
|  | 5 | 1119426 | 1119554 | 128 | 0.01 |


## Key takeaways

* It appears that package archives built from generic code are consistently close to twice as large versus as non-generic counterparts ([golang/go#50438](https://github.com/golang/go/issues/50438))
* At the same time, generic types appear to have no discernable impact on the size of compiled, binary executables

---

Next: _That's it, thank you for reading!_
