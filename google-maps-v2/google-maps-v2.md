theme: plain jane, 3
autoscale: true 
footer: (c) Diego Freniche / @dfreniche / www.freniche.com

# Google Maps V2

## Google Maps

- propietary API (not part of AOSP)
- Check we've selected a Google SDK in `build.gradle`

```
compileSdkVersion 'Google Inc.:Google APIs:24'
```

- add Google Maps to libraries

```
compile 'com.google.android.gms:play-services-maps:10.0.1'
```
Check last Google info version [here](https://developers.google.com/android/guides/setup#add_google_play_services_to_your_project) 

---

## Get Google Maps API Key 

- We need to generate a SHA-1 key in our computer using Keytool
- To use Keytool, the Java JDK folder must be in the PATH

```
// WINDOWS
keytool -list -v -keystore "%USERPROFILE%\.android\debug.keystore" -alias androiddebugkey -storepass android -keypass android

// UNIX: Linux / Mac OS X
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

---

## Select & copy SHA-1 hash

```
$ keytool -list -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
androiddebugkey, Nov 25, 2015, PrivateKeyEntry,
Certificate fingerprint (SHA1): 66:07:5A:01:F0:7E:18:97:49:DC:65:DC:A1:00:32:25:5B:A0:2F:36
```

---

## Create Project in Google API Console

- Goto: https://code.google.com/apis/console/
- create a new project
- we can restrict the new key to an App or use a more generic one

---

## Add API key to Android Manifest

```xml
// Add to AndroidManifest.xml inside <application>
<!-- Google Maps API Key -->
<meta-data
    android:name="com.google.android.maps.v2.API_KEY"
    android:value="YOUR-API-KEY" />


<meta-data
    android:name="com.google.android.gms.version"
    android:value="@integer/google_play_services_version" />

```

---

## Don't upload your API-KEY to a public service!

- create a file inside `res/values` called `google_maps_api_key.xml`

```xml 
<resources>
    <string name="google_maps_api_key">AIzaDnpQp88AxOAefakeeeegAfEyCyAqtM7o</string>
</resources>

```

- add this file to your `.gitignore` file
- put in your Manifest file:

```xml 
<!-- Google Maps API Key: this key is in a separate values file -->
    <meta-data
        android:name="com.google.android.maps.v2.API_KEY"
        android:value="@string/google_maps_api_key" />

```

---

## Add permissions to Android Manifest

- ACCESS_NETWORK_STATE: para comprobar estado de la red y saber si se pueden descargar datos
- INTERNET: para comprobar el estado de la conexión a Internet
- WRITE_EXTERNAL_STORAGE: los mapas se “cachean” en almacenamiento persistente
- ACCESS_COARSE_LOCATION: acceso a Wifi + GSM para conocer la posición del usuario
- ACCESS_FINE_LOCATION: acceso al GPS
OpenGL ES V2: los mapas requieren OpenGL

---

```xml 
<permission
    android:name="<YOUR-APP-PACKAGE>.permission.MAPS_RECEIVE"
    android:protectionLevel="signature" />

<uses-permission android:name="<YOUR-APP-PACKAGE>.permission.MAPS_RECEIVE" />


<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

<!-- Required to show current location -->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

<!-- Required OpenGL ES 2.0. for Maps V2 -->
<uses-feature
    android:glEsVersion="0x00020000"
    android:required="true" />

```

---

## Layout

- Mapfragment (__using__ Support library)

```xml
<fragment
    android:id="@+id/map"
    android:name="com.google.android.gms.maps.SupportMapFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>

```

- Mapfragment (__not__ using Support library)

```xml
<fragment
    android:id="@+id/map"
    android:name="com.google.android.gms.maps.MapFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>

```

---

## Load Map

```java
private void initializeMap() {
        mapFragment = (SupportMapFragment) getSupportFragmentManager().findFragmentById(R.id.map);
        mapFragment.getMapAsync(new OnMapReadyCallback() {
            @Override
            public void onMapReady(GoogleMap googleMap) {
                // check if map is created successfully or not
                if (googleMap == null) {
                    Toast.makeText(getApplicationContext(),
                            "Sorry! unable to create maps", Toast.LENGTH_SHORT)
                            .show();
                } else {
                    setupMap(googleMap);
                }
            }
        });
    }

```

---



## Center a map in lat, lon

```java

public static void centerMapInPosition(GoogleMap googleMap, double latitude, double longitude) {
    CameraPosition cameraPosition = new CameraPosition.Builder().target(
            new LatLng(latitude, longitude)).zoom(12).build();
    googleMap.animateCamera(CameraUpdateFactory.newCameraPosition(cameraPosition));
}

```


---

## Show user position in Map


```java
if (ActivityCompat.checkSelfPermission(getBaseContext(), Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED && ActivityCompat.checkSelfPermission(getBaseContext(), Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
    // TODO: Consider calling
    //    ActivityCompat#requestPermissions
    // here to request the missing permissions, and then overriding
    //   public void onRequestPermissionsResult(int requestCode, String[] permissions,
    //                                          int[] grantResults)
    // to handle the case where the user grants the permission. See the documentation
    // for ActivityCompat#requestPermissions for more details.
    return;
}
googleMap.setMyLocationEnabled(true);
googleMap.getUiSettings().setRotateGesturesEnabled(false);

```

---

## Add pins to Map

Define MapPinnable interface

```java 

public interface MapPinnable<E> {
    float getLatitude();
    float getLongitude();
    String getPinDescription();
    String getPinImageUrl();
    E getRelatedModelObject();
}

```


---


```java 

public class MapPinsAdder {
    public static void addPins(List<MapPinnable> mapPinnables, final GoogleMap googleMap, final Context context) {
        if (mapPinnables == null || googleMap == null) {
            return;
        }

        for (final MapPinnable pinnable: mapPinnables) {
            final LatLng position = new LatLng(pinnable.getLatitude(), pinnable.getLongitude());
            final String profileImageUrl = pinnable.getPinImageUrl();

            final MarkerOptions marker = new MarkerOptions().position(position).title(pinnable.getPinDescription());
            
            Marker m = googleMap.addMarker(marker);
            m.setTag(pinnable);                   
        }
    }
}


``` 


---

## Add pins to Map


```java 
List<MapPinsAdder.MapPinnable> pins = new LinkedList<>();
for (Shop shop: shops.getAll()) {
    MapPinsAdder.MapPinnable pin = shop;

    pins.add(pin);
}

MapPinsAdder.addPins(pins, googleMap, this);


```

---


## Detect tap on Pin InfoWindow

```java 
// ...

    googleMap.setOnInfoWindowClickListener(this);

// ...

@Override
public void onInfoWindowClick(Marker marker) {
    if (marker.getTag() != null) {
        Shop shop = (Shop) marker.getTag();

        Navigator.navigateFromShopsActivityToShopDetailActivity(this, shop);
    }
}

```