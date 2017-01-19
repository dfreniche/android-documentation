theme: plain jane, 3
footer: © Diego Freniche (http://blog.freniche.com)

# Gradle

---

# What is?

- Build system
- Flexible (supports plugins, like _java_, _eclipse_, etc)
- Based on __projects__ and __tasks__
- `gradle` looks for a build.gradle file (build _configuration_ script) in the current directory


---

## Get Gradle

- Can download from [http://www.gradle.org/downloads](http://www.gradle.org/downloads)
- unzip it
- `./bin/gradle`
- in Android we get a Gradle Wrapper `gradlew`

---
## Hello, World! Gradle

```gradle
# build.gradle file

task hello {
    doLast {
        println 'Hello world!'
    }
}
```

```gradle
# syntax-sugar closure version

task hello << {
    println 'Hello world!'
}
```

This Task contains an Action: a __Groovy Closure__ with code

---

## Running Gladle

```bash
$ ./gradle hello
:hello
Hello world!

BUILD SUCCESSFUL

Total time: 2.122 secs
```

```
/gradle -q hello
```
---

## Build scripts are programs in Groovy!

```gradle
task upper << {
    String someString = 'mY_nAmE'
    println "Original: " + someString 
    println "Upper case: " + someString.toUpperCase()
}

task count << {
    4.times { print "$it " }
}
```

---


## Task dependencies

```gradle
task hello << {
    println 'Hello world!'
}
task intro(dependsOn: hello) << {
    println "I'm Gradle"
}
```

---

## Android: 

- `gradle tasks`: See all Gradle tasks
- `gradle clean`: Deletes the build directory, removing all built files
- `gladle assemble`: Compiles and jars your code, but does not run the unit tests
- `gradle check`: Compiles and tests your code
- `gradle assembleDebug`:  Compilar Debug

---

## Adding repositories
A place that contains some .jars. This is where we search for jars.

```gradle
repositories {
    mavenCentral()
}
```
---

## Dependencies

The jars we want to use. We get them from our repositories.

```
dependencies {
    compile 'com.squareup.dagger:dagger:1.2.2'
}
```

### To get dependencies:

[http://gradleplease.appspot.com/#joda-time](http://gradleplease.appspot.com/#joda-time)

---

## Minimun Gradle Android script

```gradle
apply plugin: 'com.android.application'

android {
    compileSdkVersion 20
    buildToolsVersion "20.0.0"

    defaultConfig {
        applicationId "com.freniche.gestures"
        minSdkVersion 14
        targetSdkVersion 20
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            runProguard false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
}
```

---

# Don't use dynamic dependencies

This is bad

```gradle
compile 'com.android.support:appcompat-v7:23.0.+'  

```
ref: http://blog.danlew.net/2015/09/09/dont-use-dynamic-versions-for-your-dependencies/


---

## Using gradle from Android Studio

- just open the Terminal: it's located on your project

---

## Show all dependencies in your project

```gradle

// Add this to your build.gradle (the main build.gradle in your project)
task buildscriptDependencies << {
    buildscript.configurations.classpath.each { println it }
}

```

---

## Reference

[http://tools.android.com/tech-docs/new-build-system](http://tools.android.com/tech-docs/new-build-system)

