Attempting to get settled with  Flink under Scala 2.11, but running into some odd issues with macro expansion in a project that depends on `flink-scala_2.11` and `flink-runtime_211`.  

I’ve instrumented the [SocketStreamWordCount quick start](https://github.com/OCExercise/wordcount-processing) to test this issue.  It depends on Flink 1.0-SNAPSHOT, so you can test against a local build of Flink for 2.11.


### Steps taken
1. Cloned source and checked out `release-0.10`
2. Ran `tools/change-scala-version.sh 2.11`
3. Ran `mvn clean install -Dscala.version=2.11.4 -Dscala.binary.version=2.11 -Dakka.version=2.4.1 -DskipTests=true` in Flink root; build succeeds
4. Ran my spring-booted downstream project’s build and boot run.


### Expected result

Success

### Actual result

During compilation, I get the following error:

```
[ERROR] /Users/revprez/dev/exercise/systems/jvm/flink/wordcount-processing/src/main/scala/org/opencorrelate/jvm/scala/SocketTextStreamWordCount.scala:73: error: could not find implicit value for evidence parameter of type org.apache.flink.api.common.typeinfo.TypeInformation[String]
[ERROR]         val counts = text.flatMap { _.toLowerCase.split("\\W+") filter { _.nonEmpty } }
[ERROR]                                   ^
[ERROR] /Users/revprez/dev/exercise/systems/jvm/flink/wordcount-processing/src/main/scala/org/opencorrelate/jvm/scala/WordCount.scala:40: error: can't expand macros compiled by previous versions of Scala
[ERROR]     val text = env.fromElements("To be, or not to be,--that is the question:--",
```

### Discussion

I’m not sure how the Maven build actually succeeded, as there doesn’t seem to be any `quasiquotes_2.11` under [org.scalamacros](http://central.maven.org/maven2/org/scalamacros/) of any version in the public repos, nor in the four vendor repos configured in `flink-parent`’s `pom.xml`.  Unless I’m missing something, 

```
			<dependencies>
				<dependency>
					<groupId>org.scalamacros</groupId>
					<artifactId>quasiquotes_${scala.binary.version}</artifactId>
					<version>${scala.macros.version}</version>
				</dependency>
			</dependencies>
```

I recall an earlier discussion about issues with 2.10 stuff polluting the 2.11 build.  Not sure if this is related--I’m able to get through everything I’ve identified up to this point by simply overriding properties in `flunk-parent`  with command line arguments.  

Also not sure what next steps to take.  Don’t know enough about Scala Macros to know if substituting, say, `org.scalameta:quasiquotes_2.11:0.0.4` is a viable option (actually tried it, seems to be more trouble than it’s worth).  

If anyone has any ideas, I’m more than willing to try thing—Flink’s on our critical path and if we can get it operational under 2.11 then we’er golden.