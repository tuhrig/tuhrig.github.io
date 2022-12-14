---
layout: post
title: "Docker Registry Rest API"
date: "2014-05-21"
categories: 
  - "devops"
tags: 
  - "api"
  - "cloud"
  - "docker"
  - "rest"
---

# The Docker Registry

The Docker registry is Docker's in-build way to share images. It is an open-source project and can be found at [https://github.com/dotcloud/docker-registry](https://github.com/dotcloud/docker-registry) in the official repository of DotCloud. You can set it up on your private server (maybe in the cloud) at push and pull your images to it. You can also secure it, e.g. with SSL and a NGINX (maybe I will write about this later).

# The Rest API

Similar to Docker itself, the registry provides a Rest API to interact with it. Using the Rest API, you can list all images, search or brows a certain repository. The only prerequisite is that you define a search back-end in the registry's `config.yaml`:

common:
    # add this
    search\_backend: sqlalchemy
    sqlalchemy\_index\_database: sqlite:////tmp/docker-registry.db

Now you can use the Rest API like this:

## List a certain repository

GET https://my-private-registry.com/v1/repositories/thomas/busybox/images
\[
  {
    "id": "2d8e5b282c81244037eb15b2068e1c46319c1a42b80493acb128da24b2090739"
  },
  {
    "id": "6c991eb934609424f761d3d0a7c79f4f72b76db286aa02e617659ac116aa7758"
  }
\]

## Search

GET https://my-private-registry:5000/v1/search?q=base
{
   "num\_results": 1,
   "query": "base",
   "results": \[{"description": "", "name": "test/base-img"}\]
}

## Get info to a certain image

GET https://my-private-registry.com/v1/images/2d8e5b282c81244037eb15b2068e1c46319c1a42b80493acb128da24b2090739/json
{
  "id": "2d8e5b282c81244037eb15b2068e1c46319c1a42b80493acb128da24b2090739",
  "parent": "9f4e93171ec525221fa9013d0e21f8690cef68590664eb5249e0b324c5faf31a",
  "created": "2014-04-24T15:59:59.47081913Z",
  ...
}

## List all image

And thanks to [bwilcox](http://stackoverflow.com/users/1766008/bwilcox) from StackOverflow, this is how you can list all images:

GET https://my-private-registry.com/v1/search

Result :
{ 
   "num\_results": 2, 
   "query": "", 
   "results": \[
       {"description": "", "name": "test/base-img"},
       {"description": "", "name": "test/base-test"}\]
}

# More

- [http://stackoverflow.com/questions/23699809/any-api-or-web-ui-project-to-manage-a-docker-private-registry](http://stackoverflow.com/questions/23699809/any-api-or-web-ui-project-to-manage-a-docker-private-registry)
- [https://github.com/dotcloud/docker-registry](https://github.com/dotcloud/docker-registry)
- [http://docs.docker.io/reference/api/registry\_api](http://docs.docker.io/reference/api/registry_api)

**Best regards,** Thomas
