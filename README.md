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

### Understanding docker-compose workflow

Docker-compose is a tool that simplifies development of multi-container projects (pretty much all of them). That goes from the simple webapp + database case to multi-modules solutions like ALA itself.

The typical journey of any developer pulling the project for the first time is:

```
docker-compose up --build
```

This command will build the dockerfile and start the containers as dictated by `docker-compose.yml` (mounts, networks, service names, etc.). A developer can therefore make sure the project runs automagically in any case, regardless of developers' platform.

Further refinement can enable class hot-reloading, so that code maintenance is done over a live container.

### Implemented modules

- commonui-bs3
- commonui-sample
- image-service
- image-sample

### Modules' comments

#### commonui-bs3

This module gained a simple Dockerfile and a docker-compose workflow - it runs the same commonui-bs3 static web module that is live at "<https://www.ala.org.au/commonui-bs3>". It demonstrates the most basic techniques for a container project, but the produced image itself is almost useless: instead of running your own replica it is easier to just point to the live URL in your configs. When creating a custom commonui you are most likely to produce a static server image from scratch.

In brazilian ALA, for example, we built a single image with both commonui-bs3 and commonui-bs2 contexts - some modules use different commonui versions for static content (including javascript). Sweden bioatlas mounts a static local folder (containing bs2 and bs3 files) in a vanilla nginx image.

#### commonui-sample

We need self-contained images for a portable k8s deployment, but we can't be sure of what files must be hand-picked from the original commonui-* projects. In order to keep this project small the entire commonui-bs3 and commonui-bs2 content is downloaded at build time (see Dockerfile), but overwritten by this project's files. This reduces the amount of mantained files.

Docker-compose workflow makes an interesting trick replacing the default command in a developer's machine: the versioned pages are mounted in a staging folder and copied into the web server folder every 2 seconds. Kinda lame but it works.

#### image-service

This is the image-service module image packaged as a reusable asset for any ALA deployment.

Again docker-compose is used for local build and development. The image contains a minimal `image-config.properties` that defaults to:

- Australia CAS login
- Bypass CAS security
- Postgres database at jdbc:postgresql://pgdbimage/images (side container)
- Elasticsearch running on a side container
- Home at <http://localhost:8080/images/>
- Admin at <http://localhost:8080/images/admin>
- Tomcat itself playing as image-store at <http://localhost:8080/store>

Bypassing security with CAS enabled is required for admin pages to work for any user for most ALA modules (good for local development).

#### image-sample

This is a sample deployment of the reusable image-service container with a custom properties file. A custom commonui runs in a side container and image-store is played by a dedicated nginx container.

Again docker-compose is used for local build and development. The image contains a minimal `image-config.properties` that defaults to:

- Australia CAS login
- Bypass CAS security
- Postgres database at jdbc:postgresql://pgdbimage/images (side container)
- Elasticsearch running on a side container
- Commonui runs in a side container (`vertigoala/commonui-sample`) at <http://localhost:8000/commonui-bs3>
- Home at <http://localhost:8080/images/>
- Admin at <http://localhost:8080/images/admin>
- Image-store at <http://localhost:8001/store>
