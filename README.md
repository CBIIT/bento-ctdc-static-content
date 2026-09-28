# bento-ctdc-static-content

## Overview

This repository stores static content for the CTDC project. Content is managed
through environment-specific branches for development, QA, stage, and
production.

## Environment Branches

| Environment | Linked branch |
| --- | --- |
| CTDC-DEV | `develop` |
| CTDC-QA | `qa` |
| CTDC-STAGE | `stage` |
| CTDC-PROD | `production` |

> **Note:** Environment branches are protected. Do not commit directly to
> `develop`, `qa`, `stage`, or `production`.

## Contribution Workflow

1. Update your local `develop` branch.
2. Create a feature branch from `develop`, for example `XYZ`.
3. Make and commit your changes on the feature branch.
4. Push the feature branch to GitHub.
5. Open a pull request from the feature branch into `develop`.
6. Request at least one reviewer and wait for approval.
7. After the pull request is merged, delete the feature branch if it is no longer needed.

## Promotion Flow

Static content changes are promoted through environment branches:

```text
feature branch (XYZ) --PR--> develop --PR--> qa --PR--> stage --PR--> production
```

## Content Areas

### About Page Content

`aboutPagesContent.yaml` controls CTDC informational pages. Each entry maps to a frontend route with the `page` key.

### Site-Wide Banner Content

`banners/banner_content.yaml` controls the site-wide banner text, visibility, and style.

### Login Page Content

`login/loginView.yaml` controls the `/user/login` page content. Related assets live under `login/assets/`.

See the login page editing and configuration instructions:

- [login/README.md](login/README.md)
