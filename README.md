# Reproduction of Poetry bug affecting snyk users

This repository serves as a reproduction of Poetry’s [Poetry export does not include setuptools even when it is a direct dependency. #1584](https://github.com/python-poetry/poetry/issues/1584) bug, which causes `snyk test` to fail with:

```
$ snyk test
Error processing poetry project. Unable to find dependencies in poetry.lock for package: setuptools
```

In effect this makes Snyk unusable for Poetry projects that happen to have `setuptools` in their dependencies, or any other package that Poetry deems is [unsafe to have in the lockfile](https://github.com/python-poetry/poetry/blob/2aab9bcd495e11e5f4491aa72a9510773cc4a90e/poetry/puzzle/provider.py#L50) like `distribute`, `pip`, `wheel`.

In the case of this project, `setuptools` comes from `gunicorn`, which is a pretty common dependency for web development in Python (with Django).

## Reproduction steps

Clone this project and run `snyk test` locally. Alternatively, to set up your own test project:

```sh
mkdir poetry-snyk-bug
poetry new .
poetry add gunicorn
snyk test
```

## Further info

- Poetry: [Poetry export does not include setuptools even when it is a direct dependency. #1584](https://github.com/python-poetry/poetry/issues/1584)
- Snyk: [Support poetry for Python projects #367](https://github.com/snyk/snyk/issues/367)

## Workaround

For now, as a workaround I managed to make Snyk work by manually editing the `poetry.lock`:

```diff
+[[package]]
+category = "main"
+description = "MANUALLY EDITED"
+name = "setuptools"
+optional = false
+python-versions = ">=3.6"
+version = "51.1.2"
```

This defeats the purpose of using Poetry to start with, but might prove useful to others.
