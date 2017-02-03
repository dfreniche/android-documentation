# Menus

1. Create `res/menu/menu.xml`
2. Override your Activity's `onCreateOptionsMenu()` to load the menu
3. `onOptionsItemSelected()` is called when we select a menu option

---

## menu xml file

File `menu.xml`

```xml 
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    >

    <item
        android:title="Add"
        android:id="@+id/menu_main_action_add_note"
        android:icon="@android:drawable/ic_menu_add"
        app:showAsAction="always"
         />
</menu>
```

---

## Swow / hide menu items

```
        app:showAsAction="always"
        app:showAsAction="never"
        app:showAsAction="ifRoom"
```

---

## Override onCreateOptionsMenu to create menu

```java
@Override
public boolean onCreateOptionsMenu(Menu menu) { 
	MenuInflater inflater = getMenuInflater();
	inflater.inflate(R.menu.menu, menu);
	 return true; 
}

```

---

## Override onOptionsItemSelected to manage menu clicks


```java
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) { 
        case R.id.menu_main_action_add_note:  // id defined in menu.xml
            startActivity(new Intent(this, PrefsActivity.class));  // do something
        break;
    }
    return true;
}
```