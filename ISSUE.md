## Quarkus gRPC code generation resolves an unavailable `windows-x86_32` plugin

### Description

With a 32-bit Windows JVM, Quarkus code generation selects the following
artifact for the Java gRPC protoc plugin:

```text
io.grpc:protoc-gen-grpc-java:exe:windows-x86_32:1.81.0
```

Maven Central does not contain that executable, so application dependency
resolution fails before code generation begins.

### Minimal reproducer

This repository contains only:

- Quarkus platform `3.33.4`
- the `quarkus-grpc` extension
- one `src/main/proto/hello.proto` file

There is no custom protoc/plugin configuration and no direct gRPC dependency.

### Steps to reproduce

1. Use Maven 3.9+ on Windows with a 32-bit JDK (`os.arch=x86`).
2. Clone this repository.
3. Run:

   ```powershell
   mvn clean quarkus:generate-code
   ```

### Actual result

```text
Downloading from central: https://repo.maven.apache.org/maven2/io/grpc/protoc-gen-grpc-java/1.81.0/protoc-gen-grpc-java-1.81.0-windows-x86_32.exe
[ERROR] [io.quarkus.bootstrap.resolver.maven.FailAtCompletionErrorHandler]
java.lang.RuntimeException: io.quarkus.bootstrap.resolver.maven.BootstrapMavenException:
Failed to resolve artifact io.grpc:protoc-gen-grpc-java:exe:windows-x86_32:1.81.0
```

### Expected result

Quarkus should either support code generation on 32-bit Windows or fail with a
clear, early message that gRPC Java does not publish a Windows x86_32 protoc
plugin for the managed version. The `windows-x86_64` executable is available.

### Environment

- Quarkus platform: `3.33.4`
- Managed gRPC Java code generator: `1.81.0`
- Maven repository: Maven Central
- OS/JVM: Windows, 32-bit (`os.arch=x86`)
