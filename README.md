# Lay-Z-Spa WiFi App API

## Inital Reverse of Android Lay-Z-Spa WiFi App / API

**PLEASE BE VERY CAREFUL USING THE INFORMATION SHOWN HERE, YOU COULD BREAK YOUR LAY-Z-SPA!!!**

POST JSON "/v1/auth/login" authentication request to "http://mobileapi.lay-z-spa.co.uk" with email address and 
password parameters. Note '@' character is URI encoded so replaced with '%40', for example posting a reqest with a username
of 'email@domain.com' and a password of 'Password' would be sent as follows:
```
http://mobileapi.lay-z-spa.co.uk/v1/auth/login?email=email%40domain.com&password=Password
```

You should then receive something like the following:
```
{
    "data": {
        "id": 8888,
        "firstname": "Joe",
        "lastname": "Bloggs",
        "email": "email@domain.com",
        "address_line1": "null",
        "town": "",
        "county": null,
        "country": "",
        "postcode": "",
        "optin": 0,
        "enabled": true,
        "gizwits_uid": "[UID-Here]"
        "gizwits_expires_at": "2020-03-01 05:10:01"
        "gizwits_token": "[Token-Here]"
        "created_at": "2020-03-01 09:10:19",
        "updated_at": "2020-03-05 17:10:58",
        "last_login_at": "2020-04-10 20:20:11",
        "api_token-bkp": "",
        "api_token-bkp2": "",
        "api_token": "[API-Here]",
        "full_name": "Joe Bloggs"
        "anonymous_id": "[ID-Here]"
    },
    "devices": [
        {
            "id": 888,
            "did": "[Device-ID-Here]",
            "mac": "[Pump-MAC-Add-Here]",
            "product_key": "[Product-Key-Here]",
            "uid": "[UID-Here]",
            "user_token": "[User-Token-Here]",
            "app_uid": "8888",
            "created_at": "2020-03-01 19:10:24",
            "updated_at": "2020-03-05 17:13:43",
            "device_name": "[Device-Name-Here]"
        }
    ],
    "message": "Authorization Successful!"
}
```
Ensure you take a note of your device id ('did') as well as your API token, then submit an 'is_online' POST request with
your 'api_token' and 'did' parameters. Note you must also include the following key/value pairs within the header of the request, in this example they are from the android app:

```
Content-Type: application/x-www-form-urlencoded
X-Requested-With: com.wiltonbradley.layzspa
User-Agent: Mozilla/5.0 (Linux; Android 7.1.2; SM-G930L Build/N2G48H; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/75.0.3770.143 Mobile Safari/537.36
```

For example if you had an api_token of 'j1dgj62hds7834dh36d267' and a 
did of 'qHTzHKGWH89Slsh57FYS' then the POST would be as follows:

```
http://mobileapi.lay-z-spa.co.uk/v1/gizwits/is_online?api_token=j1dgj62hds7834dh36d267&did=qHTzHKGWH89Slsh57FYS
```

You should then receive the following response:
```
{
    "data": "true"
}
```

You can now proceed to send commands to the Lay-Z-Spa API gateway, ensure when sending your command you include
both your API key and Device ID, commands supported are:
* status
* turn_on
* turn_off
* turn_filter_off
* turn_filter_on
* turn_heat_on     **IMPORTANT: Always Send a filter_on BEFORE you send the heat on command**
* turn_heat_off
* turn_wave_on   - Wave AKA AirJet
* turn_wave_off
* temp_set     - Requires an additional 'temperature' parameter

For example switching on your filter with the example API and DID shown here would be:
```
http://mobileapi.lay-z-spa.co.uk/v1/gizwits/turn_filter_on?api_token=j1dgj62hds7834dh36d267&did=qHTzHKGWH89Slsh57FYS
```

If you wanted to set the temp of your Lay-Z-Spa to 30C with the example API and DID shown here you would use:
```
http://mobileapi.lay-z-spa.co.uk/v1/gizwits/temp_set?api_token=j1dgj62hds7834dh36d267&did=qHTzHKGWH89Slsh57FYS&temperature=30
```

Finally sending a status request will bring back the following infomation:
```
{
    "data": {
        "did": "qHTzHKGWH89Slsh57FYS",
        "updated_at": 0000000000,
        "attr": {
            "system_err2": 0,
            "wave_appm_min": 0,
            "heat_timer_min": 0,
            "heat_power": 0,
            "earth": 0,
            "wave_timer_min": 0,
            "system_err6": 0,
            "system_err7": 0,
            "system_err4": 0,
            "system_err5": 0,
            "heat_temp_reach": 0,
            "system_err3": 0,
            "system_err1": 0,
            "system_err8": 0,
            "system_err9": 0,
            "filter_timer_min": 0,
            "heat_appm_min": 0,
            "power": 1,
            "temp_set_unit": "",
            "filter_appm_min": 0,
            "temp_now": 20,
            "wave_power": 0,
            "locked": 1,
            "filter_power": 1,
            "temp_set": 40
        }
    }
}
```

I hope this helps someone, I've also captured the local end, Pump to head-end (this uses MQTT), I'll post this if required.
I'll be working on a python script to automate some functions. Do let me know if you're interested or progressed with this.

As requested please find below information related to the local MQTT messages, note that intercepting local MQTT would be a bad idea and therefore any interaction should be via the API gateway. Although just in case we ever loose access to the App / head end this is what I have up to now. More needs to be done to analyse the boot process but at least this is a start.

Note I'm unsure if aQ2zORXv7N4qCkv2WKZVT is related to a unique identifier for my hot tub, therefore I've used this as an example. Therefore, please dont use the topic as outlined below but have a look at what MQTT messages you're seeing.


| Action        | Topic                            | Message                                                |
| ------------- |:--------------------------------:| ------------------------------------------------------:|
| Swtich Off    | app2dev/aQ2zORXv7N4qCkv2WKZVT/0 | 00000003140000900**1000100**00000000000000000000000000 |
| Switch On     | app2dev/aQ2zORXv7N4qCkv2WKZVT/0 | 00000003140000900**1000101**00000000000000000000000000 |
| Filter Off    | app2dev/aQ2zORXv7N4qCkv2WKZVT/0 | 00000003140000900**1000400**00000000000000000000000000 |
| Filter On     | app2dev/aQ2zORXv7N4qCkv2WKZVT/0 | 00000003140000900**1000404**00000000000000000000000000 |
| Heat Off      | app2dev/aQ2zORXv7N4qCkv2WKZVT/0 | 00000003140000900**1000200**00000000000000000000000000 |
| Heat On       | app2dev/aQ2zORXv7N4qCkv2WKZVT/0 | 00000003140000900**1000202**00000000000000000000000000 |
| AirJet Off    | app2dev/aQ2zORXv7N4qCkv2WKZVT/0 | 00000003140000900**1000800**00000000000000000000000000 |
| AirJet On     | app2dev/aQ2zORXv7N4qCkv2WKZVT/0 | 00000003140000900**1000808**00000000000000000000000000 |
| Temp 20       | app2dev/aQ2zORXv7N4qCkv2WKZVT/0 | 00000003140000900**100800014**000000000000000000000000 |
| Temp 30       | app2dev/aQ2zORXv7N4qCkv2WKZVT/0 | |
| Temp 40       |||




