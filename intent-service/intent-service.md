autoscale: true 
theme: Plain Jane,3

# Services

---

## What is a Service

- an Android Component that runs in the background
- will be terminated after all activities for this task has been terminated
- subclass of `Context`
 
---

## Intent Service

- handle asynchronous requests on demand
- these requests are `Intents`  
- to process these requests, extend `IntentService` and implement `onHandleIntent(Intent)`
- IntentService will:
    - receive the Intents (called _commands_)
    - launch a worker thread
    - process all _commands_ in a _command queue_
    - stop the service as appropriate.

---

## Start the service

```java
final Intent i = HelloWorldService.newIntent(this);
startService(i);
```

- we start this using an Intent (thus _IntentService_ name)
- can call `startService` several times
- a queue is used to dispatch every call of `startService` to the Service
- they're run in order in a background thread
- after `startService` is called, the background thread is created and onHandleIntent in called (in this bg thread)

---

## Starting a Service on App Startup

```java
public class HelloWorldServiceApp extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        final Intent i = HelloWorldService.newIntent(this);
        startService(i);
    }
}

```
---

## onHandleIntent

- runs in a thread in the background

```java
if (Looper.myLooper() != Looper.getMainLooper()) {
    // enters, because we're no in the main thread
}
```

---

## AndroidManifest.xml

- we need to declare Services inside the `<Application>` tag

```xml

<application
    ...
    >
    <activity
        ...
    </activity>
    
    <!-- Services -->

    <service android:name=".service.HelloWorldService" />

</application>
```

---

## Intent Service 1/2

```java

public class HelloWorldService extends IntentService {
    public static final String TAG = HelloWorldService.class.toString();

    // returns a new instance of this Service class: just a helper method
    public static Intent newIntent(Context context) {
        Log.d("", "Creating new Intent for HelloWorld Service");
        return new Intent(context, HelloWorldService.class);
    }

    // required by AndroidManifest
    public HelloWorldService() {
        super(HelloWorldService.class.toString());
    }

    @Override
    public void onCreate() {
        super.onCreate();
        Log.d(TAG, "Creating service with hash: " + this);
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "Destroying service with hash: " + this);
    }

```

---

## Intent Service 2/2


```java
    @Override
    protected void onHandleIntent(Intent intent) {
        Log.d(TAG, "Received something: " + intent);
        Log.d(TAG, "I'm on thread " + Thread.currentThread().getName());
        if (Looper.myLooper() != Looper.getMainLooper()) {
            Log.d(TAG, "I'm NOT on main thread ");
        }
        
        Log.d(TAG, "Doing something");
    }
}
```

---



## Repeating running commands on the service

- we want to launch the code in our service every n minutes
- say, to fetch some info from the Internet
- we could use a `Handler.sendMessageDelayed`, but if the user closes all Activities, our Handler is killed...
- use AlarmManager!

---

## Alarm Manager

- AlarmManager: a system service that can send Intents to our services for us
- instead of using `Intent` we use `PendingIntent`
- PendingIntent is just a way of packaging an intent for delayed execution

---

## Alarm Manager

```java

// create an intent to launch our service
Intent intent = HelloWorldService.newIntent(getBaseContext());
// package inside a PendingIntent
PendingIntent pendingIntent = PendingIntent.getService(getBaseContext(), 0, intent, 0);

// getService(context, idOfWhatImDoing, intent, flag)
//  where flag ==  FLAG_ONE_SHOT, FLAG_NO_CREATE, FLAG_CANCEL_CURRENT, FLAG_UPDATE_CURRENT, FLAG_IMMUTABLE

// get a reference to the AlarmManager
AlarmManager alarmManager = (AlarmManager) getSystemService(Context.ALARM_SERVICE);

// start the service every 5 seconds (5 * 1000)
alarmManager.setInexactRepeating(AlarmManager.ELAPSED_REALTIME, SystemClock.elapsedRealtime(), 1000 * 5, pendingIntent);

```

---

## Stopping the Alarm Manager

```java

AlarmManager alarmManager = (AlarmManager) getSystemService(Context.ALARM_SERVICE);
Intent intent = HelloWorldService.newIntent(getBaseContext());
PendingIntent pendingIntent = PendingIntent.getService(getBaseContext(), 0, intent, 0);

alarmManager.cancel(pendingIntent);


```


---
## Adding start/stop to the Service

```java

public static void startRunningService(Context context) {
    Intent intent = HelloWorldService.newIntent(context);
    PendingIntent pendingIntent = PendingIntent.getService(context, 0, intent, 0);
    AlarmManager alarmManager = (AlarmManager) context.getSystemService(Context.ALARM_SERVICE);
    alarmManager.setInexactRepeating(AlarmManager.ELAPSED_REALTIME, SystemClock.elapsedRealtime(), 1000*5, pendingIntent);
}

public static void stopRunningService(Context context) {
    Intent intent = HelloWorldService.newIntent(context);
    AlarmManager alarmManager = (AlarmManager) context.getSystemService(Context.ALARM_SERVICE);
    PendingIntent pendingIntent = PendingIntent.getService(context, 0, intent, 0);

    alarmManager.cancel(pendingIntent);
}


```

---

## Sending a Notification

![right|fit](img/notification.png)

- title
- text
- icon
- launches an Activity when touched
- can auto-dismiss

---

## Sending a Notification

```java
Resources resources = getResources();
Intent intent = new Intent(this, MainActivity.class);
PendingIntent pi = PendingIntent.getActivity(this, 0, intent, 0);

Notification notification = new NotificationCompat.Builder(this)
        .setTicker("Ticker")    // Set the "ticker" text which is sent to accessibility services.
        .setSmallIcon(android.R.drawable.ic_btn_speak_now)
        .setContentTitle("Notification Title")
        .setContentText("Notification Text")
        .setContentIntent(pi)
        .setAutoCancel(true)
        .build();

NotificationManagerCompat notificationManager = NotificationManagerCompat.from(this);
int notificationId = 0;
notificationManager.notify(notificationId, notification);    

```