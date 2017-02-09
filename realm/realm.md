theme: plain jane, 3
autoscale: true 
footer: (c) Diego Freniche / @dfreniche / www.freniche.com

# Realm

---

## What is this?

Realm is a database replacement for Core Data (iOS) or using SQLiteHelper + DAOs + ContentProvider + CursorLoaders (Android)

---

## Benefits & Problems

- same data layer for Android & iOS apps
- not 100% compatible with all Google Android patterns

---

## Requisites

- No support for Java outside of Android at the moment.
- JDK version >=7.
- API Level >= 9


---


## Android install
Realm is installed as a Gradle plugin.



- add to `project/build.gradle`

```gradle
dependencies {
    classpath "io.realm:realm-gradle-plugin:2.2.1"
}
```

- in `app/build.gradle` (beginning of file)

```gradle
apply plugin: 'realm-android'
```

---

## Model

- make your Model classes extend `RealmObject`
- make sure there's a defafault constructor accesible

```java 
import io.realm.RealmObject;

public class Note extends RealmObject {
    private long id;
    private String title;
    private String text;

    public Note() {
        // Realm required default constructor
    }

    // getters & setters
}
```

---

## Supported types

Realm supports the following field types: boolean, byte, short, int, long, float, double, String, Date and byte[]

- @Required: annotation: can't be null
- @Ignore: field should not be persisted to disk

---

## @PrimaryKey

```java
@PrimaryKey private long id;
```

- `@PrimaryKey` annotation will mark a field as a primary key inside Realm.
- Only one field in a RealmObject class can have this annotation, and the field should uniquely identify the object.
- Trying to insert an object with an existing primary key will result in an `RealmPrimaryKeyConstraintException`. 
- Primary key cannot be changed after the object created.

---

## Autoincrement ID

