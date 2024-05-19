# Linux平台的OD

[eteran/edb-debugger: edb is a cross-platform AArch32/x86/x86-64 debugger. (github.com)](https://github.com/eteran/edb-debugger)

[Compiling (Ubuntu) · eteran/edb-debugger Wiki (github.com)](https://github.com/eteran/edb-debugger/wiki/Compiling-(Ubuntu))

```shell
# install dependencies
sudo apt-get install         \
    cmake                    \
    build-essential          \
    libdouble-conversion-dev \
    libqt5xmlpatterns5-dev   \
    qtbase5-dev              \
    qt5-qmake                \
    qttools5-dev             \
    libqt5svg5-dev           \
    libgraphviz-dev          \
    libcapstone-dev          \
    pkg-config               \
    qttools5-dev

# build and run edb
git clone --recursive https://github.com/eteran/edb-debugger.git
cd edb-debugger
mkdir build
cd build
cmake ..
make
./edb
```

	