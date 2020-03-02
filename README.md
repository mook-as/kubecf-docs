# kubecf-docs


This repository holds the [KubeCF](https://github.com/cloudfoundry-incubator/kubecf) docs.

The `master` branch holds the sources that are compiled with [Hugo](https://gohugo.io/).

The website content it is deployed automatically in the `gh-pages` branch by Travis, and no manual intevention is needed.

## Test your changes

After cloning the repo (with submodules), just run `make serve` to test the website locally.

```
    $> git clone --recurse-submodule https://github.com/cloudfoundry-incubator/kubecf-docs
    $> cd kubecf-docs
    $> make serve
```

To run the website locally in other platforms, e.g. MacOS:

```
    $> HUGO_PLATFORM=macOS-64bit make serve
```
