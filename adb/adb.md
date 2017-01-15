theme: plain jane, 3
autoscale: true 
footer: (c) Diego Freniche / @dfreniche / www.freniche.com

# ADB: Android Debug Bridge

![inline|150%](img/r2d2.png)

---

## ADB not in PATH

- Installed in $SDK_HOME/platform-tools

- Set PATH
	- Windows:
		- `Control Panel > System & Security > System > System Properties > Advanced > Environment variables`
		- add `;%PATH_TO_ADB%`

	- Mac / Linux: `$HOME/.bash_profile`
		- add `export PATH=$PATH:/opt/android/adt-bundle-mac-x86_64/sdk/platform-tools`

---

## Check ADB

```bash
$ adb -help   (shows adb help)

$ adb devices  (lists all devices)
```


---
## Init session in Emulator

`$ adb shell`
`$ adb -e shell`  (connects with __emulator__)
`$ adb -d shell`  (concects with a connected __physical device__)

- If device is not "rooted" we connect as normal user
- Android Emulator is always "rooted"


---

## Init sesion on non-rooted phones

```bash
$ adb shell 
$ run-as com.package.name
```

---


## Install/uninstall Apps

```bash
$ adb install com.myapp.helloworld
$ adb uninstall com.myapp.helloworld
```

---

## Push / Pull files from device

```bash
$ adb pull /data/data/com.freniche.ejemplo/fichero .
$ adb push fichero.txt /data/data/com.freniche.ejemplo/
```

---

## Push / Pull files from device (for non-root devices)

```bash
$ adb shell "run-as package.name chmod 666 /data/data/package.name/databases/file"
$ adb pull /data/data/package.name/databases/file .
$ adb shell "run-as package.name chmod 600 /data/data/package.name/databases/file"
```

---

## Show console logcat

```bash
$ adb logcat
```
---


## Launch Apps from console

```bash
$ adb shell am start -n com.package.name/com.package.name.ActivityName
```

---

## Activate assertions

$ adb shell setprop debug.assert 1

---

## Show memmap of a process

```bash
$ adb shell dumpsys meminfo <package_name>
```

---


## Take a screenshot of device

```bash
adb shell screencap -p | perl -pe 's/\x0D\x0A/\x0A/g' > screen.png
```

---


http://developer.android.com/intl/es/tools/help/shell.html