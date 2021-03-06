##  Tuya Message Push Assistant Protocol

With the APP process alive, in order to improve the timeliness and success rate of push messages, Tuya Smart Camera has opened a message push assistant protocol.

- Users implement their own access to push channels, refer to  [Tuya Smart Home SDK system push message](https://tuyainc.github.io/tuyasmart_home_android_sdk_doc/en/resource/MessagePush.html)

- Register for Tuya push message monitoring, get the push message from the callback, and follow up


Message body format definition example:

```json
{
"a": "view",
"c": "action",
"cc": "smart camera ,someone is ringing the bell.",
"ct": "fcm You have a visitor",
"devId": "6cfaf335a8d6e752e0wrpy",
"msgId": "4da4dcf61573555995",
"p": {
"media": 13
},
"specialChannel": false,
"ts": "1573555995000",
"type": "doorbell"
}
```



### Register and logout listening

Register after the account is successfully logged in, and log out when the account is logged out

**Declaration**

The Tuya push assistant protocol needs to register for monitoring after the account login is successful, and log out when the account is logged out.

```java
//register
TuyaHomeSdk.getCameraInstance().registerCameraPushListener(ITuyaGetBeanCallback<CameraPushDataBean> callback)
  
//logout
TuyaHomeSdk.getCameraInstance().unRegisterCameraPushListener(ITuyaGetBeanCallback<CameraPushDataBean> callback)；
```

 **Parameters**

| Parameter | Description                                                  |
| :-------- | :----------------------------------------------------------- |
| callback  | ITuyaGetBeanCallback interface, monitor callback push messages, CameraPushDataBean encapsulates push messages |

**CameraPushDataBean Data Model**

| Field     | Type    | Description               |
| :-------- | :------ | :------------------------ |
| devId     | String  | device id                 |
| timestamp | Integer | timestamp of push message |
| etype     | String  | message type              |
| edata     | String  | message id                |

**Example**

```java
private ITuyaHomeCamera homeCamera;
private static ITuyaGetBeanCallback<CameraPushDataBean> mTuyaGetBeanCallback = new ITuyaGetBeanCallback<CameraPushDataBean>() {
    @Override
    public void onResult(CameraPushDataBean o) {
        L.d(TAG, "onMqtt_43_Result on callback");
        L.d(TAG, "timestamp=" + o.getTimestamp());
        L.d(TAG, "devid=" + o.getDevId());
        L.d(TAG, "msgid=" + o.getEdata());
        L.d(TAG, "msgtype=" + o.getEtype());
    }
};

/**
 * Register listening after successful account login
 */
public void registerCameraPushListener() {
    homeCamera = TuyaHomeSdk.getCameraInstance();
    if (homeCamera != null) {
        homeCamera.registerCameraPushListener(mTuyaGetBeanCallback);
    }
}

/**
 * Logout listening after account logout
 */
public void unRegisterCameraPushListener() {
    if (homeCamera != null) {
        homeCamera.unRegisterCameraPushListener(mTuyaGetBeanCallback);
    }
}
```

> **Note**: 
>
> 1. When the app process is killed, the listener is invalid. After the app logs in successfully, register for monitoring; the app logs out and cancels the monitoring
>
> 2. To avoid repeated push messages (user own channel pushed, generated by Tuya assist protocol), you can filter by message id, message type, and message arrival time