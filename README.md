# Goal

This repo implements brownout for the deprecated `sklearn` package on PyPI.

# Brownout schedule

The following table shows the dates and time window, where an exception will be
raised if you attempt to install the `sklearn` package from PyPI.

| Dates                             | Window(s)                      |
|-----------------------------------|--------------------------------|
| 2022 November 1st - December 30th | :00-:05 every hour             |
| 2023 January 1st - February 28th  | :00-:10 every hour             |
| 2023 March 1st - April 30th       | :00-:15 every hour             |
| 2023 May 1st - June 30th          | :00-:10 and :30-:40 every hour |
| 2023 July 1st - August 31st       | :00-:15 and :30-45 every hour  |
| 2023 September 1st - October 30th | :00-:20 and :00-50 every hour  |
| 2023 November 1st onwards         | always raise an exception      |

# How to fix the error for the main use cases

- use `pip install scikit-learn` rather than `pip install sklearn`
- replace `sklearn` by `scikit-learn` in your pip requirements files
  (`requirements.txt`, `setup.py,` `setup.cfg`, `Pipfile`, etc ...)
- if the `sklearn` package is used by one of your dependencies
  it would be great if you take some time to track which package uses
  `sklearn` instead of `scikit-learn` and report it to their issue tracker
- as last resort, set the environment variable ,
  `SKLEARN_ALLOW_DEPRECATED_SKLEARN_PACKAGE_INSTALL=True` to avoid this error

# Reason for the deprecation

`sklearn` package on PyPI exists to prevent malicious actors to use the
`sklearn` package, since `sklearn` (the import name) and `scikit-learn` (the
project name) are sometimes used interchangeably. `scikit-learn` is the actual
package name and should be used with pip, e.g. for:
- pip commands: `pip install scikit-learn`
- pip requirement files (`requirements.txt`, `setup.py,` `setup.cfg`,
  `Pipfile`, etc ...)

At the time of writing (October 2022) `sklearn` downloads is about 1/5 of the
`scikit-learn` downloads on PyPI so a lot of people are using it.

There are some edge cases with the way the PyPI `sklearn` package is
implemented:
- `pip install sklearn==1.1.1` will say that the 1.1.1 version does not exist,
  which is confusing. The only available version at the time of writing of
  `sklearn` is 0.0.
- `pip uninstall sklearn` will actually not uninstall `scikit-learn`, you can
  still do `import sklearn` afterwards
- it can be confusing to have both `sklearn` and `scikit-learn` in the `pip
  list` output prompting questions like "why do I have scikit-learn 1.1.1 and
  sklearn 0.0, and what does it even mean"?

# Testing whether a package will be affected by the `sklearn` deprecation

If you want to test whether a package has `sklearn` in its dependencies
independently of the brownout schedule, you can do:

```
SKLEARN_ALLOW_DEPRECATED_SKLEARN_PACKAGE_INSTALL=False \
    pip install package-to-test-goes-here
```

If you get an error that means that the package has `sklearn` in one of its
dependencies. It would be greatly appreciated if you track which package it is,
and if you report it to the appropriate project issue tracker to make them
aware of the `sklearn` deprecation.