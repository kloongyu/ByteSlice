**ByteSlice** is a main-memory data format for fixed length unsigned
integers, and attributes that can be encoded as such. It is primarily designed for highly efficient
ordinal comparison based *scan* and *lookup* in **column-store databases**. The basic idea is to
chop column values into multiple bytes and store the bytes at different
contiguous memory spaces.

The implementation heavily utilizes Single-Instruction-Multiple-Data
(**SIMD**) instruction sets on modern CPUs to achieve bare-metal speed
processing. The scan algorithms are optimized to reduce number of
instructions, memory footprint, branch mis-predictions and other
performance-critical factors.

# Clone

```bash
git clone --recursive https://github.com/fzqneo/ByteSlice.git
```

If you have cloned without the `--recursive` option, run below under the
top level directory:

```bash
git submodule update --init --recursive
```


# Build

You need CMake to generate build scripts. Makefile is tested.

To generate debug build:

```bash
mkdir debug
cd debug
cmake -DCMAKE_BUILD_TYPE=debug ..
make -j4
```

To generate release build:

```bash
mkdir release
cd release
cmake -DCMAKE_BUILD_TYPE=release ..
make -j4
```

NOTE: The default build type is `debug`, which may not give optimal
performance.


# Running examples

Example programs are in 'example/' directory.

```bash
example/example1 -s 10000000
```

To see a full list of options:

```bash
example/example1 -h
```

NOTE: The source code of example program showcases how to use the library.


# Using the library

A quick glimpse:

```c++
// Create a column of two million 12-bit values in ByteSlice format
Column* column = new Column(ColumnType::kByteSlicePadRight, 12, 2*1024*1024);
// Prepare a bit vector to store scan results
BitVector* bitvector = new BitVector(column);
// Execute scan on the column with predicate value < 3
column->Scan(Comparator::kLess,
            3,
            bitvector,
            Bitwise::kSet);

```


# Multithreading

Multithreading is controlled by OpenMP environment variables: (assume
you use GCC)

```bash
OMP_NUM_THREADS=2 ./example/example1
```

NOTE: The default number of threads depends on the system, which is
usually the number of cores. You may also want to set the thread
affinity via GOMP_CPU_AFFINITY (assume you use GCC).


# Running tests

```bash
make check
```

Build tests without running.

```bash
make check-build
```


#  Documentation (work in progress)

You need doxygen to generate documentations in html and latex.

```bash
 doxygen
```


# File structure

+ `example/` - Example programs

+ `third-party/` - Third-party libraries

+ `src/` - ByteSlice library source files

+ `tests/` - Unit tests written in GoogleTest framework


# Citing this work

Ziqiang Feng, Eric Lo, Ben Kao, and Wenjian Xu. "**Byteslice: Pushing
the envelop of main memory data processing with a new storage layout.**"
In Proceedings of the 2015 ACM SIGMOD International Conference on
Management of Data, pp. 31-46. ACM, 2015.

Download: http://dl.acm.org/citation.cfm?id=2747642


# Contact

Ziqiang Feng ( zf at cs dot cmu dot edu )


# Platform requirements

1. C++ compiler supporting C++11, OpenMP and AVX2
2. CPU with AVX2 instruction set extension


# Tested platform

This package has been tested with the following configuration:

- Linux 3.13.0-66-generic (64-bit)
- Intel(R) Core(TM) i7-4770 CPU @ 3.40GHz
- g++ 4.9.3


# Known issues

1. `posix_memalign()` is used in some files, causing compilation failure
   on Windows.
