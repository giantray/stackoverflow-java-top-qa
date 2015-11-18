##�p����oandroid�ߤ@?�H

###??
�C�@��android�Ƹm�����ߤ@ID?�H�p�G���H��\��java��??���o�O�H

###�^��1�]�̨Ρ^

�p����oandroid�ߤ@?�H
�n�]�G
1.���ݭn�S�w?��.
2.�b99.5% Android�Ƹm�]�]�Aroot?���^�W�A�YAPI => 9�A�O?�ߤ@��.
3.����app���Z������o�ۦP�ߤ@��.

?�N?�G

```
if API => 9/10: (99.5% of devices)

return unique ID containing serial id (rooted devices may be different)

else

return unique ID of build information (may overlap data - API < 9)
```

�N?:

```java

/**
 * Return pseudo unique ID
 * @return ID
 */public static String getUniquePsuedoID() {
    // If all else fails, if the user does have lower than API 9 (lower
    // than Gingerbread), has reset their device or 'Secure.ANDROID_ID'
    // returns 'null', then simply the ID returned will be solely based
    // off their Android device information. This is where the collisions
    // can happen.
    // Thanks http://www.pocketmagic.net/?p=1662!
    // Try not to use DISPLAY, HOST or ID - these items could change.
    // If there are collisions, there will be overlapping data
    String m_szDevIDShort = "35" + (Build.BOARD.length() % 10) + (Build.BRAND.length() % 10) + (Build.CPU_ABI.length() % 10) + (Build.DEVICE.length() % 10) + (Build.MANUFACTURER.length() % 10) + (Build.MODEL.length() % 10) + (Build.PRODUCT.length() % 10);

    // Thanks to @Roman SL!
    // http://stackoverflow.com/a/4789483/950427
    // Only devices with API >= 9 have android.os.Build.SERIAL
    // http://developer.android.com/reference/android/os/Build.html#SERIAL
    // If a user upgrades software or roots their device, there will be a duplicate entry
    String serial = null;
    try {
        serial = android.os.Build.class.getField("SERIAL").get(null).toString();

        // Go ahead and return the serial for api => 9
        return new UUID(m_szDevIDShort.hashCode(), serial.hashCode()).toString();
    } catch (Exception exception) {
        // String needs to be initialized
        serial = "serial"; // some value
    }

    // Thanks @Joe!
    // http://stackoverflow.com/a/2853253/950427
    // Finally, combine the values we have found by using the UUID class to create a unique identifier
    return new UUID(m_szDevIDShort.hashCode(), serial.hashCode()).toString();}
```
###�^��2
�n�]�G
1.���ݭn�S�w?��.
2.�b100% Android�Ƹm�]�]�Aroot?���^�W�A�O?�ߤ@��.

���]
1.����app���Z������o�ۦP�ߤ@��.

```java
private static String uniqueID = null;
private static final String PREF_UNIQUE_ID = "PREF_UNIQUE_ID";

public synchronized static String id(Context context) {
    if (uniqueID == null) {
        SharedPreferences sharedPrefs = context.getSharedPreferences(
                PREF_UNIQUE_ID, Context.MODE_PRIVATE);
        uniqueID = sharedPrefs.getString(PREF_UNIQUE_ID, null);
        if (uniqueID == null) {
            uniqueID = UUID.randomUUID().toString();
            Editor editor = sharedPrefs.edit();
            editor.putString(PREF_UNIQUE_ID, uniqueID);
            editor.commit();
        }
    }
    return uniqueID;
}
```

###�^��3�]�ݭn���m?�d�^

�n�]�G
1.����app���Z������o�ۦP�ߤ@��.

�N?�G

```java
    final TelephonyManager tm = (TelephonyManager) getBaseContext().getSystemService(Context.TELEPHONY_SERVICE);
    final String tmDevice, tmSerial, androidId;
    tmDevice = "" + tm.getDeviceId();
    tmSerial = "" + tm.getSimSerialNumber();
    androidId = "" + android.provider.Settings.Secure.getString(getContentResolver(), android.provider.Settings.Secure.ANDROID_ID);
    UUID deviceUuid = new UUID(androidId.hashCode(), ((long)tmDevice.hashCode() << 32) | tmSerial.hashCode());
    String deviceId = deviceUuid.toString();
```

??�G�n���o�H�U?��
```
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
```
stackoverflow?���G
http://stackoverflow.com/questions/2785485/is-there-a-unique-android-device-id