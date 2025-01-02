# ESP32_2432S028 SSL server connection and request

This program can be user to fetch data from a specific server where solar plant data are stored.\
In this specific case I am using *zcsazzurrosystems* since it is connected with my private solar plant system.

## Library overview

The library is realized implementing a simple structure:
### 1. Connection to the WiFi with WiFi.h library:
```cpp
void connect_wifi(){

  /* Try to open connection */
  WiFi.begin(ssid, password);

  ...
```

### 2. Connection to the server using a CACertificate:  
It is necessary to set the certificate
```cpp

    client.setCACert(Certificate_login);

```
  
Contact the server (handshake)
```cpp

    if (!http.begin(client, server_url)) {

```
  
Perform POST request
```cpp

  int httpCode = http.POST(payload);

```
  
From the resposonse get the id_Token using JSON
```cpp

  id_Token = String(doc["idToken"]);

```
### 3. Fetch data from server 
It is necessary to perform another POST request passing required data necessary to receive as a response all the data associated to the plant. Finally, it is possible to parse all these data and display in the screen.

In this case add this special header
```cpp

  http.addHeader("Authorization", "Bearer " + id_Token);

```

Perform a new POST request
```cpp

  int httpCode = http.POST(payload);

```
Extract data in a user defined struct
```cpp
  data.power_solar = compute_response(doc, "P_SELF", device_name);
  data.power_grid = compute_response(doc, "P_LOAD", device_name);
  data.battery_level = compute_response(doc, "BP1", device_name);
```

The remaining part of the code is related to my (very) simple GUI implementation, realized with LVGL library.

## Procedure
Configure modifying HTTP_display.ino

1. set SSID and password for local access point connection
  
```cpp
const char* ssid = "YOUR_SSID";
const char* password = "YOUR_PASSWORD";
```
  
2. set username and password for the login in the external server

```cpp
const char* username_login = "YOUR_EMAIL_ADDRESS";
const char* password_login = "YOUR_LOGIN_PASSWORD";
  ```
  
3. update your device name

```cpp
const String device_name = "YOUR_DEVICE_NAME";
  ```

> [!NOTE]
> I found my device name analyzing mi network connection with the server. Try with your browser and using web developement tools.




## Future developement
The device is not well programmed, since it will work without interruptions, but this can lead to unwanted power consuptions. It is necessary to reduce the power consumtion turning off the display when it is unused. This is possible by introducing a sort of sleep mode for the display and at the same time keeping the device responsive with a simple button.
It is possible to increase efficiency also enabling sleep mode in ESP32. 

Improvements also in code can be considered, introducing a new lvgl library for our purpose.

Finally, error management should me implemented in order to manage failures in connection or server timeout which will interrupt data fetching.

>[!CAUTION]
>This program is not reccomened for external use.  
>Try it at your own risk :heavy_exclamation_mark:\
>Contact me for more detail and for source code. :email:
