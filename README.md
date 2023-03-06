# mold-llvm-cov-issue-demo
There was an issue when using llvm-cov with mold, and this repository aims to demonstrate it

# Usage
Clone repository and then in base directory:

```shell
mkdir build_ld && cd build_ld
cmake ..
make -j
```

After the program was built, run in `build_ld`:

```shell
./main_exe
llvm-profdata merge default.profraw -o default.profdata
llvm-cov report ./main_exe -instr-profile=default.profdata
```

And here is what I get from the console:

```text
Filename                      Regions    Missed Regions     Cover   Functions  Missed Functions  Executed  Instantiations   Missed Insts.  Executed       Lines      Missed Lines     Cover
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
main.cc                             1                 0   100.00%           1                 0   100.00%               1               0   100.00%           1                 0   100.00%
src/dummy.cc                        1                 0   100.00%           1                 0   100.00%               1               0   100.00%           1                 0   100.00%
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
TOTAL                               2                 0   100.00%           2                 0   100.00%               2               0   100.00%           2                 0   100.00%
```

To link using mold, I tried 2 ways:

```shell
mkdir build_mold && cd build_mold
cmake ..
mold -run make -j
```

Or:

```shell
mkdir build_mold && cd build_mold
cmake .. -DUSE_MOLD=ON
make -j
```

and then run these again in `build_mold`:

```shell
./main_exe
llvm-profdata merge default.profraw -o default.profdata
llvm-cov report ./main_exe -instr-profile=default.profdata
```

And here is what I got after linking with mold:

```text
Filename                      Regions    Missed Regions     Cover   Functions  Missed Functions  Executed  Instantiations   Missed Insts.  Executed       Lines      Missed Lines     Cover
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
main.cc                             1                 1     0.00%           1                 1     0.00%               1               1     0.00%           1                 1     0.00%
src/dummy.cc                        1                 1     0.00%           1                 1     0.00%               1               1     0.00%           1                 1     0.00%
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
TOTAL                               2                 2     0.00%           2                 2     0.00%               2               2     0.00%           2                 2     0.00%
```
