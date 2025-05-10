# Summarize contiguous numeric (integer) ranges found on standard input

Here's a simple tool you can use to check whether you have one or more
expected sets of contiguous integers in a file (or some other input).

## Installation

```sh
$ pip install summarize-ranges
```

Works with Python 3.8 through 3.14.

## Options

* `--tsv` print TAB-separated high,low range values.
* `--python` print closed,open Python ranges.
* `--ignore-word-boundaries` (or `--iwb`) match numbers with no respect for
  word boundaries (e.g., match the `33` in `abc33def`).
* `--ignore-negatives` (or `--in`) do not match leading hyphens that would
  otherwise indicate a negative number. So `catch-22` will result in the number
  `22` being collected rather than `-22`.

## Example usage

You can of course redirect a file or more complicated pipeline into
`summarize-ranges`. The examples below all just use `echo` for simplicity.

```sh
# One range is found:
$ echo 3 | summarize-ranges
Range 1: 3

$ echo 3 4 5 | summarize-ranges --human
Range 1: 3 to 5

# TAB-separated output:
$ echo 3 4 5 | summarize-ranges --tsv
3	5

# TAB-separated output for Python:
$ echo 3 4 5 | summarize-ranges --tsv --python
3	6

# Two ranges:
$ echo 3 5 | summarize-ranges
Range 1: 3
Range 2: 5

echo some text 3 more 4 text and 6, 7, 8. | summarize-ranges
Range 1: 3 to 4
Range 2: 6 to 8

# Negative numbers are recognized:
$ echo some text -3 and -4. | summarize-ranges
Range 1: -4 to -3

# But leading hyphens in numbers can be ignored:
$ echo some text -3 and -4. | summarize-ranges --ignore-negatives
Range 1: 3 to 4

# Python style closed-open ranges
$ echo 3 | summarize-ranges --python
Range 1: 3 to 4

$ echo 3 4 5 10 11 | summarize-ranges --python
Range 1: 3 to 6
Range 2: 10 to 12

# Ignore word boundaries:
$ echo some33 text34here and 35. | summarize-ranges --ignore-word-boundaries
Range 1: 33 to 35
```

## Python API

There is a simple Python class, `RangeSummarizer` you can use to build your
own tools.  See `src/summarize_ranges/ranges.py` and the tests in
`test/test_summarizer.py`.

## Testing

Testing is done with [pytest](https://docs.pytest.org/en/stable/) and
[nox](https://nox.thea.codes/en/stable/index.html).

`env PYTHONPATH=src pytest` and `nox` can be used directly. Or, to use the
`Makefile`, install [uv](https://github.com/astral-sh/uv), then `make test`
or `make nox`.

## Possible enhancements

* Allow for `--expect high,low` arguments and exit non-zero if the expected
  range(s) are not found. This could also have a `--strict` option to check
  that there are no additional unexpected ranges found and a `--quiet` option
  to suppress output (though `/dev/null` can just be used).
