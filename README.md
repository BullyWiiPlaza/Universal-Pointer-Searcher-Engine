## Universal Pointer Searcher

This is a severely reworked `C++` command line version of the [`Universal Pointer Searcher`](https://github.com/BullyWiiPlaza/Universal-Pointer-Searcher) which offers you a very flexible way of performing pointer searches which furthermore supports finding pointers on memory dumps of any system/console out there (and not just one specific system like all other pointer searchers I have seen).

## Requirements

### Windows

This application may require you to install the `Microsoft Visual C++ Redistributable for Visual Studio 2015, 2017 and 2019` from [here](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0):

```
>dumpbin /dependents UniversalPointerSearcher.exe
Microsoft (R) COFF/PE Dumper Version 14.28.29336.0
Copyright (C) Microsoft Corporation.  All rights reserved.


Dump of file UniversalPointerSearcher.exe

File Type: EXECUTABLE IMAGE

  Image has the following dependencies:

    KERNEL32.dll
    USER32.dll
    bcrypt.dll
    WLDAP32.dll
    ADVAPI32.dll
    CRYPT32.dll
    WS2_32.dll
    WTSAPI32.dll
    KERNEL32.dll
    USER32.dll
    KERNEL32.dll
    USER32.dll
```

### Linux

The application may require you to install [`Intel TBB`](https://software.intel.com/content/www/us/en/develop/tools/oneapi/components/onetbb.html) via e.g. `sudo apt install libtbb-dev`. The application has the following dependencies:

```
$ ldd UniversalPointerSearcher.elf
    linux-vdso.so.1 (0x00007ffe2db9a000)
    libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f7c34edf000)
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f7c34ebc000)
    libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007f7c34cdb000)
    libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f7c34b8c000)
    libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007f7c34b71000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7c3497f000)
    libtbb.so.2 => /usr/lib/x86_64-linux-gnu/libtbb.so.2 (0x00007f7c34937000)
    /lib64/ld-linux-x86-64.so.2 (0x00007f7c365f6000)
```

## Repository Files

This repository contains the **README.md**, the **LICENSE**, the compiled **UniversalPointerSearcher.exe** (**Windows** version) and the compiled **UniversalPointerSearcher.elf** (**Linux** version). A **Mac OS X** version might be released at a later time if there is enough demand (let me know).

## Usage

This is a command line application. You have to pass the respective information such as source memory dump file path and starting address to the application via your system's command line (e.g. `cmd.exe`). Alternatively, there are more optional options to configure the pointer searcher. You can list them by passing the `--help` flag. If you do not pass certain command line options, meaningful defaults will be chosen for you automatically which may or may not be what you wanted.

Command line parsing works via `CLI11` so you may want to consult the documentation [here](https://cliutils.github.io/CLI11/book/) if you have any questions on how to pass certain information to the application.

Some pointer searcher demonstration runs can be seen [here](https://www.youtube.com/watch?v=FPbmaZVXkDw) or for learning in the section below.

**Note:**

Some of the command line switches may be renamed or changed since the creation of the video so always consult the `--help` documentation when in doubt.

### Examples

The following command line options can be used to perform different use cases. On Windows, you need to type `UniversalPointerSearcher.exe` followed by the command line flags. On Linux you need to type `./UniversalPointerSearcher` instead.

* Performing a pointer search using 1 initial memory dump and 3 comparison memory dumps:

  ```
  --initial-file-path D:\Cpp\PointerSearcher\test_memory_dumps\mario_kart_8\39CEB148.bin --initial-starting-address 0x30000000 --address-size 4 --comparison-file-path D:\Cpp\PointerSearcher\test_memory_dumps\mario_kart_8\39CF14D8.bin %% D:\Cpp\PointerSearcher\test_memory_dumps\mario_kart_8\39CF14D8.bin %% D:\Cpp\PointerSearcher\test_memory_dumps\mario_kart_8\39CF14D8.bin --comparison-starting-address 0x30000000 %% 0x30000000 %% 0x30000000 --endian big --target-address 39CEB148 39CF14D8 39CF14D8 39CF14D8
  ```

  Note that the `%%` signs separate the memory snapshot file groups respectively. If you don't use them, the pointer searcher will assume you mean 1 memory snapshot with 3 memory snapshot files which would probably yield an error.

* Validating existing pointers against an initial memory dump and a comparison memory dump, also find pointers with (an) additional depth level(s) and write them to a file:

  ```
  --input-memory-pointers-file-path D:\Cpp\PointerSearcher\test_memory_dumps\mario_kart_8\MemoryPointers.txt --initial-file-path D:\Cpp\PointerSearcher\test_memory_dumps\mario_kart_8\39CF14D8.bin --comparison-file-path D:\Cpp\PointerSearcher\test_memory_dumps\mario_kart_8\39CF14D8.bin --comparison-starting-address 0x30000000 --target-address 0x39CF14D8 0x39CF14D8 --initial-starting-address 0x30000000 --address-size 4 --endian big --scan-deeper-by 2 --store-memory-pointers-file-path D:\Cpp\PointerSearcher\test_memory_dumps\mario_kart_8\MemoryPointersUpdated.txt
  ```

  Note that the `MemoryPointers.txt` is a text file containing memory pointers, one per line. An example of such a text file would look like the following:

  ```
  [0x86409AFC] + 0x12E4
  [0x80409AFC] + 0x14
  [[0x86409AFC] + 0x14] + 0x10
  ```

  You can always output all memory pointers found by the pointer searcher into a text file by using the `--store-memory-pointers-file-path` command line option. The file can then be re-used as input for deeper pointer depth scans via `--scan-deeper-by`.
  
* Performing a pointer search by parsing an entire folder:

  ```
  --initial-file-path D:\Cpp\PointerSearcher\test_memory_dumps\smash_switch_2\Dump1 --file-extensions bin --target-address 0x5FDF030420 --address-size 8 --endian little
  ```

  The preconditions for this to work are that all memory snapshot files/modules have to be named by the module name (optional), an underscore (`_`) and the starting address of that memory snapshot. For example: `Heap0000_5F3E000000.bin` or `5F3E000000.bin`. If you want to specify a different file extension, you may use the respective command line option to do so.

* Performing a pointer search with multiple memory snapshot files:

  ```
  --initial-file-path "D:\Cpp\PointerSearcher\test_memory_dumps\super_mario_galaxy_2\SB4E01_DUMP80.bin" "D:\Cpp\PointerSearcher\test_memory_dumps\super_mario_galaxy_2\SB4E01_DUMP90.bin" --initial-starting-address 0x80000000 0x90000000 --address-size 4 --target-address 0x81458EEC 0x81458EEC --comparison-file-path "D:\Cpp\PointerSearcher\test_memory_dumps\super_mario_galaxy_2\SB4E01_DUMP80.bin" "D:\Cpp\PointerSearcher\test_memory_dumps\super_mario_galaxy_2\SB4E01_DUMP90.bin" --comparison-starting-address 0x80000000 0x90000000 --endian big --pointer-offset-range 0x0,0x400 --pointer-depth-range 1,3 --maximum-pointers-printed-count 1000
  ```

* Writing (compressed) pointer maps:

  ```
  --initial-file-path "D:\Cpp\PointerSearcher\test_memory_dumps\super_mario_galaxy_2\SB4E01_DUMP80.bin" "D:\Cpp\PointerSearcher\test_memory_dumps\super_mario_galaxy_2\SB4E01_DUMP90.bin" --initial-starting-address 0x80000000 0x90000000 --address-size 4 --target-address 0x81458EEC --endian big --write-pointer-maps-file-paths "D:\Cpp\PointerSearcher\test_memory_dumps\super_mario_galaxy_2\PointerMap.bin"
  ```

* Reading/Using (compressed) pointer maps for a pointer search:

  ```
  --address-size 4 --target-address 0x81458EEC --read-pointer-maps-file-paths "D:\Cpp\PointerSearcher\test_memory_dumps\super_mario_galaxy_2\PointerMap.bin"
  ```

### Saving/Re-running Searches

Keep in mind that you can write batch/shell scripts in order to save your command line command for re-use later. More information can be found [here](https://bullywiihacks.forumotion.com/t6494-#32192).

## Glossary

For your better understanding of how this tool operates/is meant to be used, here are a few important terms.

* [**Memory pointer**](https://en.wikipedia.org/wiki/Pointer_(computer_programming))

  A memory pointer consists of a base address and a variable number of offsets. An example for a memory pointer may be: *[[[0x86409AFC] + 0x12E4] + 0x199C] + 0x1A30*

  Here, the base address is *0x86409AFC* and the offsets are *0x12E4*, *0x199C* and *0x1A30*.

  If you want to follow a memory pointer manually, you need to look up the base address in the memory, take the value stored there, add the 1st offset to that value, look up the value stored at the result, add the 2nd offset, look up the value stored at that result and so on until all offsets have been processed. The final result will then be the destination address. Hopefully, you landed at the right address. Since following pointers manually is tedious, this application offers ways of doing this automatically via its validation mode.

* **Memory dump**
  
  This is a file representing a direct copy of the RAM from a starting address to an end address. Typically it covers the whole memory range or a more relevant fraction of it.
  
* **Pointer search**
  
  This is the act of finding potentially viable memory pointers based on provided memory dumps.
  
* **Pointer validation**
  
  In this step, all existing memory pointers are checked against all provided comparison memory snapshots. This means that the pointers will be followed on each comparison memory snapshot and if they do not reach the given target address, the respective pointer will be excluded from the final results.
  
* **Address**
  
  In computing, an address is an absolute (hexadecimal) number which uniquely specifies a location in memory.
  
* **Offset**
  
  This is a (hexadecimal) number which is kind of a relative address. It can be added to an address to get somewhere else in memory. Furthermore, offsets in e.g. memory dumps start at 0 but the address at that location may not be 0 (see starting address).
  
* **Starting address**

  This is the address where you started dumping the memory from.
  
* **Target address**

  This is the address you're trying to reach in the pointer search. The amount of provided target addresses must be 1 for each memory snapshot.

* **Memory snapshot file**

  This is a memory dump which has a starting address (and a certain length). The end address is of course given by starting address + length.

* **Pointer address range**

  This is a range consisting of a starting and an end address. By specifying a range, you can exclude collecting address value pairs outside of that particular range.

* **Memory snapshot**

  This is a collection of multiple memory snapshot files which form a complete memory snapshot. On some systems there are different memory regions which have to be dumped specifically, e.g. [`MEM80` and `MEM90`](https://www.youtube.com/watch?v=90NM58D2xYc) on the `Nintendo Wii`. On other systems, there may only be 1 contiguous memory region to be dumped, hence the memory snapshot is the same as that file.

  Consequently, memory snapshot can only have 1 target address when performing a pointer search.

* **[Endian](https://en.wikipedia.org/wiki/Endianness)**

  This is the byte order of all memory snapshot files. The endian is fixed and given by the operating system you dumped the memory from. It can either be little or big endian.

* **Address size**

  Similar to the endian, the address size is fixed on the system you're dumping memory from. On 32-bit systems the address size is 32-bit, on 64-bit systems the address size is 64-bit and so on. When passing a value for the address size, the value has to be 1 (= 8 bit), 2 (= 16 bit), 4 (= 32 bit) or 8 (= 64 bit). Any other values are invalid.

* **Pointer map**

  This is a file containing the address value pairs retrieved from a memory snapshot which have been determined to be viable for a pointer search.

* **[Compressed](https://en.wikipedia.org/wiki/Data_compression) pointer map**

  Simply a pointer map which has furthermore been compressed with a compression algorithm to save disc space.

* **Pointer depth range**

  This is the range of depth levels to search pointers at which is equivalent to the amount of offsets the resulting memory pointers will have. A pointer depth of 3 will e.g. yield a memory pointer with 3 offsets.

  Note:
  Using a depth of e.g. 5 or higher could make your RAM usage "explode" due to many pointers being added to the list so be careful when you modify this setting.
  
* **Pointer offset range**

  This is the range of pointer offsets to consider valid. Any offset which happens to be outside of that range will cause the pointer result to be removed from the list of viable memory pointers. It is a good idea to keep the offset range reasonable low to avoid nonsensical pointers with huge offsets to be outputted.

* **Visited addresses**

  The visited addresses are the addresses which are visited when following a memory pointer. It is a chain of addresses and the amount of addresses is equal to the base address + the amount of pointer offsets. When excluding cycles, duplicated addresses in the visited addresses chain will cause the respective memory pointer to be excluded.

## Anti Virus Problems

If your anti virus marks the pointer searcher as a virus, it's a false positive. The program is protected for security reasons and might cause some anti virus vendors to get worried due to the added protection like packing. There is absolutely no malicious code.

## Bug Reports/Feature Requests

If you find a definite bug or a lacking feature, please create a **detailed** GitHub issue (e.g. with all input and output data as well as error messages if applicable) and I will try to help you out.

## Performance Tips

The more pointer depth you are asking for and the more viable pointers are found for the pointer search, the slower it will take. You can try the following measures to improve the performance:

* Reduce the (upper) pointer depth
* Reduce the pointer offset range size

The pointer searches with many small memory dump files being added as a unit (memory snapshots) could be sped up as well at the pointer map parsing stage by generating a pointer map. Using compression slows the loading down a bit due to decompression but it will save a lot of disk space.

## Contributing

If you appreciate this tool, [feel free to donate any amount of money to me on PayPal](https://www.paypal.me/bullywiiplaza) to support further development and updates.