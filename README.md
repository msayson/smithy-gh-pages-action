# smithy-gh-pages-action
GitHub Action to generate and deploy API documentation to GitHub Pages, using Smithy models or OpenAPI specification files as input.

API docs will by default be generated at *username*.github.io/*provided-api-doc-filepath*.

If you've configured your account to use a custom domain for GitHub Pages instead of *username*.github.io, then that will be used instead of *username*.github.io.

### Requirements

#### Option 1: Provide a pre-built OpenAPI JSON spec
Use your repository's filepath to the OpenAPI JSON file as the value for the `openapi-json-filepath` input, and do not provide a value for the `gradle-smithy-task-name` input.

Besides providing other required inputs (see Usage), there are no other requirements for your repository.

#### Option 2: Provide a Gradle task that generates an OpenAPI JSON spec
In this mode, your repository must be built using Gradle and provide a Gradle task that generates an OpenAPI JSON spec from your API models.

Provide the name of this Gradle task for the `gradle-smithy-task-name` input, as well as providing other required inputs under Usage.

### Usage
See [action.yml](action.yml)

Add the following permissions to your GitHub workflow, prior to the lines defining workflow jobs.

```yaml
# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write
```

Add the following steps to your GitHub workflow, replacing the input values:

```yaml
- name: Generate API docs and deploy to GitHub Pages
  uses: msayson/smithy-gh-pages-action@v1.1.0
  with:
    # Name of Gradle task to generate OpenAPI JSON spec from Smithy models
    # If not provided, the OpenAPI JSON spec must be provided by your package
    gradle-smithy-task-name: 'REPLACE_WITH_GRADLE_TASK_NAME'
    # Filepath of input OpenAPI JSON spec
    openapi-json-filepath: 'REPLACE_WITH_OPENAPI_JSON_FILEPATH'
    # Filepath to use for generated API docs HTML
    api-docs-filepath: 'REPLACE_WITH_API_DOCS_FILEPATH'
```

#### Workflow example
```yaml
name: Generate API docs and deploy to GitHub Pages

on:
  # Automatically trigger when push to main branch
  push:
    branches: ["main"]
  # Enable running workflow manually from GitHub Actions
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  generate-api-docs:
    name: Generate API Documentation
    runs-on: ubuntu-latest
    steps:
      - name: Generate API docs and deploy to GitHub Pages
        uses: msayson/smithy-gh-pages-action@v1.1.0
        with:
          gradle-smithy-task-name: build
          openapi-json-filepath: build/smithyprojections/consent-management-api-models/source/openapi/ConsentManagementApi.openapi.json
          api-docs-filepath: api/docs.html
```

### Technologies
[yq](https://github.com/mikefarah/yq) is used to convert JSON to YAML, since YAML is the required format for ReDoc, while many tools such as Smithy generate OpenAPI specs with the JSON format.

[ReDoc](https://github.com/Redocly/redoc) is used to automatically generate API documentation from the OpenAPI YAML specification.

[GitHub Actions](https://docs.github.com/en/actions) are used to automatically generate and deploy HTML API documentation to [GitHub Pages](https://pages.github.com/).
