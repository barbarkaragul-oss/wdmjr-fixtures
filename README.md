# wdmjr-fixtures

Private scratch repository that records what GitHub Actions actually does, so that
[Why Didn't My Job Run?](https://github.com/barbarkaragul-oss/why-didnt-my-job-run) can be tested
against ground truth instead of against what the documentation seems to say.

Every workflow here dumps `toJSON(github)` in a job that always runs; the simulator's fixture
recorder fetches the run logs and each job's conclusion through the API and stores them as test
fixtures. Nothing in this repository is a product.
