# Quarkus gRPC Windows x86_32 resolution reproducer

This is a minimal Quarkus project with one `.proto` source and only the
`quarkus-grpc` extension. Quarkus 3.33.4 manages `grpc-java` 1.81.0.

## Reproduce on a 32-bit Windows JVM

Run this from the repository root using Maven 3.9+ and a 32-bit Windows JDK:

```powershell
mvn clean quarkus:generate-code
```

Expected result: the Quarkus bootstrap resolver tries to download:

```text
io.grpc:protoc-gen-grpc-java:exe:windows-x86_32:1.81.0
```

and fails because Maven Central does not publish that classifier for version
1.81.0. The requested URL is:

```text
https://repo.maven.apache.org/maven2/io/grpc/protoc-gen-grpc-java/1.81.0/protoc-gen-grpc-java-1.81.0-windows-x86_32.exe
```

The matching `windows-x86_64` executable is published. This project contains
no custom protoc configuration or direct `io.grpc` dependency; the classifier
is selected by Quarkus from the JVM/OS architecture.

## Optional local diagnostic

On a non-Windows machine, this command is useful to inspect the resolution
path, but a real 32-bit Windows JVM is the authoritative reproduction:

```sh
mvn -e -X quarkus:generate-code -Dos.name=Windows -Dos.arch=x86
```

## Issue report details

- Quarkus platform: 3.33.4
- gRPC Java code generator selected by Quarkus: 1.81.0
- OS/JVM: Windows with `os.arch=x86` (32-bit)
- Trigger: `quarkus:generate-code` with a `src/main/proto/*.proto` file
