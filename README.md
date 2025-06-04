# taskfile-utils

This project contains a Taskfile collection for different purposes to avoid redundancies in each project.

!!! note

    In order to use remote tasks, since it is still an [experimental feature](https://taskfile.dev/experiments/remote-taskfiles/), please remind to set the `TASK_X_REMOTE_TASKFILES` environment variable:

```
export TASK_X_REMOTE_TASKFILES=1
```

# Package management

## npm

The `npm.yaml` file contains a single task that installs a package, if not present already, via the `npm` CLI, i.e.:

```yaml
version: 3

includes:
  npm_utils: https://raw.githubusercontent.com/Terradue/taskfile-utils/refs/heads/main/npm.yaml

tasks:

  generate_docs:
    desc: Given an input OpenAPI YAML document, creates Redocly human-readable documentation.
    deps:
    - task: npm_utils:conditional_install
      vars:
        PACKAGE: '@redocly/cli'
    cmds:
    - redocly build-docs {{.API}} --output={{.OUTPUT}}
```

## pip

The `pip.yaml` file contains a single task that installs a package, if not present already, via the `pip` CLI, i.e.:

```yaml
version: 3

includes:
  pip_utils: https://raw.githubusercontent.com/Terradue/taskfile-utils/refs/heads/main/pip.yaml

tasks:

  generate_skeleton:
    desc: Given an input OpenAPI YAML document, creates FastAPI server skeleton with Pydantic v2 models.
    deps:
    - task: pip_utils:conditional_install
      vars:
        PACKAGE: fastapi-code-generator
    cmds:
    - fastapi-codegen --input {{.API}} --output {{.OUTPUT}} --output-model-type pydantic_v2.BaseModel

```

# Command execution

## AsyncAPI

The `asyncapi.yaml` file contains tasks to generate contents, given an [AsyncAPI](https://www.asyncapi.com/) document:

### invoke_generator

Allow invoke the AsyncAPI Generator command (it will be installed, if not present), with arbitrary parameters:

- `API`: the reference to the AsyncAPI document, typically a YAML document;
- `TEMPLATE`: the AsyncAPI Generator template, i.e. `@asyncapi/html-template@3.0.0`, see the related [documentation](https://www.asyncapi.com/docs/tools/generator/usage);
- `OUTPUT`: the reference to the directory where the AsyncAPI Generator produces the output file(s);
- `AUX_PARAMETER`: any auxiliary AsyncAPI Generator parameter(s).

### generate_docs

By wrapping the previous command, generates an HTML human-readable document, with arbitrary parameters:

- `API`: the reference to the AsyncAPI document, typically a YAML document;
- `OUTPUT`: the reference to the directory where the AsyncAPI Generator produces the output file(s).

### generate_models

Given an input OpenAPI YAML document, generates [Pydantic v2](https://pydantic.dev/articles/pydantic-v2-final) models, with arbitrary parameters:

- `API`: the reference to the AsyncAPI document, typically a YAML document;
- `OUTPUT`: the reference to the directory where the AsyncAPI Generator produces the output file(s).

## Java

The `java.yaml` file contains tasks to execute a simple Java jar file, by hiding the artifact download from the [Maven Central Repository](https://central.sonatype.com/):

### execute

Download, if not present in the local M2 repo already, and executes an arbitrary artifact identified by the canonical GAV coordinates in the format of `<groupId>:<artifactId>[:<type>[:<classifier>]]:<version>`, with arbitrary parameters:

- `GAV`: the artifact coordinates, i.e. `net.sourceforge.plantuml:plantuml-mit:1.2025.3`.

Optional parameters below are also supported:

- `MAVEN_REPO`: the Maven Central Repository URL, `https://repo1.maven.org/maven2` by default;
- `LOCAL_M2_REPO`: the local File System path where artifacts will be stored, `$HOME/.m2/repository` by default.

## JSON

The `json.yaml` file contains tasks to generate contents, given a [JSON Schema](https://json-schema.org/) document:

### create_models

Given a JSON Schema, generates the related [Pydantic v2](https://pydantic.dev/articles/pydantic-v2-final) models (with field aliases), with arbitrary parameters:

- `SCHEMA`: the reference to a JSON Schema file (it can be expressed as a YAML file).
- `OUTPUT`: the reference to the directory where the AsyncAPI Generator produces the output file(s).

Optional parameters below are also supported:

- `BASE_CLASS`: the [Pydantic v2](https://pydantic.dev/articles/pydantic-v2-final) base class, `pydantic.BaseModel` by default.

### check_instance

Executes a strict JSON Schema check of an instance file against an input schema, with arbitrary parameters:

- `SCHEMA`: the reference to a JSON Schema file (it can be expressed as a YAML file).
- `INSTANCE`: the reference to the instance file that can needs to be checked against the input schema.

## OpenAPI

The `openapi.yaml` file contains tasks to generate contents, given an [OpenAPI](https://www.openapis.org/) document:

### generate_docs

Generates an HTML [Redocly](https://redocly.com/) human-readable document, with arbitrary parameters:

- `API`: the reference to the OpenAPI document, typically a YAML document;
- `OUTPUT`: the reference to the the output file where documentation will be produced.

### generate_skeleton

Given an input OpenAPI document, generates a [FastAPI](https://fastapi.tiangolo.com/) server skeleton with [Pydantic v2](https://pydantic.dev/articles/pydantic-v2-final) models, with arbitrary parameters:

- `API`: the reference to the OpenAPI document, typically a YAML document;
- `OUTPUT`: the reference to the directory where the the output file(s) will be produced.
