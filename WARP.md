# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project overview

This is a single-module Maven project (`java3d-examples`) that packages assets and a small Java helper for Java 3D example programs.

Key characteristics:
- Java 8 project (`maven-compiler-plugin` targets `1.8`).
- Maven coordinates: `org.jogamp.java3d:java3d-examples:1.7.2`.
- Depends on sibling artifacts `java3d-core` and `java3d-utils` at the same groupId and version.
- Primary purpose is to provide example assets (geometry, textures, audio) and a helper class for loading them.

Top-level references from `README.md`:
- Licensing details are in `COPYRIGHT.txt`, `LICENSE.txt`, and `THIRDPARTY-LICENSE-*.txt` in the repo root.
- Related projects: `java3d-core` (core 3D API), `vecmath` (math library), and `java3d-utils` (utilities). For broader setup instructions, consult the `java3d-core` README in that project.

## Code and resource layout

- Java source:
  - `src/main/java/org/jdesktop/j3d/examples/Resources.java`
    - Static utility for resolving example resources.
    - First tries the classpath (`ClassLoader.getResource(filename)`).
    - If not found, falls back to the local filesystem under:
      - `${user.dir}/src/` + `filename`, then
      - `${user.dir}/src/main/java/org/jdesktop/j3d/examples/` + `filename`.
    - This allows examples to find assets both when run from an installed JAR and directly from a checkout.

- Runtime assets:
  - `src/main/resources/audio/` — audio clips used by examples.
  - `src/main/resources/geometry/` — OBJ/LWO/CG meshes and related material/intensity files.
  - `src/main/resources/images/` — textures, backgrounds, and animation image sequences.

There are currently no Java sources under `src/test/java`, so the project has no unit tests defined in this repository.

## Build, install, and packaging

This is a standard Maven build with `pom.xml` at the repo root.

Common commands (run from the project root):

- Clean and build the project, producing the JAR in `target/`:
  - `mvn clean install`

- Build without running tests (useful once tests exist):
  - `mvn clean install -DskipTests`

The default Maven goal configured in the POM is `install`, so a bare `mvn` from the project root will also build and install to the local Maven repository.

## Tests

No test sources are present, but once tests are added under `src/test/java`, the standard Maven lifecycle applies:

- Run the full test suite:
  - `mvn test`

- Run a single test class (replace `ExampleTest` with the class name, without `.java`):
  - `mvn -Dtest=ExampleTest test`

- Run a single test method in a class (replace with your class and method names):
  - `mvn -Dtest=ExampleTest#methodName test`

## Linting, formatting, and docs

The POM does not currently configure any dedicated code style or static analysis plugins. If you add tools like Checkstyle, SpotBugs, or formatters, prefer wiring them into the Maven build and update this file with the exact commands.

The project does configure these documentation-related plugins:
- `maven-source-plugin` — attaches a sources JAR during the build.
- `maven-javadoc-plugin` — attaches a Javadoc JAR with doclint disabled (`-Xdoclint:none`).

You normally do not need to run these directly; they are executed as part of the standard Maven lifecycle when installing or deploying the artifact.

## Working with resources in code

When adding new example code that needs to load assets from this project, prefer using `org.jdesktop.j3d.examples.Resources`:
- Place new assets under `src/main/resources` in an appropriate subdirectory.
- Load them by their classpath-relative filename (e.g., `images/bg.jpg`) via `Resources.getResource("images/bg.jpg")`.
- The helper will allow your code to run both from an IDE using a checkout and from the built JAR, without hardcoding absolute paths.
