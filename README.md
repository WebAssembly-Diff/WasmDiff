# README

Here is some information to get you started:

- For an overview of the AFL++ documentation and a very helpful graphical guide, please visit [docs/README.md](https://github.com/AFLplusplus/AFLplusplus/blob/stable/docs/README.md).
- To get you started with tutorials, go to [docs/tutorials.md](https://github.com/AFLplusplus/AFLplusplus/blob/stable/docs/tutorials.md).
- For releases, see the [Releases tab](https://github.com/AFLplusplus/AFLplusplus/releases) and [branches](https://github.com/AFLplusplus/AFLplusplus#branches). The best branches to use are, however, `stable` or `dev` - depending on your risk appetite. Also take a look at the list of [important changes in AFL++](https://github.com/AFLplusplus/AFLplusplus/blob/stable/docs/important_changes.md) and the list of [features](https://github.com/AFLplusplus/AFLplusplus/blob/stable/docs/features.md).
- If you want to use AFL++ for your academic work, check the [papers page](https://aflplus.plus/papers/) on the website.
- To cite our work, look at the [Cite](https://github.com/AFLplusplus/AFLplusplus#cite) section.
- For comparisons, use the fuzzbench `aflplusplus` setup, or use `afl-clang-fast` with `AFL_LLVM_CMPLOG=1`. You can find the `aflplusplus` default configuration on Google's [fuzzbench](https://github.com/google/fuzzbench/tree/master/fuzzers/aflplusplus).

## Compile WasmDiff

```bash
make -j
```



## Compile target program

```bash
CC=/path/to/afl-cc CXX=/path/to/afl-c++ ./configure --disable-shared


```

