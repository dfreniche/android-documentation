# 64K limit

## Demo

- create an Empty Android project
- add to build.gradle

```gradle
compile 'com.google.android.gms:play-services:10.0.1'
```

- try to compile: compiles fine
- try to run: error!

```
Error:Execution failed for task ':app:transformClassesWithDexForDebug'.
> com.android.build.api.transform.TransformException: com.android.ide.common.process.ProcessException: java.util.concurrent.ExecutionException: com.android.dex.DexIndexOverflowException: method ID not in [0, 0xffff]: 65536

```

> 65536: 64 K method limit

## To solve

- add to gradle

```gradle
defaultConfig {
    ...
    multiDexEnabled true
}

compile 'com.android.support:multidex:1.0.0'
```

- extend from MultiDexApplication

```java
public class MultidexDemoApp extends MultiDexApplication {
}
```

- use in Android Manifest

```java
<application
        android:name=".MultidexDemoApp"
```


---

## Reference

https://developer.android.com/studio/build/multidex.html?hl=en-419

