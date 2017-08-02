theme: plain jane, 3
autoscale: true 
footer: (c) Diego Freniche / @dfreniche / www.freniche.com

# Permissions

---

## Android version < N (API 23)

- just use a `uses-permission` entry inside Manifest

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```
---

## Android version >= N

1. We still __need__ to put the `uses-permission` line
1. We ask the user for permission programmatically
  - using `checkSelfPermission`
1. we get response from user's action
  - in `onRequestPermissionsResult`


---

## Ask the user for permission programmatically

```java
// check read storage permission for Android > M

@TargetApi(Build.VERSION_CODES.M)   // this annotation supress a Lint warning if we are targeting API < 23
public void checkStoragePermission() {
  if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    if (this.checkSelfPermission(Manifest.permission.READ_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED) {
      requestPermissions(new String[]{Manifest.permission.READ_EXTERNAL_STORAGE}, REQUEST_CODE_EXTERNAL_STORAGE); 
      // REQUEST_CODE_EXTERNAL_STORAGE is just a number
      return;
    }
  }
}
```

---

# Get response from user's action

```java 
@Override
public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
  // we can ask for multiple permissions, so we need to check which one is this
  switch (requestCode) {  
    case REQUEST_CODE_EXTERNAL_STORAGE:
      if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
        Toast.makeText(this, "We need access to your photos or App won't work properly", Toast.LENGTH_LONG);
      }
      break;
    default:
      super.onRequestPermissionsResult(requestCode, permissions, grantResults);
      break;
  }
}
```