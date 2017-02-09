autoscale: true

## (1/3) On 1st activity

### Call 2nd activity with `startActivityForResult`

```java
Intent i = new Intent(this, SecondActivity.class);
startActivityForResult(i, 1);
```

---

## (2/2) On 2nd

### Return something

- with OK status 

```java
Intent returnIntent = new Intent();
returnIntent.putExtra("result",result);
setResult(RESULT_OK,returnIntent);
finish();
```

- with CANCELLED status 

```java
Intent returnIntent = new Intent();
setResult(RESULT_CANCELED, returnIntent);
finish();
```

---

### Return to a Fragment

`RESULT_OK` defined in Activity

```
if (resultCode == getActivity().RESULT_OK) {

}

```

---

## (3/3) On 1st activity

### Get returned values

```java
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == 1) {
        if(resultCode == RESULT_OK){
            String result=data.getStringExtra("result");
        }
        if (resultCode == RESULT_CANCELED) {
            //Write your code if there's no result
        }
    }
}
```