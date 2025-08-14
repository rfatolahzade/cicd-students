# ArtifactGuide

Artifacts have one clear purpose: persist job outputs for:   

 - Passing data between jobs  
 - Manual download (via Pipelines UI)  
 - GitLab Pages (special case)

This project demonstrates how to use artifacts and caches in GitLab CI/CD pipelines to optimize workflows and pass data between jobs.
Project Structure:
```bash
root@NL:/Projects/artifactguide# tree
.
├── README.md
├── requirements.txt     # Just contained numpy==2.3.2 package
└── scripts
    ├── analyze_data.py  # Data analysis script like mean,avg,max,min..
    ├── generate_data.py # Data generation script
    └── index.js

2 directories, 5 files
```
Our pipeline consists of three stages:

 - setup: Install dependencies
 - generate: Create sample data
 - analyze: Process and analyze data

The pipeline:
```bash
image: python:3.13.6-alpine3.21

stages:
  - setup
  - generate
  - analyze

cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - .cache/pip/

install-deps:
  stage: setup
  before_script:
    - pip install --upgrade pip
  script:
    - pip install -r requirements.txt --cache-dir .cache/pip
  cache:
    policy: push

generate-data:
  stage: generate
  script:
    - mkdir -p data  # Ensure directory exists
    - python scripts/generate_data.py
  artifacts:
    paths:
      - data/output.json
    expire_in: 1 hour
  cache:
    policy: pull

analyze:
  stage: analyze
  script:
    - python scripts/analyze_data.py
  dependencies:
    - generate-data
  artifacts:
    paths:
      - report/
    when: always

```
How it works:
 - generate-data job creates data/output.json
 - GitLab saves this file as an artifact
 - analyze job automatically downloads the artifact before running
 - Analysis script processes the data file


Cache policies used:
| Policy   |  Description | 	Used in  |
|---     |---     |---    |
|push | Uploads cache after job | install-deps |
|pull | Fetch cache before job | generate-data, analyze |

Other sample ,I'm going to demonstrates a simple GitLab Pages setup for hosting static content, including a resume PDF and HTML page.
```bash
image: busybox

pages:
  stage: deploy
  script:
    - echo "The site will be deployed to $CI_PAGES_URL"
  artifacts:
    paths:
      - public
```
tree  of the project:
```bash
root@NL:/Projects/rfatolahzade.gitlab.io# tree
.
├── README.md
└── public
    ├── Ramin_Fathollahzadeh_DevOps_CV.pdf
    └── index.html

2 directories, 3 files
```
the index.html contained:
```bash
root@NL:/Projects/rfatolahzade.gitlab.io# cat public/index.html
<!DOCTYPE html>
<html>
<head>
    <title>My GitLab Page</title>
</head>
<body>
    <h1>Hello World!</h1>
    <p>Deployed via GitLab Pages</p>

    <!-- Add this link -->
    <p>📄 Download my CV: <a href="Ramin_Fathollahzadeh_DevOps_CV.pdf">PDF Version</a></p>
</body>
```
How it works:
- The public directory is published as artifacts
- GitLab Pages automatically serves content from this directory
-The PDF is directly accessible via the link in index.html

NOTICE: 
- Your project must be named YOURUSERNAME.gitlab.io for the base URL to work
- After successful deployment, Visit https://YOURUSERNAME.gitlab.io (Pipeline logs will show: The site will be deployed to https://YOURUSERNAME-gitlab-io-CI_PAGES_URL.gitlab.io)

Also you could add a CNAME record pointing to YOURUSERNAME.gitlab.io (notice in notice: ur repo should be public)
