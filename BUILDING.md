<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

# Building
Apache Ratis uses Apache Maven to build the artifacts.
It is required to have Maven 3.3.9 or later.
Apache Ratis is written in Java 8.
Therefore, it as well requires Java 8 or later.

When building Ratis the first time, shaded files need to be generated by the following command:
```
$ mvn package -DskipTests
```
After that, `mvn compile` or `mvn test` can be used as normal.
For example, we may run the basic tests by
```
$ mvn test -Dtest=TestRaftWith\*
```

# Shading

We shade protos, protobuf and other libraries such as Netty, gRPC, Guava and Hadoop
so that applications using Ratis may use protobuf and other libraries with versions
different from the versions used here.

Ratis requires the shaded sources for compilation.
The generated sources are stored in the following directories.
```
ratis-proto-shaded/src/main/java/
ratis-hadoop-shaded/src/main/java/
```
They are not checked-in to git though.

If you want to force compiling the proto files (for example after changing them),
you should run
```
$ mvn package -DskipTests -Dcompile-protobuf
```

For removing the shaded sources, run
```
$ mvn clean -Pclean-shade
```

Note that `mvn clean` alone does not remove the shaded sources.

## What are shaded?

| Original packages                   | Shaded packages                                              |
| ------------------------------------|--------------------------------------------------------------|
| `com.google.common`                 | `org.apache.ratis.shaded.com.google.common`                  |
| `com.google.protobuf`               | `org.apache.ratis.shaded.com.google.protobuf`                |
| `com.google.thirdparty.publicsuffix`| `org.apache.ratis.shaded.com.google.thirdparty.publicsuffix` |
| `io.grpc`                           | `org.apache.ratis.shaded.io.grpc`                            |
| `io.netty`                          | `org.apache.ratis.shaded.io.netty`                           |
| `org.apache.hadoop.ipc.protobuf`    | `org.apache.ratis.shaded.org.apache.hadoop.ipc.protobuf`     |

The protos defined in this project are stored in the `org.apache.ratis.shaded.proto` package.

# How to deploy

To publish, use the following settings.xml file ( placed in ~/.m2/settings.xml )
```
<settings>
<servers>
  <server>
    <id>apache.releases.https</id>
    <username>ratis_committer</username>
    <password>********</password>
  </server>
  
  <server>
    <id>apache.snapshots.https</id>
    <username>ratis_committer</username>
    <password>********</password>
  </server>
</servers>
</settings>
```

Then use
```
$ mvn deploy
(or)
$ mvn -s /my/path/settings.xml deploy
```
We also use release profile for building the release
```
$ mvn install -Prelease -Papache-release
```