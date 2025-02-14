# AFL++ Extended Version

## Build

To build the extended AFL++, you need the following dependencies:
- `clang` and `llvm` (version 16)
- `make`

Then, run the following commands:
```bash
make
```
The compiled executables will be in the project root directory.

## Usage

You have to use `afl-cc` or `afl-c++` to compile the program under test, and for the entry file which contains the `LLVMFuzzerTestOneInput` function as the harness (without a typical `main` function), you will need the `-fsanitize=fuzzer` flag.

During compilation, `afl-cc`/`afl-c++` will accumulate the count of basic blocks in the file `/tmp/bb_count`. You will need to remove the file to guarantee correct information during compilation. The index of each basic block will be written to `stdout` as follows:
```text
BB <basic_block_index>: <file_containing_the_bb>:<start_line>:<start_column>
```
You can redirect `stdout` to a file to save the information for reference.

To start fuzzing, you will need to set the environment variable
```bash
exprot AFL_BB_MAP_SIZE=<total_number_of_basic_blocks>
```
where `<total_number_of_basic_blocks>` will be `<max_basic_block_index> + 1`.

The basic block map of a test case `<fuzz_dir>/queue/<test_case_file_name>` will be written to `<fuzz_dir>/bbmap/<test_case_file_name>`. The map saves raw bits where the bit at index `i` is set if the basic block with index `i` is executed in the test case.

## TODOs

- [ ] Automatically remove the `bbmap` directory if it exists.
- [ ] Improves usability.