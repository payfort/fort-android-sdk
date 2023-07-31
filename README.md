# FORT Android SDK

# Intro

The FORT Mobile SDK allows Merchants to securely integrate the payment functions. It also allows Merchants to easily accept In-App payments. Instead of the traditional, time-consuming, and complex way of being redirected to the mobile browser to complete the payment, In-App payments can be completed through our FORT Mobile SDK. In turn, this gives the Merchant’s consumers a smooth, pleasing user-experience by using In-App payment functions through the native applications.


##  Installing the Mobile SDK

These are the first steps you need to follow to integrate the Amazon
Payment Services Android SDK into your Android application.

### Installing the Android Mobile SDK

You need to complete two steps to install the Amazon Payment Services
Android SDK.

**Step 1**

First, you need to add the repository to your build file. Add it in your
root build.gradle at the end of repositories:

```

allprojects {
  repositories {
    ...
    maven { url https://android-sdk.payfort.com }
  }
}



```


**Step 2**

Add the Add fortSDK to your build.gradle dependencies using this code:

```<div class="center-column"></div> 

apply plugin: 'com.android.application' 
android { ... } 
dependencies {
  implementation("com.amazon:fortpayment:+:release@aar"){
    transitive = true
}  
}


```

### Setting Android OS permissions

You need to set the following two permission so that the Android mobile
SDK works properly:

```<div class="center-column"></div> 

< uses-permission android:name="android.permission.INTERNET" />
< uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


```
# Getting Started

In order to start using FORT Android SDK you need to have an account on the FORT ,  click [here](https://github.com/payfort/fort-android-sdk/wiki)
 to follow the instructions.