- no autoincrement ids provided. [Github discussion](https://github.com/realm/realm-java/issues/469)

```java
int number = realm.where(Shop.class).findAllSorted(KEY_SHOP_ID).size();
return new AtomicLong(number + 1);
```

---

## What if I can't extend from RealObject?

RealmModel interface
An alternative to extending the RealmObject base class is implementing the RealmModel interface and adding the @RealmClass annotation.

```java 
@RealmClass
public class User implements RealmModel {

}
```

---

## What if I can't extend from RealmObject?

All methods available on RealmObject are then available through static methods.

```java 
// With RealmObject
user.isValid();
user.addChangeListener(listener);

// With RealmModel
RealmObject.isValid(user);
RealmObject.addChangeListener(user, listener);
```

---

## Init Realm

- Best done in our `android.app.application` subclass

```java
// Initialize Realm
Realm.init(this);
```

---

## Get a Realm reference

```java
// Get a Realm instance for this thread
Realm realm = Realm.getDefaultInstance();
```

---

## Transactions

Changing Realm data can only be done from inside a transaction.

```java
realm.beginTransaction();
realm.deleteAll();
realm.commitTransaction();
```

- `realm.beginTransaction()`
- `realm.commitTransaction()`
- `realm.cancelTransaction()`
- `realm.isInTransaction()`


---

## Transactions (2)

```
realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        realm.delete(Shop.class);
    }
});

```

---


## Inserting data

```java
// Initialize Realm
Realm.init(this);

// Get a Realm instance for this thread
Realm realm = Realm.getDefaultInstance();

// Persist your data in a transaction
realm.beginTransaction();

for (int i = 0; i < 10; i++) {
    Notebook notebook1 = new Notebook();
    notebook1.setId(i);
    notebook1.setTitle("My first notebook " + i);

    try {
        final Notebook managenedNotebook1 = realm.copyToRealm(notebook1);
    } catch (RealmPrimaryKeyConstraintException e) {
        Log.d("Realm", "duplicated key " + i);
    }
}

realm.commitTransaction();
```

---

## Inserting data: duplicated keys

```
try {
    final Notebook managenedNotebook1 = realm.copyToRealm(notebook1);
} catch (RealmPrimaryKeyConstraintException e) {
    Log.d("Realm", "duplicated key " + i);
}
```

---

## Inserting data: copy to realm or update

- to update we need at least one field annotated with `@PrimaryKey`

```java
realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        realm.copyToRealmOrUpdate(note);
    }
});
```

---

## Auto-updating data

Auto-Updating Objects

```java
realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        Dog myDog = realm.createObject(Dog.class);
        myDog.setName("Fido");
        myDog.setAge(1);
    }
});
Dog myDog = realm.where(Dog.class).equalTo("age", 1).findFirst();

realm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        Dog myPuppy = realm.where(Dog.class).equalTo("age", 1).findFirst();
        myPuppy.setAge(2);
    }
});

myDog.getAge(); // => 2

```

---

## Clear the Realm database

- deletes all tables

```java 
realm.beginTransaction();
realm.deleteAll();
realm.commitTransaction();
```

- delete only the table Notebook

```
realm.beginTransaction();
realm.delete(Notebook.class);
realm.commitTransaction();
```

---

## Querying Realm

- Realm can't return a Cursor. [Github issue](https://github.com/realm/realm-java/issues/438)

- `RealmResults`: This class holds all the matches of a RealmQuery for a given Realm

```java
RealmResults<E> result = realm.where(elementClass).findAll();
RealmResults<E> result = realm.where(elementClass).findAll().sort("id");
RealmResults<E> result = realm.where(elementClass).findAll().distinct("id");
RealmResults<E> result = realm.where(elementClass).equalTo();


```

---

## Create objects from JSON (I)

```java 
Note note = realm.createObjectFromJson(Note.class, "{id:1000, title: tile, text: \"some text\"}");
realm.copyToRealm(note);

```


---

## Create objects from JSON (II)

- Using GSON

```java
// Using the User class
public class User extends RealmObject {
    private String name;
    private String email;
    // getters and setters left out ...
}

Gson gson = new GsonBuilder().create();
String json = "{ name : 'John', email : 'john@corporation.com' }";
User user = gson.fromJson(json, User.class);
```

---

## Threads

Although Realm files can be accessed by multiple threads concurrently, you cannot hand over Realms, Realm objects, queries, and results between threads.

---

## Realm browser

- tool to inspect Realm database files 

http://stackoverflow.com/questions/28478987/how-to-view-my-realm-file-in-the-realm-browser

---

## Extract Realm DB from emulator

- problem: accessing data from a non-rooted device, last API levels is not easy
- we can do `adb shell` & `run-as <my-package>` but we can't do `adb pull` directly
- Solution:
    - run some android code to copy the Ream db file into /sdcard
    - `adb pull /sdcard/file.realm .`

---

```bash
adb shell "run-as com.freniche.realmhelloworld chmod 666 /data/data/com.freniche.realmhelloworld/files"
# for devices running an android version lower than lollipop
adb pull /data/data/com.freniche.realmhelloworld/files/ .
# for devices running an android version equal or grater than lollipop
adb exec-out run-as com.freniche.realmhelloworld cat files/ > files
adb shell "run-as com.freniche.realmhelloworld chmod 600 /data/data/com.freniche.realmhelloworld/files/"
```

---

## Get Realm doc as Dash Docset 

- Download the javadoc Realm web, using wget (install wget via homebrew with `brew install wget`)
```bash
wget --no-parent -r -k https://realm.io/docs/java/2.3.0/api/
```

- Download the [javadocset tool from Kapeli's Github](https://github.com/Kapeli/javadocset)
    - give it execution permission `chmod a+x javadocset`

- Convert the javadoc into a docset

```bash 
cd realm.io/docs/java/latest
javadocset realm-java api
```

---

## Reference

- [Getting started](https://realm.io/docs/java/latest/#getting-started)
- [Java API](https://realm.io/docs/java/latest/api/)