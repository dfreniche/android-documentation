theme: Plain jane
autoscale: true

# Volley Notes

---

- Google library to do networking
- Part of AOSP

https://developer.android.com/training/volley/index.html

---

## Gradle

dependencies {
    ...
    compile 'com.android.volley:volley:1.0.0'
}


To use Volley, you must add the `android.permission.INTERNET` permission to your app's manifest. Without this, your app won't be able to connect to the network.

---

## Volley

```java
RequestQueue queue = Volley.newRequestQueue(this);
String url ="http://madrid-shops.com/json/getShops.php";

StringRequest stringRequest = new StringRequest(url, new Response.Listener<String>() {
    @Override
    public void onResponse(String response) {
        Log.d("", "Response from Volley: " + response);

        try(Reader reader = new StringReader(response)){
            Gson gson = new GsonBuilder().create();

            ShopResponse shopResponse = gson.fromJson(reader, ShopResponse.class);
            System.out.println(shopResponse.result);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}, new Response.ErrorListener() {
    @Override
    public void onErrorResponse(VolleyError error) {
        Log.d("ERROR", "Error");
    }
});

queue.add(stringRequest);

```


---

## JSON ejemplo / parsing with GSON


```json

{
  "result": [
    {
      "id": "310",
      "name": "acosta",
      "img": "http:\/\/madrid-shops.com\/media\/shops\/acosta-small.jpg",
      "logo_img": "http:\/\/madrid-shops.com\/media\/shops\/logo-acosta-200.jpg",
      "address": "Calle de Claudio Coello, 46, 28001 Madrid",
      "opening_hours_es": "L-S 10:30 - 20:30",
      "opening_hours_en": "M-S 10:30 - 20:30",
      "opening_hours_cn": "10:30 - 20:30",
      "opening_hours_jp": " \u6708\u66dc\uff5e\u571f\u66dc10:30-20:30",
      "opening_hours_mx": "L-S 10:30 - 20:30",
      "opening_hours_cl": "L-S 10:30 - 20:30",
      "telephone": "914 35 76 57",
      "email": "",
      "url": "http:\/\/acostamadrid.com\/es\/",
      "

``` 

---

## Sacamos el array de JSONs

```java 
import com.google.gson.annotations.SerializedName;

import java.util.List;

/**
 * Created by dfreniche on 08/12/16.
 */

public class ShopResponse {
    @SerializedName("result")
    public List<ShopEntity> result;

}
``` 

---

## Result is a List<ShopEntity>

```java
public class ShopEntity {
    @SerializedName("id") private Long id;
    @SerializedName("name") private String name;
    @SerializedName("img") private String img;
    @SerializedName("description_jp") private String description_jp;
    @SerializedName("categories") private List<String> categories;


``` 
