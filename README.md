# README

WasmDiff is a discrepancy detector that is based on differential fuzzing to automate the detection of discrepancy exhibited by C/C++ code porting to Wasm.

## Build

```bash
make -j
```



## Compile target program

```bash
cd [target_project_path]
CC=/path/to/afl-cc CXX=/path/to/afl-c++ ./configure --disable-shared
# Forkserver

#

```



## Fuzzing

