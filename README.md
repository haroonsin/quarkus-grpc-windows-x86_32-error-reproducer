# Quarkus gRPC Windows x86_32 resolution reproducer

This is a minimal Quarkus project with one `.proto` source and only the
`quarkus-grpc` extension. Quarkus 3.33.4 manages `grpc-java` 1.81.0.

## Steps to reproduce error

```powershell
./mvnw clean quarkus:generate-code
```

Expected result: the Quarkus bootstrap resolver generates grpc artefacts based on proto file.


Actual Result: Maven tries to download artifacts for windows 32 bit even though im using M3 (macos) 
```text
[INFO] --- quarkus:3.33.4:generate-code (default-cli) @ grpc-windows-x86_32-reproducer ---
Downloading from central: https://repo.maven.apache.org/maven2/io/grpc/protoc-gen-grpc-java/1.81.0/protoc-gen-grpc-java-1.81.0-windows-x86_32.exe
[ERROR] [io.quarkus.bootstrap.resolver.maven.FailAtCompletionErrorHandler] The following errorswere encountered while processing Quarkus application dependencies:
[ERROR] [io.quarkus.bootstrap.resolver.maven.FailAtCompletionErrorHandler] 1)
java.lang.RuntimeException: io.quarkus.bootstrap.resolver.maven.BootstrapMavenException: Failedto resolve artifact io.grpc:protoc-gen-grpc-java:exe:windows-x86_32:1.81.0
```

and fails because Maven Central does not publish that classifier for version
1.81.0. The requested URL is:

```text
https://repo.maven.apache.org/maven2/io/grpc/protoc-gen-grpc-java/1.81.0/protoc-gen-grpc-java-1.81.0-windows-x86_32.exe
```

## Issue report details

- Quarkus platform: 3.33.4
- gRPC Java code generator selected by Quarkus: 1.81.0
- OS/JVM: macos-arm64
- Trigger: `quarkus:generate-code` with a `src/main/proto/*.proto` file
