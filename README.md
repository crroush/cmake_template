### CMAKE Template
This project serves an example of how to setup a cmake project in what is
hopefully a straight forward way, vs some of the other cmake examples that are
out there.  cmake has so many options and can be difficult to debug it is easy
to create a lot of bloat.

About me: I am not a cmake fan boy.  Build systems can be very complicated and
can leave developers spending way more time trying to compile than actually
doing good things. I use `make` and `cmake` regularly, and depending on the
project I will pick the solution that makes what I am trying to do easy.

#### Project Structure
```
.
├── benchmarks
│   ├── cmake_bench.cc
│   └── CMakeLists.txt
├── cmake
│   └── CPM.cmake
├── CMakeLists.txt
├── inc
│   └── cmk
│       └── cmake_proj.h
├── lib
│   ├── CMakeLists.txt
│   └── cmake_proj.cc
├── README.md
├── src
│   ├── CMakeLists.txt
│   └── cmake_src.cc
├── tests
│   ├── CMakeLists.txt
│   └── cmake_test.cc
└── third-party
    ├── benchmark-1.7.1.tar.gz
    ├── CMakeLists.txt
    ├── CMakeLists.txt.in
    ├── fftw-3.3.10.tar.gz
    └── googletest-1.13.0.tar.gz
```
##### Carrying your dependencies with you in your project
In some situations you may not have direct internet access, or in places where
internet access is heavily restricted.  For these situations it is better to
bring your dependencies with you.  I show how to carry those dependencies and
build them using [CPM](https://github.com/cpm-cmake/CPM.cmake)  for cmake projects
and `ExternalProject_Add` for other projects, like fftw.

##### Building during configure time:
There are cases that sometimes arise where you may not want to use the system
level version of a library.  In these cases you may consider wanting to build
your own version.  I have ran across this numerous times with fftw.  So rather
than trying to build and install one on the host, I show how you can build it
during configure time, and then you can link to it when you call
`target_link_libraries` for you executable by adding `PkgConfig::fftw3`

The hack a coworker showed me was to basically create a separate cmake project
`third-party/CMakeLists.txt.in` that is built during configuration time.

##### Benchmarks / Tests
I include googletest and google benchmark so you can easily test and benchmark
your software.  These are also packaged internally as tar.gz files.
```
make test
```
or

```
ctest
```
will execute the tests, you can also add github runners to force running the
tests on each commit or PR to master.

##### Code Examples
I show how to build a basic library and link against it with a src file.  The
code is really dumb, and provides no real utility other than to provide the user
with an example of how they might go about building their own libraries.

#### Building
It is setup to prefer to use clang vs gcc.  You can omit the lines in the
`cmake_template/CMakeLists.txt` by commenting them out or removing them.

```
find_program(CMAKE_C_COMPILER   NAMES $ENV{CC}  clang   gcc PATHS ENV PATH NO_DEFAULT_PATH)
find_program(CMAKE_CXX_COMPILER NAMES $ENV{CXX} clang++ g++ PATHS ENV PATH NO_DEFAULT_PATH)
```
##### Ninja
```
mkdir build
cd build
cmake .. -GNinja
ninja
```

##### Make
```
mkdir build
cd build
cmake ..
make -j`${nproc}`
```
