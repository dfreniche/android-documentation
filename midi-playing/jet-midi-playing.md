theme: plain jane, 3
autoscale: true 
footer: (c) Diego Freniche / @dfreniche / www.freniche.com

# Android MIDI Paying

> JET is an interactive music player for small embedded devices, including the those running the Android platform

- JetPlayer: library to play Jet files. [Documentation](https://developer.android.com/reference/android/media/JetPlayer.html). [Download](https://github.com/android/platform_external_sonivox)
- JetCreator: application to build .jet files



JetCreator manual: [https://developer.android.com/guide/topics/media/jet/jetcreator_manual.html](https://developer.android.com/guide/topics/media/jet/jetcreator_manual.html)

---

## Sample files: important to use .jet files

[https://github.com/android/platform_external_sonivox/blob/master/jet_tools/JetCreator_content/JetCreator_demo_1.zip](https://github.com/android/platform_external_sonivox/blob/master/jet_tools/JetCreator_content/JetCreator_demo_1.zip)

---

## Sample code

```java
JetPlayer jetPlayer = JetPlayer.getJetPlayer();
AssetFileDescriptor afd = this.getResources().openRawResourceFd(R.raw.jet);
jetPlayer.loadJetFile(afd);
byte segmentId = 0;
// queue segment 0, repeat once, use General MIDI
jetPlayer.queueJetSegment(0, -1, 0, 0, 0, (byte) 0);
jetPlayer.play();
```

---

## Reference:

- [http://stackoverflow.com/a/20116393/225503](http://stackoverflow.com/a/20116393/225503)
