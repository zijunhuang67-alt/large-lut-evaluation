# Security-Parameter Correctness Pilot

## Scope

- Task type: author artifact rerun, limited to a security-parameter correctness pilot.
- Target: *Evaluating Larger Lookup Tables using CKKS*.
- Artifact commit: `4b5ba0dc409266dc5f02d0c429085815799cab36`.
- OpenFHE: version `1.6.0`, installed at `/workspace/.local/openfhe-1.6.0`.
- Excluded: paper-wide reproduction, performance comparison, algorithm changes, security-parameter changes, Docker, dependency installation, 12/16/20-bit runs, and an experiment matrix.

The frozen environment and build configuration are recorded in `environment_lock.md`. Complete commands and console outputs are retained in `commands.log` and `logs/`.

## Environment boundary

The artifact was built with `/usr/bin/g++`, GNU C++ 14.2.0, while the repository README specifies Clang with C++20 support. Both the project and OpenFHE were built in `Release` mode with native CPU optimization enabled. This compiler difference prevents treating timings as paper-comparable performance evidence. The correctness verdicts below apply only to this observed GCC environment.

## Preflight

- `git rev-parse HEAD` matched the requested commit.
- `cmake --build build -j2` completed successfully.
- `ctest --test-dir build --output-on-failure` passed 3/3 tests.
- Before the secure attempt, approximately 32 GiB memory and 30 GiB root-filesystem space were available.

The CTest cases are toy-parameter checks and are not secure-run evidence.

## Toy result

Classification: **toy / insecure / correctness-only**.

The single requested toy run completed with:

```text
mode=toy (insecure)
input_bits=8
digit_bits=4
ring_dim=256
threads=2
runs=1
seed=1
checked=256
mismatches=0
exit_code=0
```

Verdict: **toy correctness reproduced**.

## Secure 8-bit pilot

Attempt 1 used the originally requested GNU time wrapper:

```sh
OMP_NUM_THREADS=1 timeout 30m /usr/bin/time -v \
  ./build/lut-bench \
  --secure \
  --kind cleartext \
  --input-bits 8 \
  --digit-bits 4 \
  --threads 1 \
  --runs 1 \
  --seed 1
```

It exited with code 127 before `lut-bench` started because `/usr/bin/time` is absent:

```text
timeout: failed to run command ‘/usr/bin/time’: No such file or directory
```

Attempt 1 failed before benchmark launch because `/usr/bin/time` was absent. Its log and `exit_code=127` CSV row are preserved unchanged.

The single authorized retry used Python standard-library `subprocess` with a 1,800-second timeout and `resource.getrusage(resource.RUSAGE_CHILDREN)`. It used the same benchmark and cryptographic arguments; no dependency, build, source, or parameter was changed.

Attempt 2 produced these runtime-observed values:

```text
mode=secure
input_bits=8
digit_bits=4
lut_kind=cleartext
threads=1
runs=1
seed=1
ring_dim=65536
scale_bits=48
logQP=1404.000000
cleaning=0
h=192
large_digits=4
setup_s=15.125217
lut_preparation_s=0.000003
evaluation_s=36.362695
checked=65536
mismatches=0
exit_code=0
wrapper_elapsed_s=51.94705339300003
child_max_rss_kb=12457216
```

The `mode=secure` path completed setup without a security-bound error, and all 65,536 decrypted outputs matched. The sparse encapsulation modulus value is not printed by the executable; the evidence for its check is successful completion of the artifact's unchanged secure setup path.

Verdict: **secure correctness reproduced for this single 8-bit configuration.**

## Performance scope

The toy timing is not a secure result. Attempt 2 is a single correctness pilot in a GCC environment that differs from the documented Clang environment; it is not a paper performance reproduction or comparison.

Verdict: **paper performance not assessed**.
