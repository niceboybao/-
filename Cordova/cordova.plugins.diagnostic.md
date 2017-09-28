# Cordova插件diagnostic详解

- [概述](#1)

  - [安装](#1.1)

- [方法详解](#2)

  - [Android and iOS](#2.1)

    - [isLocationAuthorized()](#2.1.1)
    - [requestLocationAuthorization()](#2.1.2)
    - [isCameraAuthorized()](#2.1.3)
    - [requestCameraAuthorization()](#2.1.4)
    - [isMicrophoneAuthorized()](#2.1.5)
    - [requestMicrophoneAuthorization()](#2.1.6)
    - [isContactsAuthorized()](#2.1.7)
    - [requestContactsAuthorization()](#2.1.8)

  - [Only iOS](#Only-iOS)

    - [isCameraRollAuthorized()](#isCameraRollAuthorized)
    - [requestCameraRollAuthorization()](#requestCameraRollAuthorization)

# 1.概述

这款iOS、Android和Windows 10手机的cor陶/phonegap插件用于管理设备设置，比如位置、蓝牙和WiFi。它支持管理运行时权限、设备硬件和核心操作系统特性。

## 1.1安装

这里只针对cordova的打包平台android 或者ios做安装命令

```
$ cordova plugin add cordova.plugins.diagnostic
```

**cordova plugin ls** 即可插件是否安装完成

# 2.方法详解

Cordova diagnostic插件支持android，ios，windowPhone等移动平台，具体方法如下：(一下方法不够具体全面，全是在平时工作中遇到的一个整理，谢谢体贴)

## 2.1Android and iOS

(以下方法仅支持Android 和iOS)

### isLocationAuthorized()

检查应用程序是否被授权使用位置。

#### Parameters

- {Function} successCallback - The callback which will be called when operation is successful. This callback function is passed a single boolean parameter. On iOS this will return TRUE if application is authorized to use location either "when in use" (only in foreground) OR "always" (foreground and background). On Android this will return TRUE if the app currently has runtime authorisation to use location.
- {Function} errorCallback - The callback which will be called when operation encounters an error. This callback function is passed a single string parameter containing the error message.

#### Example usage

```
cordova.plugins.diagnostic.isLocationAuthorized(function(authorized){
    console.log("Location is " + (authorized ? "authorized" : "unauthorized"));
}, function(error){
    console.error("The following error occurred: "+error);
});
```

### 2.1.1requestLocationAuthorization()

请求应用程序的位置授权。

#### Parameters

- {Function} successCallback - Invoked in response to the user's choice in the permission dialog. It is passed a single string parameter which defines the [resulting authorisation status](#runtime-permission-statuses).
- {Function} errorCallback - The callback which will be called when operation encounters an error. This callback function is passed a single string parameter containing the error message.
- {String} mode - (iOS-only / optional) location authorization mode specified as a [locationAuthorizationMode constant](#locationauthorizationmode-constants). If not specified, defaults to `WHEN_IN_USE`.

#### Example iOS usage

```
cordova.plugins.diagnostic.requestLocationAuthorization(function(status){
    switch(status){
        case cordova.plugins.diagnostic.permissionStatus.NOT_REQUESTED:
            console.log("Permission not requested");
            break;
        case cordova.plugins.diagnostic.permissionStatus.DENIED:
            console.log("Permission denied");
            break;
        case cordova.plugins.diagnostic.permissionStatus.GRANTED:
            console.log("Permission granted always");
            break;
        case cordova.plugins.diagnostic.permissionStatus.GRANTED_WHEN_IN_USE:
            console.log("Permission granted only when in use");
            break;
    }
}, function(error){
    console.error(error);
}, cordova.plugins.diagnostic.locationAuthorizationMode.ALWAYS);
```

#### 2.1.2Example Android usage

```
cordova.plugins.diagnostic.requestLocationAuthorization(function(status){
    switch(status){
        case cordova.plugins.diagnostic.permissionStatus.NOT_REQUESTED:
            console.log("Permission not requested");
            break;
        case cordova.plugins.diagnostic.permissionStatus.GRANTED:
            console.log("Permission granted");
            break;
        case cordova.plugins.diagnostic.permissionStatus.DENIED:
            console.log("Permission denied");
            break;
        case cordova.plugins.diagnostic.permissionStatus.DENIED_ALWAYS:
            console.log("Permission permanently denied");
            break;
    }
}, function(error){
    console.error(error);
});
```

### 2.1.3isCameraAuthorized()

检查应用程序是否被授权使用照相机。

```
cordova.plugins.diagnostic.isCameraAuthorized(params);
cordova.plugins.diagnostic.isCameraAuthorized(successCallback, errorCallback, params)
cordova.plugins.diagnostic.isCameraAuthorized(successCallback, errorCallback, externalStorage)
```

#### Parameters

- {Object} params - (optional) parameters:

  - {Function} successCallback - The callback which will be called when operation is successful. This callback function is passed a single boolean parameter which is TRUE if camera is authorized for use.
  - {Function} errorCallback - The callback which will be called when operation encounters an error. This callback function is passed a single string parameter containing the error message.
  - {Boolean} externalStorage - (Android only) If true, checks permission for `READ_EXTERNAL_STORAGE` in addition to `CAMERA` run-time permission. Defaults to true.

#### Example usage

```
cordova.plugins.diagnostic.isCameraAuthorized({
    successCallback: function(authorized){
        console.log("App is " + (authorized ? "authorized" : "denied") + " access to the camera");
    },
    errorCallback: function(error){
        console.error("The following error occurred: "+error);
    },
    externalStorage: false
});

cordova.plugins.diagnostic.isCameraAuthorized(
    function(authorized){
        console.log("App is " + (authorized ? "authorized" : "denied") + " access to the camera");
    }, function(error){
        console.error("The following error occurred: "+error);
    }, {
        externalStorage: false
    }
);

cordova.plugins.diagnostic.isCameraAuthorized(
    function(authorized){
        console.log("App is " + (authorized ? "authorized" : "denied") + " access to the camera");
    }, function(error){
        console.error("The following error occurred: "+error);
    }, false
);
```

### requestCameraAuthorization()

Requests camera authorization for the application.

```
cordova.plugins.diagnostic.requestCameraAuthorization(params);
cordova.plugins.diagnostic.requestCameraAuthorization(successCallback, errorCallback, params)
cordova.plugins.diagnostic.requestCameraAuthorization(successCallback, errorCallback, externalStorage)
```

#### Parameters

- {Object} params - (optional) parameters:

  - {Function} successCallback - The callback which will be called when operation is successful. This callback function is passed a single string parameter indicating whether access to the camera was granted or denied: `cordova.plugins.diagnostic.permissionStatus.GRANTED` or `cordova.plugins.diagnostic.permissionStatus.DENIED`
  - {Function} errorCallback - The callback which will be called when operation encounters an error. This callback function is passed a single string parameter containing the error message.
  - {Boolean} externalStorage - (Android only) If true, requests permission for `READ_EXTERNAL_STORAGE` in addition to `CAMERA` run-time permission. Defaults to true.

#### Example usage

```
cordova.plugins.diagnostic.requestCameraAuthorization({
    successCallback: function(status){
        console.log("Authorization request for camera use was " + (status == cordova.plugins.diagnostic.permissionStatus.GRANTED ? "granted" : "denied"));
    },
    errorCallback: function(error){
        console.error(error);
    },
    externalStorage: false
});

cordova.plugins.diagnostic.requestCameraAuthorization(
    function(status){
        console.log("Authorization request for camera use was " + (status == cordova.plugins.diagnostic.permissionStatus.GRANTED ? "granted" : "denied"));
    }, function(error){
        console.error("The following error occurred: "+error);
    }, {
        externalStorage: false
    }
);

cordova.plugins.diagnostic.requestCameraAuthorization(
    function(status){
        console.log("Authorization request for camera use was " + (status == cordova.plugins.diagnostic.permissionStatus.GRANTED ? "granted" : "denied"));
    }, function(error){
        console.error("The following error occurred: "+error);
    }, false
);
```

### isMicrophoneAuthorized()

检查应用程序是否被授权使用麦克风权限。

```
cordova.plugins.diagnostic.isMicrophoneAuthorized(successCallback, errorCallback);
```

#### Parameters

- {Function} successCallback - The callback which will be called when operation is successful. This callback function is passed a single boolean parameter which is TRUE if microphone is authorized for use.
- {Function} errorCallback - The callback which will be called when operation encounters an error. This callback function is passed a single string parameter containing the error message.

#### Example usage

```
cordova.plugins.diagnostic.isMicrophoneAuthorized(function(authorized){
    console.log("App is " + (authorized ? "authorized" : "denied") + " access to the microphone");
}, function(error){
    console.error("The following error occurred: "+error);
});
```

### requestMicrophoneAuthorization()

请求应用程序的麦克风授权。

```
cordova.plugins.diagnostic.requestMicrophoneAuthorization(successCallback, errorCallback);
```

#### Parameters

- {Function} successCallback - The callback which will be called when operation is successful. This callback function is passed a single string parameter indicating whether access to the microphone was granted or denied: `cordova.plugins.diagnostic.permissionStatus.GRANTED` or `cordova.plugins.diagnostic.permissionStatus.DENIED`
- {Function} errorCallback - The callback which will be called when an error occurs. This callback function is passed a single string parameter containing the error message.

#### Example usage

```
cordova.plugins.diagnostic.requestMicrophoneAuthorization(function(status){
   if(status === cordova.plugins.diagnostic.permissionStatus.GRANTED){
       console.log("Microphone use is authorized");
   }
}, function(error){
    console.error(error);
});
```

### isContactsAuthorized()

检查应用程序是否被授权使用联系人(地址本)。

```
`cordova.plugins.diagnostic.isContactsAuthorized(successCallback, errorCallback);`
```

#### Parameters

- {Function} successCallback - The callback which will be called when operation is successful. This callback function is passed a single boolean parameter which is TRUE if contacts is authorized for use.
- {Function} errorCallback - The callback which will be called when operation encounters an error. This callback function is passed a single string parameter containing the error message.

#### Example usage

```
cordova.plugins.diagnostic.isContactsAuthorized(function(authorized){
    console.log("App is " + (authorized ? "authorized" : "denied") + " access to contacts");
}, function(error){
    console.error("The following error occurred: "+error);
});
```

### requestContactsAuthorization()

请求联系应用程序的授权。

```
cordova.plugins.diagnostic.requestContactsAuthorization(successCallback, errorCallback);
```

#### Parameters

- {Function} successCallback - The callback which will be called when operation is successful. This callback function is passed a single string parameter indicating whether access to contacts was granted or denied: `cordova.plugins.diagnostic.permissionStatus.GRANTED` or `cordova.plugins.diagnostic.permissionStatus.DENIED`
- {Function} errorCallback - The callback which will be called when an error occurs. This callback function is passed a single string parameter containing the error message.

#### Example usage

```
cordova.plugins.diagnostic.requestContactsAuthorization(function(status){
    if(status === cordova.plugins.diagnostic.permissionStatus.GRANTED){
        console.log("Contacts use is authorized");
    }
}, function(error){
    console.error(error);
});
```

## Only iOS

(以下方法仅支持iOS)

### isCameraRollAuthorized()

检查应用程序是否被授权在照片应用程序中使用相机胶卷。

```
cordova.plugins.diagnostic.isCameraRollAuthorized(successCallback, errorCallback);
```

#### Parameters

- {Function} successCallback - The callback which will be called when operation is successful. This callback function is passed a single boolean parameter which is TRUE if access to Camera Roll is authorized.
- {Function} errorCallback - The callback which will be called when operation encounters an error. This callback function is passed a single string parameter containing the error message.

#### Example usage

```
cordova.plugins.diagnostic.isCameraRollAuthorized(function(authorized){
    console.log("App is " + (authorized ? "authorized" : "denied") + " access to the camera roll");
}, function(error){
    console.error("The following error occurred: "+error);
});
```

### requestCameraRollAuthorization()

Requests camera roll authorization for the application. Should only be called if authorization status is NOT_REQUESTED. Calling it when in any other state will have no effect. When calling this function, the message contained in the `NSPhotoLibraryUsageDescription` .plist key is displayed to the user; this plugin provides a default message, but you should override this with your specific reason for requesting access - see the [iOS usage description messages](#ios-usage-description-messages) section for how to customise it.

```
cordova.plugins.diagnostic.requestCameraRollAuthorization(successCallback, errorCallback);
```

#### Parameters

- {Function} successCallback - The callback which will be called when operation is successful. This callback function is passed a single string parameter indicating the new authorization status: `cordova.plugins.diagnostic.permissionStatus.GRANTED` or `cordova.plugins.diagnostic.permissionStatus.DENIED`
- {Function} errorCallback - The callback which will be called when operation encounters an error. This callback function is passed a single string parameter containing the error message.

#### Example usage

```
cordova.plugins.diagnostic.requestCameraRollAuthorization(function(status){
    console.log("Authorization request for camera roll was " + (status == cordova.plugins.diagnostic.permissionStatus.GRANTED ? "granted" : "denied"));
}, function(error){
    console.error(error);
});
```
