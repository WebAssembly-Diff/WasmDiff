# README

WasmDiff is a discrepancy detector that is based on differential fuzzing to automate the detection of discrepancy exhibited by C/C++ code porting to Wasm.

### Building WasmDiff

```bash
git clone https://github.com/WebAssembly-Diff/WasmDiff.git
cd WasmDiff
make -j
cd compilers
./build.sh
```

### Instrumenting WasmEdge

```bash
git clone https://github.com/WasmEdge/WasmEdge.git
cd WasmEdge
export LLVM_DIR=${LLVM-LIB}/lib
export LLD_DIR=${LLVM-LIB}/lib/cmake/lld
#Specify the compiler
cmake -DCMAKE_C_COMPILER_AR=${AR_PATH} -DCMAKE_C_COMPILER_RANLIB=${RANLIB_PATH} -DCMAKE_C_COMPILER=/WasmDiff/compilers/diff-cc-1 -DCMAKE_CXX_COMPILER=/WasmDiff/compilers/diff-cxx-1 -DCMAKE_BUILD_TYPE=Release -DWASMEDGE_BUILD_TESTS=ON ..
#Build
make -j
#Export
export AFL_WASM_RUNTIME=`pwd`/WasmEdge/build/tools/wasmedge/wasmedge
```

### Compiling C/C++ single file

```bash
/WasmDiff/afl-clang -O2 target.c -o target
/WasmDiff/compiler/diff-cc-0 -02 target.c -o target-0
emcc -s STANDALONE_WASM target.c -02 -o target.wasm
```

### Compiling the project (Libitff as an example)

```bash
wget https://download.osgeo.org/libtiff/tiff-4.6.0.tar.gz
./autogen.sh
# patch the config.sub
grep -q -F -- '-wasi' ./config/config.sub ||   sed -i -e 's/-nacl\*)/-nacl*|-wasi)/' ./config/config.sub
#AFL-instrument
/WasmDiff/afl-clang ./configure --enable-shared=no CFLAGS="-O2"
make clean && make -j && make check
#WasmDiff-forkserver-instrument
GCC_PATH=/usr/bin && CC=$GCC_PATH/clang CXX=$GCC_PATH/clang++ ./configure --enable-shared=no CFLAGS="-O0 -g" && make -j && make check
# target:tiff2pdf-0
/WasmDiff/compilers/diff-cc-0 -DHAVE_CONFIG_H -I. -I../../config -I../../libtiff -I../../port  -I../../libtiff -I../../port -g -Wall -W -MT tiff2pdf.o -MD -MP -MF .deps/tiff2pdf.Tpo -c -o tiff2pdf.o tiff2pdf.c
mv -f .deps/tiff2pdf.Tpo .deps/tiff2pdf.Po
/WasmDiff/compilers/diff-cc-0 -g -Wall -W -o tiff2pdf-0 tiff2pdf.o  ../../libtiff/.libs/libtiff.a ../../port/.libs/libport.a /usr/lib/x86_64-linux-gnu/libjpeg.so -lz -lm
#Wasm
WASI_PATH=${WASI_SDK_PATH}/bin \
CC=$WASI_PATH/clang \
CXX=$WASI_PATH/clang++ \
CPP=$WASI_PATH/clang-cpp \
AR=$WASI_PATH/llvm-ar \
RANLIB=$WASI_PATH/llvm-ranlib \
NM=$WASI_PATH/llvm-nm \
LD=$WASI_PATH/wasm-ld \
./configure --with-oniguruma=builtin --host=wasm32-wasi --enable-shared=no CFLAGS="--sysroot=/opt/wasi-sdk-20.0/share/wasi-sysroot -D_WASI_EMULATED_PROCESS_CLOCKS -D_WASI_EMULATED_SIGNAL -O0 -g" LDFLAGS="-lwasi-emulated-process-clocks -lwasi-emulated-signal"
make clean && make && make check
```

#### If CMake required:

```bash
$ cmake -DCMAKE_TOOLCHAIN_FILE=${WASI_SDK_PATH}/share/cmake/wasi-sdk.cmake ...
```

### Fuzzing

```bash
sudo /WasmDiff/afl-sysconfig
# Standard output
/WasmDiff/afl-fuzz -i in -o out .. ./target @@
# File output
/WasmDiff/afl-fuzz -i in -o out -J 'test.file' .. ./target @@ test.file
```

The fuzzing results is under `out/default/diff/`.