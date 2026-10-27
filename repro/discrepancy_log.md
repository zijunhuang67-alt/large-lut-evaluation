# Discrepancy Log

## D-001: Compiler differs from the documented environment

- Expected/documented: the README specifies Clang with C++20 support.
- Observed: the artifact and OpenFHE were built with GNU g++ 14.2.0 (`/usr/bin/g++`).
- Control: no rebuild or compiler change was performed in this pilot.
- Impact: correctness observations are reported for this GCC environment only; paper performance is not assessed and no Clang/GCC timing comparison is made.

## D-002: Required GNU time executable is absent

- Attempt 1 failed before benchmark launch because /usr/bin/time was absent.
- Command: `OMP_NUM_THREADS=1 timeout 30m /usr/bin/time -v ./build/lut-bench --secure --kind cleartext --input-bits 8 --digit-bits 4 --threads 1 --runs 1 --seed 1`
- Observed: `timeout: failed to run command ‘/usr/bin/time’: No such file or directory`.
- Exit code: `127`.
- Stage: launcher failure before `lut-bench` started.
- Preservation: the original log and first CSV row remain unchanged.
- Controlled resolution: the one authorized retry used Python standard-library `subprocess` and `resource`, retained the same secure arguments, and did not install `time` or change the build or cryptographic parameters.
- Attempt 2 result: `exit_code=0`, `mismatches=0`, `ring_dim=65536`, and `child_max_rss_kb=12457216`.
- Secure verdict: **secure correctness reproduced for this single 8-bit configuration.**
- Performance verdict: **paper performance not assessed**.

## D-003: Artifact dependency differs from the paper implementation context

- Repository statement: the README says this artifact uses the newer OpenFHE v1.6.0 and has better performance than the implementation in the paper.
- Observed artifact dependency: OpenFHE 1.6.0.
- Impact: even a successful pilot would validate this artifact configuration, not establish reproduction of the paper's reported performance.
- Performance verdict: **paper performance not assessed**.
