# kubecf-docs


This repository holds the [KubeCF]() docs.

The `master` branch holds the sources that are compiled with [Hugo].

The website content it is deployed automatically in the `gh-pages` branch by Travis, and no manual intevention is needed.

## Test your changes

After cloning the repo (with submodules), just run `make serve` to test the website locally.

```
    $> git clone --recurse-submodule https://github.com/SUSE/kubecf-docs
    $> cd kubecf-docs
    $> make serve
```

