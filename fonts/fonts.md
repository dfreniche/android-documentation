theme: plain jane, 3
autoscale: true 
footer: (c) Diego Freniche / @dfreniche / www.freniche.com

# Custom Fonts in Android

---

## Where to get fonts from

- License is __very__ important
- You have to normally pay for using Fonts in your App

https://fonts.google.com/

---

## Download & copy the fonts in your project

- inside `src/main/assets`
- this directory isn't inside `src/main/res`

---

## Why `assets`?

>  if you want to package a custom font with your app. There are API calls to create a Typeface from a font file stored in the file system or in your app's assets/ directory. But there is no API to create a Typeface from a font file stored in the res/ directory (or from an InputStream, which would allow use of the res/ directory).

http://stackoverflow.com/a/5583782/225503


---

## Code: Font class

- add this class to your project
- this works with `android.widget.TextView` and all TextView subclasses: `AppCompatTextView, Button, CheckedTextView, Chronometer, DigitalClock, EditText, RowHeaderView, TextClock, ...`

```java
import android.content.Context;
import android.graphics.Typeface;
import android.support.annotation.NonNull;
import android.widget.TextView;

public class Font {
    private final String      assetName;
    private volatile Typeface typeface;

    public Font(@NonNull String assetName) {
        if (assetName == null || assetName.length() == 0) {
            throw new IllegalArgumentException("Need a Font name");
        }
        this.assetName = assetName;
    }

    public void apply(@NonNull final Context context, @NonNull final TextView textView) {
        if (typeface == null) {
            synchronized (this) {
                if (typeface == null) {
                    typeface = Typeface.createFromAsset(context.getAssets(), assetName);
                }
            }
        }
        textView.setTypeface(typeface);
    }
}
```

---

## Define some Fonts

```java
public class AdventureFonts {
    public static final Font  KAUSHAN_SCRIPT_REGULAR = new Font("KaushanScript-Regular.ttf");
}
```

- Use the fonts

```java
AdventureFonts.KAUSHAN_SCRIPT_REGULAR.apply(this, mainText);
```
