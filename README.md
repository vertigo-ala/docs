## Welcome to Vertigo ALA

This is a proof of concept for a dockerized version of a few ALA modules (Atlas of Living Australia). These repos will serve the purpose of setting a common ground for discussion among ALA mantainers.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### General steps for each module

- Create a basic Dockerfile and implement a docker-compose workflow
- Implement console logging and verbosity
- Avoid code changes whenever possible
- Define a minimal set of externalized configs for the container
- Automate container build (Docker hub auto build and/or Gitlab CI)
- Create a sample project derived from this image (sample custom module)
- Create a helm chart for k8s deployment with self-documented values.yaml

### Implemented modules

- common-ui

