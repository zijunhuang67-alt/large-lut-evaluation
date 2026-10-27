# Cloud Environment Lock

Audit date: 2026-10-27 (Asia/Shanghai)

This file records the cloud environment used to build and test the repository. No dependencies were reinstalled, no security parameters were changed, Docker was not invoked, and no `--secure` benchmark was run during this audit.

## 1. Repository

Repository root:

```text
/workspace/large-lut-evaluation
```

`git rev-parse HEAD`:

```text
4b5ba0dc409266dc5f02d0c429085815799cab36
```

`git status --short` before creating this requested report produced no output; the checkout was clean. After saving this report, the only expected status entry is:

```text
?? repro/
```

## 2. OpenFHE

- Installation location: `/workspace/.local/openfhe-1.6.0`
- Version: `1.6.0`
- Git tag: `v1.6.0`
- Full Git commit: `6206d24f9eefefc620b524a4f2b9f308feb91e9f`
- `NATIVE_SIZE`: `64`
- OpenMP: enabled (`OpenFHE_OPENMP=ON`, `WITH_OPENMP` is defined, and the executable links to `libgomp.so.1`)

The full commit was verified by resolving `refs/tags/v1.6.0` with `git ls-remote`. The temporary OpenFHE source checkout is no longer retained under `/tmp`; the installed configuration is retained under the installation location above.

Relevant installed configuration evidence:

```text
PACKAGE_VERSION="1.6.0"
BASE_OPENFHE_VERSION=1.6.0
OpenFHE_CXX_COMPILER_VERSION="14.2.0"
OpenFHE_OPENMP="ON"
OpenFHE_NATIVE_SIZE="64"
OpenFHE_CXX_FLAGS="-Wall -Werror -DOPENFHE_VERSION=1.6.0 -march=native -Wno-parentheses -O3 -DMATHBACKEND=4 -fopenmp"
```

## 3. Build configuration

- `CMAKE_BUILD_TYPE`: `Release`
- Actual C++ compiler: `/usr/bin/g++`
- Compiler identity: GNU C++ `14.2.0` (`g++ (Debian 14.2.0-19) 14.2.0`)
- Project `NATIVEOPT`: `ON`
- OpenFHE `WITH_NATIVEOPT`: `ON`
- OpenFHE prefix used by the project: `/workspace/.local/openfhe-1.6.0`

The effective native-optimization setting is independently visible in the installed OpenFHE flags as `-march=native`. The project cache records `NATIVEOPT:BOOL=ON`.

Complete OpenFHE CMake configuration command used during onboarding:

```sh
/workspace/.tools/bin/cmake \
  -S /tmp/openfhe-v1.6.0-src \
  -B /tmp/openfhe-v1.6.0-build \
  -DCMAKE_C_COMPILER=gcc \
  -DCMAKE_CXX_COMPILER=g++ \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=/workspace/.local/openfhe-1.6.0 \
  -DBUILD_UNITTESTS=OFF \
  -DBUILD_EXAMPLES=OFF \
  -DBUILD_BENCHMARKS=OFF \
  -DNATIVE_SIZE=64 \
  -DWITH_NATIVEOPT=ON
```

Complete project CMake configuration command used during onboarding:

```sh
cd /workspace/large-lut-evaluation
/workspace/.tools/bin/cmake \
  -S . \
  -B build \
  -DCMAKE_CXX_COMPILER=g++ \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_PREFIX_PATH=/workspace/.local/openfhe-1.6.0
```

## 4. Cloud resources

`nproc`:

```text
5
```

`free -h`:

```text
               total        used        free      shared  buff/cache   available
Mem:            33Gi       704Mi        32Gi        52Ki        40Mi        32Gi
Swap:             0B          0B          0B
```

`df -h`:

```text
Filesystem      Size  Used Avail Use% Mounted on
overlay          32G  1.2M   30G   1% /
tmpfs            17G     0   17G   0% /dev
tmpfs           1.0M  4.0K 1020K   1% /run/codex-runtime-secrets
none             17G   36K   17G   1% /tmp
tmpfs            64M     0   64M   0% /dev/tty
tmpfs            17G     0   17G   0% /tmp/codex-daemon-1000
tmpfs            17G     0   17G   0% /workspace/.git
tmpfs            17G     0   17G   0% /workspace/.agents
tmpfs            17G     0   17G   0% /workspace/.codex
tmpfs            17G     0   17G   0% /workspace/.aws
tmpfs            17G     0   17G   0% /tmp/.git
tmpfs            17G     0   17G   0% /tmp/.agents
tmpfs            17G     0   17G   0% /tmp/.codex
tmpfs            17G     0   17G   0% /tmp/.aws
```

## 5. Test rerun

Command:

```sh
ctest --test-dir build --output-on-failure
```

Result:

```text
Internal ctest changing into directory: /workspace/large-lut-evaluation/build
Test project /workspace/large-lut-evaluation/build
    Start 1: cleartext
1/3 Test #1: cleartext ........................   Passed    0.36 sec
    Start 2: plaintext
2/3 Test #2: plaintext ........................   Passed    0.33 sec
    Start 3: ciphertext
3/3 Test #3: ciphertext .......................   Passed    0.43 sec

100% tests passed, 0 tests failed out of 3

Total Test time (real) =   1.13 sec
```
