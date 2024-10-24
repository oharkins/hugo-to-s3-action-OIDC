# Deploy Hugo To S3 Action OIDC

This GitHub action makes it easy to build and deploy any Hugo static website to AWS S3 with just one step using OIDC.

You will need to set up your aws instance to use Github OIDC.
[Configuring OIDC in AWS](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)


## Example

```yaml
---
name: Hugo Build and Deploy to S3

on:
  workflow_dispatch:
  push:
    branches:
      - master
  pull_request:
    branches:
      - master

permissions:
  id-token: write
  contents: read

jobs:
  build:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
      - name: Check out master
        uses: actions/checkout@master

      - name: Build and deploy
        uses: oharkins/hugo-to-s3-action-oidc@v0.0.1
        with:
          hugo-version: 0.136.5
          pipeline-execution-role: <your execution role>
          aws-region: us-west-2
```

## Custom Config File

Optional `config` to specify a custom config file. Handy for Hugo sites that have more than one config file.

### Example

#### GitHub Action

```yaml
---
name: Hugo Build and Deploy to S3

on:
  push:
    branches:
      - master
  pull_request:
    branches:
      - master
  workflow_dispatch:

permissions:
  id-token: write
  contents: read
  
jobs:
  build:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
      - name: Check out master
        uses: actions/checkout@master

      - name: Build and deploy
        uses: AlbertMorenoDEV/deploy-hugo-to-s3-action@v0.0.1
        with:
          hugo-version: 0.136.5
          config: path/config.toml
          pipeline-execution-role:  <your execution role>
          aws-region: us-west2
```

## Targets

Optional `input` to specify a deployment target. Handy for Hugo sites that have multiple environments (e.g. "staging" and "production").

If `target` is not specified in your GitHub Action, the first deployment target is used by default.

### Example

#### GitHub Action

```yaml
name: Hugo Build and Deploy to S3

on:
  workflow_dispatch:
  push:
    branches:
      - master
  pull_request:
    branches:
      - master

permissions:
  id-token: write
  contents: read

jobs:
  build:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
      - name: Check out master
        uses: actions/checkout@master

      - name: Build and deploy
        uses: oharkins/hugo-to-s3-action-oidc@v0.0.1
        with:
          hugo-version: 0.136.5
          target: production
          pipeline-execution-role:  <your execution role>
          aws-region: us-west2
```

#### Hugo Config

```yaml
---
baseURL: /
languageCode: en-us
title: Example Site

# -------------- AWS S3 Deployment Configs -------------- #

deployment:
  targets:
    - name: "staging"
      URL: "s3://stg.example.com?region=us-west-2"
    - name: "production"
      URL: "s3://example.com?region=us-west-2"
```