# Fork notes

This fork of [acmeair/acmeair](https://github.com/acmeair/acmeair) keeps the application exactly as
it was at commit `f16122729873ef0449ea276dfb2d2a1d45bebb40` (2015-08-17), the version used in studies
of monolith-to-microservice decomposition (for example Sellami and Saied, 2025, MonoEmbed, and
Weerasinghe et al., 2026), and updates only its build, so that it runs with a current JDK. It is the
AcmeAir of the MonoSplit study (controlled experiment on LLM decompositions of Java monoliths).

## What changed

- **Gradle wrapper: 2.6 → 9.8.2.** Gradle 2.6 runs only on Java 8 or older, and current Gradle
  versions do not accept the old build script.
- **`build.gradle`:** the `compile` configuration, removed in Gradle 7, became `api` and
  `implementation` (java-library plugin); the Java EE 7 API is `compileOnly`, since the application
  server provides it; the custom `wrapper` task was removed; the sources are compiled for Java 8
  (`--release 8`), the platform of the original code, so the class files are Java 8 class files.
- **`settings.gradle`:** the WebSphere eXtreme Scale data service (`acmeair-services-wxs`) is built
  only with `-Pwxs`, because its dependency, IBM's objectgrid client 8.6.0.2, is not in a public
  repository (install it in the local Maven repository first). Without it, the war packages the
  MongoDB (Morphia) data service, one of the two deployments of the original
  (`gradlew -Pservice=morphia build`).
- **Repositories:** Maven Central only; the local Maven repository only for the wxs service.

## What did not change

- No Java source, resource, web or configuration file of the application:
  `git diff f16122729873ef0449ea276dfb2d2a1d45bebb40 -- '*.java'` is empty.
- The versions of the dependencies: javaee-web-api 7.0, json-simple 1.1.1, mongo-java-driver 2.13.0
  and morphia 0.110.
- `acmeair-reporter`, a separate Maven project for performance reports, is not part of the build,
  as in the original.

## Build

JDK 17 or later (the study uses Eclipse Temurin 21):

```
./gradlew build          # all modules except wxs; war in acmeair-webapp/build/libs
./gradlew classes        # compile only
```

The wrapper checks the SHA-256 of the Gradle distribution it downloads
(`gradle/wrapper/gradle-wrapper.properties`).
