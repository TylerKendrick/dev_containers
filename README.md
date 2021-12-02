# Remote Dev Containers

This repository contains a collection of container definitions to use as remote code workspaces in dev containers. A dev container is a running Docker container with a well-defined tool/runtime stack and its prerequisites. A code workspace is a file/folder structure that is used in a runtime environment to develop code.

## Container Layers

A dev container in this repository follows a composition pattern for constructing development environments. These containers build complete images from the following layers: runtimes, sdks, frameworks, toolsets, and IDE integration. An image that is used to build a layer should be uploaded to a repository that is isolated from the composites. An image that is built as a complete composite should be accessible to developers in a common repository. This isolation is meant to prevent developers from accidentally hosting images that aren't supported or approved by the organization.

### Runtimes

All supported runtimes for your organization should be defined as dockerfiles.

#### runtime-\<name>
Runtime images should be prefixed by the string "runtime-" and be followed by the name of the runtime. For example, a container with the java runtime would read "runtime-java".

### SDKs

All supported sdks for your organization should be defined as dockerfiles.

#### sdk-\<name>

SDKs should be prefixed by the string "sdk-" and be followed by the name of the sdk. For example, a container with the java sdk would read "sdk-java".

### Frameworks

All supported frameworks for your organization should be defined as dockerfiles.

#### framework-\<sdk>-\<name>:\<version>-\<os>

Frameworks should be prefixed by the string "framework-" and be followed by the name of the dependent sdk with the string "<sdk>-", then by the name of the framework. For example, a container with the Spring Framework from the Spring Boot Platform for Java would read "framework-java-spring". This is to reduce the chance of naming collisions between frameworks for different languages.

### Toolsets

All supported toolsets for your organization should be defined as dockerfiles.

#### `tool-<name>:<version>-<os>`

#### CLIs

##### `tool-cli-<name>:<version>-<os>`

### IDEs

All supported IDEs for your organization (with remote container support) should be defined as dockerfiles.


## Composite Workspaces

Composite workspaces should be constructed as multi-stage docker builds. These workspaces should be built using each layer, except the ide.

### `<workspace>:<version>-<os>`

```dockerfile
# syntax=docker/dockerfile:1
# runtime layer
FROM dev-containers/runtime-java:latest as runtime
WORKDIR /src/
COPY /runtime ./runtime/java

# sdk layer
FROM dev-containers/sdk-java:latest as sdk
WORKDIR /src/
COPY /sdk ./sdk/java

# framework layer
FROM dev-containers/framework-java-spring:latest as spring-framework
WORKDIR /src/
COPY /framework ./framework/springboot

# tools-cli layer
FROM dev-containers/tools-cli-springboot as tools-cli
WORKDIR /src/
COPY /tools-cli ./tools-cli/springboot

```
## Composite Codespaces

### `<codespace>:<version>-<os>`

Example eclipse codespace:
```dockerfile
# syntax=docker/dockerfile:1
FROM workspaces/java-springboot:latest as workspace
COPY /src ./src

FROM dev-containers/ide-eclipse:latest as ide
WORKDIR /src/
COPY /ide ./ide
RUN go eclipse ./ide

```

Example vscode codespace:
```dockerfile
# syntax=docker/dockerfile:1
FROM workspaces/java-springboot:latest as workspace
COPY /src ./src

FROM dev-containers/ide-vscode:latest as ide
WORKDIR /src/
COPY /ide ./ide
RUN go vscode ./ide
```