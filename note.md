# Note

## Workflow

* start to see mipp integration (fma)

## Notes

### To add the new MIPP arch

* arch folder in `Eigen/src/Core/arch/<arch>/`
* arch load in `Eigen/Core`
* vecto config in `Eigen/src/Core/util/ConfigureVectorization.h`

### To benchmark

* run `./bench_multi_compilers.sh compilerlist.txt bench_norm.cpp`
* see BenchTimer

### Build and run Eigen test with MIPP

* `cmake .. -DEIGEN_TEST_MIPP=ON`
* `make check -j -k; while [ $? -ne 0 ]; do make check -j -k; done`

### Other

* disable the turbo boost `echo "1" | sudo tee /sys/devices/system/cpu/intel_pstate/no_turbo`
* to get all eigen types `cat */* | egrep -o 'Packet[0-9]+[a-z]+? ' | sort | uniq`

* `float* test = ...; ((intptr_t)test % 32);`

```c++
EIGEN_STRONG_INLINE Packet8f Bf16ToF32(const Packet8bf& a) {
  // auto s = mipp::cvt<short,float>(a);
  // auto s = mipp::cvt<int16_t,int32_t>((__m128)a.m_val);
  // return (__m256) mipp::lshift<float>(s, 16);
```

### Plafrim eigen tests

* `salloc -C skylake --time=03:00:00`
* `ssh <nodeName>`
* `module load build/cmake/3.21.3 compiler/gcc/11.2.0`
* `cd eigen && mkdir build ; cd build`
* `cmake .. -DEIGEN_TEST_MIPP=ON`
* `make buildtests -j -k; while [ $? -ne 0 ]; do make buildtests -j -k; done`

### types size

> Integer

| type        | type std | Byte | bits |
|:------------|:---------|:-----|:-----|
| char / bool | int8_t   | 1    | 8    |
| short       | int16_t  | 2    | 16   |
| int         | int32_t  | 4    | 32   |
| long        | int64_t  | 8    | 64   |

> float

| type   | type std | Byte | bits |
|:-------|:---------|:-----|:-----|
| float  | uint32_t | 4    | 32   |
| double | uint64_t | 8    | 64   |

### types size (Eigen / MIPP)

> Integer

| type eigen | type avx | ~type C/MIPP       |
|:-----------|:---------|:-------------------|
| Packet16b  | __m128i  | low + char         |
| Packet4i   | __m128i  | low + int          |
| Packet8i   | __m256i  | int                |
| Packet4l   | __m256i  | long               |
| Packet8h   | __m128i  | low + short (half) |

> float

| type eigen | type avx | ~type C/MIPP          |
|:-----------|:---------|:----------------------|
| Packet4f   | __m128   | low + float           |
| Packet8f   | __m256   | float                 |
| Packet2d   | __m128d  | low + double          |
| Packet4d   | __m256d  | double                |
| Packet8bf  | __m128i  | low + short (float16) |

<!-- Complex -->
Packet1cd
Packet2cd
Packet2cf
Packet4cf

<!-- other -->

<!-- float, double -->
Packet2f
Packet16f
Packet8d

<!-- short?, int, long -->
Packet8s
Packet4s
Packet2i
Packet16i
Packet2l
Packet4l

<!-- half -->
Packet4h
Packet8h
Packet16h

Packet4hf
Packet8hf

<!-- BFloat https://en.wikipedia.org/wiki/Bfloat16_floating-point_format -->
Packet16bf
Packet4bf
Packet8bf

<!-- ? -->
Packet2bl
Packet4bi

<!-- Complex -->
Packet4c
Packet8c
Packet16c
Packet1cd
Packet2cd
Packet4cd
Packet1cf
Packet2cf
Packet4cf
Packet8cf

<!-- unsigned -->
Packet2ui
Packet2ul
Packet4uc
Packet4ui
Packet4us
Packet8uc
Packet8us
Packet16uc

## Links

* <https://github.com/aff3ct/MIPP>

## Test failed

### On plafrim

> Plafrim

```txt
The following tests FAILED:
  902 - matrix_function_5 (Subprocess aborted)
  911 - matrix_power_7 (Subprocess aborted)
  975 - levenberg_marquardt (Subprocess aborted)
```

> PC

```txt
The following tests FAILED:
  412 - qr_colpivoting_1 (Child aborted)
  453 - eigensolver_selfadjoint_12 (Child aborted)
```

Tous passe au second run des tests