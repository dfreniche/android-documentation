theme: plain jane, 3
autoscale: true 
footer: (c) Diego Freniche / @dfreniche / www.freniche.com

## Check Network available

> you should not base decisions on whether a network is "available." You should always check isConnected() before performing network operations

```java
private static final String DEBUG_TAG = "NetworkStatusExample";
...      
ConnectivityManager connMgr = (ConnectivityManager) 
        getSystemService(Context.CONNECTIVITY_SERVICE);
NetworkInfo networkInfo = connMgr.getNetworkInfo(ConnectivityManager.TYPE_WIFI); 
boolean isWifiConn = networkInfo.isConnected();
networkInfo = connMgr.getNetworkInfo(ConnectivityManager.TYPE_MOBILE);
boolean isMobileConn = networkInfo.isConnected();
Log.d(DEBUG_TAG, "Wifi connected: " + isWifiConn);
Log.d(DEBUG_TAG, "Mobile connected: " + isMobileConn);
```

---

```java
public boolean isOnline() {
    ConnectivityManager connMgr = (ConnectivityManager) 
            getSystemService(Context.CONNECTIVITY_SERVICE);
    NetworkInfo networkInfo = connMgr.getActiveNetworkInfo();
    return (networkInfo != null && networkInfo.isConnected());
}  
```

---

## Detect Connection Changes

- NetworkReceiver: subclass of BroadcastReceiver


registers the BroadcastReceiver NetworkReceiver in onCreate(), and it unregisters it in onDestroy(). This is more lightweight than declaring a <receiver> in the manifest. When you declare a <receiver> in the manifest, it can wake up your app at any time, even if you haven't run it for weeks. By registering and unregistering NetworkReceiver within the main activity, you ensure that the app won't be woken up after the user leaves the app.

---

```java
public class NetworkReceiver extends BroadcastReceiver {   
      
@Override
public void onReceive(Context context, Intent intent) {
    ConnectivityManager conn =  (ConnectivityManager)
        context.getSystemService(Context.CONNECTIVITY_SERVICE);
    NetworkInfo networkInfo = conn.getActiveNetworkInfo();
       
    // Checks the user prefs and the network connection. Based on the result, decides whether
    // to refresh the display or keep the current display.
    // If the userpref is Wi-Fi only, checks to see if the device has a Wi-Fi connection.
    if (WIFI.equals(sPref) && networkInfo != null && networkInfo.getType() == ConnectivityManager.TYPE_WIFI) {
        // If device has its Wi-Fi connection, sets refreshDisplay
        // to true. This causes the display to be refreshed when the user
        // returns to the app.
        refreshDisplay = true;
        Toast.makeText(context, R.string.wifi_connected, Toast.LENGTH_SHORT).show();

    // If the setting is ANY network and there is a network connection
    // (which by process of elimination would be mobile), sets refreshDisplay to true.
    } else if (ANY.equals(sPref) && networkInfo != null) {
        refreshDisplay = true;
                 
    // Otherwise, the app can't download content--either because there is no network
    // connection (mobile or Wi-Fi), or because the pref setting is WIFI, and there 
    // is no Wi-Fi connection.
    // Sets refreshDisplay to false.
    } else {
        refreshDisplay = false;
        Toast.makeText(context, R.string.lost_connection, Toast.LENGTH_SHORT).show();
    }
}
```

---

### onCreate

```java
// Register BroadcastReceiver to track connection changes.
IntentFilter filter = new IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION);
receiver = new NetworkReceiver();
this.registerReceiver(receiver, filter);
```

### onDestroy

```java
if (receiver != null) {
    this.unregisterReceiver(receiver);
}
```

---
