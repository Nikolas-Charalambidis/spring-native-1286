# Spring Native issue [#1286](https://github.com/spring-projects-experimental/spring-native/issues/1286)

This repository is a Spring Boot project built on top of the `2.6.1` parent version. 
It is a reprocution of Spring Native issue [#1286](https://github.com/spring-projects-experimental/spring-native/issues/1286) describing a native Docker image using Packeto (that uses GraalVM) cannot be built. 

The repository contains a bunch of dependencies that might or might not be relevant (I reproduced with them). The most relevant dependencies are:
- `org.springframework.boot`:`spring-boot-starter-data-r2dbc`
- `org.springframework.boot`:`spring-boot-starter-webflux`
- `org.springframework.boot`:`spring-boot-starter-actuator`
- `org.springframework.experimental`:`spring-native` (from `org.springframework.experimental`) of version `0.11.0` that is compatible with Java 17
- `io.r2dbc`:`r2dbc-postgresql`

## Prerequisites
- Java 17 is set properly as `JAVA_HOME`. The command `java -version` results in the following or similar:

   ```
   openjdk version "17" 2021-09-14
   OpenJDK Runtime Environment (build 17+35-2724)
   OpenJDK 64-Bit Server VM (build 17+35-2724, mixed mode, sharing)
   ```
- Optional: Maven is installed, alternatively use the included Maven Wrapper. 
Actually, an exact version of Maven is not that important as long as it is able to work with Java 17. 
Verify version with `mvn -v` where Java 17 shall be displayed.

## Steps
1. Execute the following command (Windows):
   ```
   ./mvnw.cmd spring-boot:build-image -Pnative-docker-image -DskipTests
   ```
2. Boom! Enjoy the error.

## Expected behavior

The native Docker image is created.

## Actual behavior

The build results in the error below. The very same error occured in the Github Actions build [#5](https://github.com/Nikolas-Charalambidis/spring-native-1286/runs/4494241258?check_suite_focus=true).

```
[INFO]     [creator]     [/layers/paketo-buildpacks_native-image/native-image/sandbox.Application:183]    classlist:   6,694.82 ms,  0.94 GB
[INFO]     [creator]     [/layers/paketo-buildpacks_native-image/native-image/sandbox.Application:183]        (cap):   1,798.10 ms,  0.94 GB
[INFO]     [creator]     [/layers/paketo-buildpacks_native-image/native-image/sandbox.Application:183]        setup:   5,766.55 ms,  0.94 GB
[INFO]     [creator]     [/layers/paketo-buildpacks_native-image/native-image/sandbox.Application:183]     analysis:  12,186.03 ms,  1.50 GB
[INFO]     [creator]     Fatal error:com.oracle.graal.pointsto.util.AnalysisError$ParsingError: Error encountered while parsing org.springframework.beans.TypeConverterDelegate.convertIfNecessary(java.lang.String, java.lang.Object, java.lang.Object,
 java.lang.Class, org.springframework.core.convert.TypeDescriptor)
[INFO]     [creator]     Parsing context:
[INFO]     [creator]        at org.springframework.beans.TypeConverterDelegate.convertIfNecessary(TypeConverterDelegate.java:119)
[INFO]     [creator]        at org.springframework.beans.TypeConverterSupport.convertIfNecessary(TypeConverterSupport.java:73)
[INFO]     [creator]        at org.springframework.beans.TypeConverterSupport.convertIfNecessary(TypeConverterSupport.java:45)
```
...
```
[INFO]     [creator]     Caused by: com.oracle.graal.pointsto.util.AnalysisError: parsing had failed in another thread
[INFO]     [creator]            at com.oracle.graal.pointsto.util.AnalysisError.shouldNotReachHere(AnalysisError.java:153)
[INFO]     [creator]            at com.oracle.graal.pointsto.meta.AnalysisMethod.ensureGraphParsed(AnalysisMethod.java:656)
[INFO]     [creator]            at com.oracle.graal.pointsto.phases.InlineBeforeAnalysisGraphDecoder.lookupEncodedGraph(InlineBeforeAnalysis.java:182)
[INFO]     [creator]            at jdk.internal.vm.compiler/org.graalvm.compiler.replacements.PEGraphDecoder.doInline(PEGraphDecoder.java:1120)
```
...
```
[INFO]     [creator]     Caused by: org.graalvm.compiler.java.BytecodeParser$BytecodeParserError: com.oracle.graal.pointsto.constraints.UnresolvedElementException: Discovered unresolved method during parsing: org.springframework.beans.BeanUtils$Kot
linDelegate.instantiateClass(java.lang.reflect.Constructor, java.lang.Object[]). To diagnose the issue you can use the --allow-incomplete-classpath option. The missing method is then reported at run time when it is accessed the first time.
[INFO]     [creator]            at parsing org.springframework.beans.BeanUtils.instantiateClass(BeanUtils.java:196)
[INFO]     [creator]            at jdk.internal.vm.compiler/org.graalvm.compiler.java.BytecodeParser.throwParserError(BytecodeParser.java:2624)
[INFO]     [creator]            at com.oracle.svm.hosted.phases.SharedGraphBuilderPhase$SharedBytecodeParser.throwParserError(SharedGraphBuilderPhase.java:107)
[INFO]     [creator]            at jdk.internal.vm.compiler/org.graalvm.compiler.java.BytecodeParser.iterateBytecodesForBlock(BytecodeParser.java:3485)
```
...
```
[INFO]     [creator]     Caused by: com.oracle.graal.pointsto.constraints.UnresolvedElementException: Discovered unresolved method during parsing: org.springframework.beans.BeanUtils$KotlinDelegate.instantiateClass(java.lang.reflect.Constructor, ja
va.lang.Object[]). To diagnose the issue you can use the --allow-incomplete-classpath option. The missing method is then reported at run time when it is accessed the first time.
[INFO]     [creator]            at com.oracle.svm.hosted.phases.SharedGraphBuilderPhase$SharedBytecodeParser.reportUnresolvedElement(SharedGraphBuilderPhase.java:307)
[INFO]     [creator]            at com.oracle.svm.hosted.phases.SharedGraphBuilderPhase$SharedBytecodeParser.handleUnresolvedMethod(SharedGraphBuilderPhase.java:298)
[INFO]     [creator]            at com.oracle.svm.hosted.phases.SharedGraphBuilderPhase$SharedBytecodeParser.handleUnresolvedInvoke(SharedGraphBuilderPhase.java:252)
[INFO]     [creator]            at jdk.internal.vm.compiler/org.graalvm.compiler.java.BytecodeParser.genInvokeStatic(BytecodeParser.java:1677)
[INFO]     [creator]            at jdk.internal.vm.compiler/org.graalvm.compiler.java.BytecodeParser.genInvokeStatic(BytecodeParser.java:1652)
[INFO]     [creator]            at jdk.internal.vm.compiler/org.graalvm.compiler.java.BytecodeParser.processBytecode(BytecodeParser.java:5419)
[INFO]     [creator]            at jdk.internal.vm.compiler/org.graalvm.compiler.java.BytecodeParser.iterateBytecodesForBlock(BytecodeParser.java:3477)
[INFO]     [creator]            ... 39 more
```
