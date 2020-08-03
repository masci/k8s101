# k8s101

This repo contains the source code for the introductory Kubernetes workshop
at [https://masci.github.io/k8s101/](https://masci.github.io/k8s101/).

## Development

Docs are built with [mkdocs](https://www.mkdocs.org/), you can install the
required dependencies with pip:
```
pip install -r requirements.txt
```

Edit Markdown files under the `src/` folder, docs will be built under the
`docs/` folder at the root of the repo. Docs will be built and published
at every merge on the `master` branch.
