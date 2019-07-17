## Welcome to Vertigo ALA

This is a proof of concept for a dockerized version of a few ALA modules (Atlas of Living Australia). These repos will serve the purpose of setting a common ground for discussion among ALA mantainers.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### General steps for each module

- Fork the module
- Create a basic Dockerfile and implement a docker-compose workflow
- Implement console logging and verbosity
- Avoid code changes whenever possible
- Define a minimal set of externalized configs for the container
- Automate container build (Docker hub auto build and/or Gitlab CI)
- Create a sample project derived from this image (sample custom module)
- Create a helm chart for k8s deployment with self-documented values.yaml

### Implemented modules

- commonui-bs3
- commonui-sample
- image-service
- image-sample

### Modules' comments

#### commonui-bs3

This module gained a simple Dockerfile and a docker-compose workflow. It demonstrates the most basic techniques for a container project, but the produced image itself is almost useless: you are most likely to produce a static commonui web server image from scratch.

In brazilian ALA, for example, we built a single image with both commonui-bs3 and commonui-bs2 contexts - some modules use different commonui versions for static content (including javascript). Sweden bioatlas mounts a static local folder (containing bs2 and bs3 files) in a vanilla nginx image.

#### commonui-sample

We need self-contained images for a portable k8s deployment, but we can't be sure of what files must be hand-picked from the original commonui-* projects. In order to keep this project small the entire commonui-bs3 and commonui-bs2 content is downloaded at build time, but overwritten by this project's files.

Docker-compose workflow makes an interesting trick replacing the image default command in a developer's machine: only new pages are versioned, but they are mounted in a staging folder and copied into the web server folder.

#### image-service

This is the image-service module image and docker-compose for local development. The image contains a minimal `image-config.properties` that defaults to:

- Australia CAS login
- Bypass CAS security
- Postgres database at jdbc:postgresql://pgdbimage/images (side container)
- Elasticsearch running on a side container
- Home at http://localhost:8080/images/

Bypassing security with CAS enabled is required for admin pages to work for any user for most ALA modules (good for local development).

#### image-sample

This is a sample deployment of the reusable image-service container with a custom properties file. A custom commonui runs in a side container.
