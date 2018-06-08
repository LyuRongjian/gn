# libFuzzer and ClusterFuzz Integration

ClusterFuzz is a distributed fuzzing infrastructure that automatically
executes libFuzzer powered fuzzer tests on scale.

Googlers can read more [here](https://goto.google.com/clusterfuzz).

## Status Links

* [Buildbot] - status of all libFuzzer builds.
* [ClusterFuzz Fuzzer Status] - fuzzing metrics, links to crashes and coverage
reports.
* [ClusterFuzz libFuzzer Logs] - individual fuzz target run logs.
* [Corpus GCS Bucket] - current corpus for each fuzz target. Can be used to
upload bootstrapped corpus.

## Integration Details

The integration between libFuzzer and ClusterFuzz consists of:

* Build rules definition in [fuzzer_test.gni].
* [Buildbot] that automatically discovers fuzz targets using `gn refs`, builds
fuzz targets with multiple sanitizers and uploads binaries to a GCS bucket.
Recipe is defined in [chromium_libfuzzer.py].
* ClusterFuzz downloads builds and runs fuzz targets continuously.
* Fuzz target run logs are uploaded to [ClusterFuzz libFuzzer Logs] GCS bucket.
* Fuzzing corpus is maintained for each fuzz target in [Corpus GCS Bucket]. Once
a day, the corpus is minimized to reduce number of duplicates and/or reduce
effect of parasitic coverage.
* [ClusterFuzz Fuzzer Status] displays fuzzer runtime metrics as well as
provides links to crashes and coverage reports.


## Corpus
ClusterFuzz uses two corpus types with libFuzzer:

* **Seed** (or **static**) corpus: files manually uploaded by developers.
ClusterFuzz uses these files for fuzzing but doesn't delete/overwrite them.

* **General** (or **working**) corpus: files generated by fuzzers themselves.
These corpus files are frequently modified during fuzzing sessions and can be
deleted during corpus minimization.

A fuzz target has two input corpus directories, seed and general, but its output
goes into general corpus directory. Seed corpus is read-only.


[Buildbot]: https://build.chromium.org/p/chromium.fyi/buildslaves/slave43-c1
[chromium_libfuzzer.py]: https://code.google.com/p/chromium/codesearch#chromium/build/scripts/slave/recipes/chromium_libfuzzer.py
[ClusterFuzz Fuzzer Status]: https://clusterfuzz.com/v2/fuzzer-stats
[ClusterFuzz libFuzzer Logs]: https://console.cloud.google.com/storage/browser/clusterfuzz-libfuzzer-logs
[Corpus GCS Bucket]: https://console.cloud.google.com/storage/clusterfuzz-corpus/libfuzzer
[fuzzer_test.gni]: https://code.google.com/p/chromium/codesearch#chromium/src/testing/libfuzzer/fuzzer_test.gni