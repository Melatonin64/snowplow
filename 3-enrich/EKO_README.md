# Building Custom Kinesis Stream Enrich

## Why the fork ? What Changed ?

Needed support for `text/plain` requests in stream enrich kinesis (to support Beacon API in Chrome).
This was a simple addition in `./3-enrich/scala-common-enrich/src/main/scala/com.snowplowanalytics.snowplow.enrich/common/adapters/registry/snowplow/Tp2Adapter.scala`.  
However, since `stream-enrich` uses `scala-common-enrich` as a Scala managed dependency (which means it does not use the local copy of `scala-common-enrich` but instead downloads a published version from a remote Maven repository when building), we need to build a custom `scala-common-enrich` JAR and use it in `stream-enrich` as an unmanaged dependency.  
Knowing nothing about Scala or its ecosystem, I was able to hack it together using quick-and-dirty solutions that unfortunately involve some manual steps.

## Prerequisites

- SBT 2.11

## Pack scala-common-enrich

- `cd ./3-enrich/scala-common-enrich`
- `sbt pack`
- JAR files will be under `./3-enrich/scala-common-enrich/target/pack/lib`

## Build stream-enrich-kinesis

- Copy some JARs from `./3-enrich/scala-common-enrich/target/pack/lib` to `./3-enrich/stream-enrich/core/lib` avoiding conflicts with stream-enrich deps
- `cd ./3-enrich/stream-enrich`
- `sbt compile` (not sure if required?)
- `sbt "project kinesis" assembly`
- JAR artifact will be under `./3-enrich/stream-enrich/kinesis/target/scala-2.11`, should be around 75MB
- Create a zip archive from JAR (zip filename is same as JAR's but dashes are replaced with underscores)
