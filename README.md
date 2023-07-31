![Coverage](https://img.shields.io/badge/Coverage-Lines%2090%25-brightgreen)
```bash
grep -o -P '(?<=coverFile"><a href=")[^"]+|(?<=coverPerLo">)[0-9.]+|(?<=coverPerHi">)[0-9.]+|(?<=coverNumLo">)[0-9]+|(?<=coverNumHi">)[0-9]+' lcov-report/index.html | {
    while read -r directory; do
        read -r line_coverage_per
        read -r line_coverage_high
        read -r function_coverage_per
        read -r function_coverage_high

    directory=${directory//\/index.html/}
    if [[ "$directory" != *"test"* ]] && [[ "$directory" != *"repo_name"* ]]; then
        line_color=red
        if (( $(echo "$line_coverage_per > 80 && line_coverage_per < 90" |bc -l) )); then
        line_color=amber
        elif (( $(echo "$line_coverage_per >= 90" |bc -l) )); then
        line_color=brightgreen
        fi
        function_color=red
        if (( $(echo "$function_coverage_per > 80 && function_coverage_per < 90" |bc -l) )); then
        function_color=amber
        elif (( $(echo "$function_coverage_per >= 90" |bc -l) )); then
        function_color=brightgreen
        fi
        line_badge_url="https://img.shields.io/badge/Line%20Coverage-$line_coverage_per%25-$line_color"
        function_badge_url="https://img.shields.io/badge/Function%20Coverage-$function_coverage_per%25-$function_color"
        # line_coverage=$((line_coverage_low > 0.0 ? line_coverage_low : line_coverage_high))
        # function_coverage=$((function_coverage_low > 0.0 ? function_coverage_low : function_coverage_high))
        echo "$directory: ![Line Coverage]($line_badge_url) ![Function Coverage]($function_badge_url)"

    fi
    done
}
```
# Gcov Example

**Use [Gcov](https://gcc.gnu.org/onlinedocs/gcc/Gcov.html) + [LCOV](http://ltp.sourceforge.net/coverage/lcov.php) / [gcovr](https://github.com/gcovr/gcovr) to show C/C++ projects code coverage results.**

[![pages-build-deployment](https://github.com/shenxianpeng/gcov-example/actions/workflows/pages/pages-build-deployment/badge.svg)](https://github.com/shenxianpeng/gcov-example/actions/workflows/pages/pages-build-deployment) [![Build](https://github.com/shenxianpeng/gcov-example/actions/workflows/build.yml/badge.svg)](https://github.com/shenxianpeng/gcov-example/actions/workflows/build.yml)

This repo shows how to use Gcov to create lcov/gcovr coverage reports for C/C++ projects.

**Code coverage reports online**

* 📄 [LCOV - code coverage report](https://shenxianpeng.github.io/gcov-example/lcov-report/index.html)
* 📄 [gcovr - code coverage report](https://shenxianpeng.github.io/gcov-example/gcovr-report/coverage.html)

Note: The source code is under the `master` branch, and code coverage report under branch `coverage`.

## What problem does Gcov solve

The problem I encountered: A C/C++ project from decades ago has no unit tests, only regression tests. But I want to know:

* What code is tested by regression tests? 
* Which code is untested?
* What is the code coverage? 
* Where do I need to improve automated test cases in the future?

Can code coverage be measured without unit tests? The answer is Yes.

There are some tools on the market that can measure the code coverage of black-box testing, such as Squish Coco, Bullseye, etc but need to be charged. their principle is to insert instrumentation during build.

I tried Squish Coco but I have some build issues are not resolved.

## How Gcov works

Gcov workflow diagram

![flow](img/gcov-flow.jpg)

There are three main steps:

1. Adding special compile options to the GCC compilation to generate the executable, and `*.gcno`.
2. Running (testing) the generated executable, which generates the `*.gcda` data file.
3. With `*.gcno` and `*.gcda`, generate the `gcov` file from the source code, and finally generate the code coverage report.

Here's how each of these steps is done exactly.

## Getting started

You can clone this repository and run `make help` to see how to use it.

```bash
$ git clone https://github.com/shenxianpeng/gcov-example.git
cd gcov-example

$ make help
help                           Makefile help
build                          Make build
coverage                       Run code coverage
lcov-report                    Generate lcov report
gcovr-report                   Generate gcovr report
deps                           Install dependencies
clean                          Clean all generate files
lint                           Lint code with clang-format
```

Steps to generate code coverage reports

```bash
# 1. compile
make build

# 2. run executable
./main

# 3. run code coverage
make coverage

# 4. generate report
# support lcov and gcovr reports
# to make report need to install dependencies first
make deps
# then
make lcov-report
# or
make gcovr-report
```
