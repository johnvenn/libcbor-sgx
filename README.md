# [libcbor](https://github.com/PJK/libcbor)

[![CircleCI](https://circleci.com/gh/PJK/libcbor/tree/master.svg?style=svg)](https://circleci.com/gh/PJK/libcbor/tree/master)
[![Documentation Status](https://readthedocs.org/projects/libcbor/badge/?version=latest)](https://readthedocs.org/projects/libcbor/?badge=latest)
[![latest packaged version(s)](https://repology.org/badge/latest-versions/libcbor.svg)](https://repology.org/project/libcbor/versions)
[![codecov](https://codecov.io/gh/PJK/libcbor/branch/master/graph/badge.svg)](https://codecov.io/gh/PJK/libcbor)

**libcbor** is a C library for parsing and generating [CBOR](https://cbor.io/), the general-purpose schema-less binary data format.

## Main features
 - Complete [IETF RFC 8949 (STD 94)](https://www.rfc-editor.org/info/std94) conformance
 - Robust platform-independent C99 implementation
 - Layered architecture offers both control and convenience
 - Flexible memory management
 - No shared global state - threading friendly
 - Proper handling of UTF-8
 - Full support for streams & incremental processing
 - Extensive documentation and test suite
 - No runtime dependencies, small footprint
  
## SGX support
	The forked repo is tailored for CBOR inside SGX. 


## Getting started

### Compile from source

Assume you have installed Intel SGX SDK, and set $(SGX_SDK) as system env by 
"source /opt/intel/sgxsdk/environment" or similar command
depending on the installation folder of SGXSDK in your system.

```bash
git clone https://github.com/johnvenn/libcbor-sgx.git libcbor
cd libcbor && git apply sgx-libcbor.patch && cd ..
=====> NOTE: in this forked repo, the patch is applied, the above 2 steps are only for SGX release
mkdir build && cd build
cmake ../libcbor
make
make install

After all these steps, cbor package is installed in build/package
Set the include path and lib path to CBOR installation folder. 

A SampleCode to run cbor functions inside enclave is provided in SGXSampleCBOR. After the above
steps, you can use the built include files and lib inside this sample.


```

## Usage example

```c
#include <cbor.h>
#include <stdio.h>

int main(void) {
  /* Preallocate the map structure */
  cbor_item_t* root = cbor_new_definite_map(2);
  /* Add the content */
  bool success = cbor_map_add(
      root, (struct cbor_pair){
                .key = cbor_move(cbor_build_string("Is CBOR awesome?")),
                .value = cbor_move(cbor_build_bool(true))});
  success &= cbor_map_add(
      root, (struct cbor_pair){
                .key = cbor_move(cbor_build_uint8(42)),
                .value = cbor_move(cbor_build_string("Is the answer"))});
  if (!success) return 1;
  /* Output: `length` bytes of data in the `buffer` */
  unsigned char* buffer;
  size_t buffer_size;
  cbor_serialize_alloc(root, &buffer, &buffer_size);

  fwrite(buffer, 1, buffer_size, stdout);
  free(buffer);

  fflush(stdout);
  cbor_decref(&root);
}
```

## Documentation
Get the latest documentation at [libcbor.readthedocs.org](http://libcbor.readthedocs.org/)

## Contributions

Bug reports and contributions are welcome. Please see [CONTRIBUTING.md](https://github.com/PJK/libcbor/blob/master/CONTRIBUTING.md) for more info.

Kudos to all the [contributors](https://github.com/PJK/libcbor/graphs/contributors)!

## License
The MIT License (MIT)

Copyright (c) Pavel Kalvoda, 2014-2020

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
