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

 The process to work with APS Android SDK Plugin is:
 # Create SDK token
 Android mobile SDK token URLs
These are the URLs you need to use when you request a mobile SDK token for your Android app:

Test Environment URL

https://sbpaymentservices.payfort.com/FortAPI/paymentApi

Production Environment URL

https://paymentservices.payfort.com/FortAPI/paymentApi

```
curl -H "Content-Type: application/json" -d
'{"command":"SDK_TOKEN","language":"en","access_code":"xxxxx","merchant_identifier":"xxxxx",,"language":"en","device_id":"ffffffff-a9fa-0b44-7b27-29e70033c587",
"signature":"7cad05f0212ed933c9a5d5dffa31661acf2c827a"}'
https://sbpaymentservices.payfort.com/FortAPI/paymentApi

```


# Standard Checkout Implementation

You can use the standard Amazon Payment Services Android SDK interface to display a standard payment UI screen. This option is customizable in two ways -- you can hide the loading screen, and some of the UI elements can be customized. We address standard UI customization options in this section.

1: Define a Callback Manager

Define and initialize an instance of the FortCallBackManager in your activity as follows:

```
private FortCallBackManager fortCallback= null;
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    if (fortCallback == null)
        fortCallback = FortCallback.Factory.create();
}
```



2: Attach the Callback to the Activity

You need to add the following statement to the onActivityResult function as follows:
```
@Override protected void onActivityResult(int requestCode, int resultCode, Intent  data){  
super.onActivityResult(requestCode, resultCode,  data); fortCallback.onActivityResult(requestCode,resultCode,data);
}
```




3: Call the Android Mobile SDK and Collecting the Android SDK request

The below code registers a new callback for a new request. The registerCallBack requires the following inputs:

```
public void registerCallback(
        Activity context, final FortRequest fortRequest, String
        environment, final int requestCode, final FortCallBackManager callbackManager, boolean   showLoading, final
        FortInterfaces.OnTnxProcessed callback)
```

The Java model/ bean of the Android Mobile SDK request is as follows:

```

public class FortRequest implements Serializable {
    private Map<String, Object> requestMap;
    private boolean showResponsePage;

    public Map<String, Object> getRequestMap() {
        return
                requestMap;
    }

    public void setRequestMap(Map<String, Object> requestMap) {
        this.requestMap = requestMap;
    }

    public boolean isShowResponsePage() {
        return showResponsePage;
    }

    public void setShowResponsePage(boolean showResponsePage) {
        this.showResponsePage = showResponsePage;
    }
}
```
4: Initiate a checkout request:
For every transaction that needs to be processed, do the following call and handle the callback methods upon your business flow:

Example for register callback:

```
FortSdk.getInstance().registerCallback(this,fortRequest,5,fortCallback, showLoading,new
    FortInterfaces.OnTnxProcessed() {
    @Override public void onCancel (Map < String, Object > requestParamsMap, Map < String, Object > responseMap)
    {       
    }

    @Override public void onSuccess (Map < String, Object > requestParamsMap, Map < String, Object > fortResponseMap)
    {
    }

    @Override public void onFailure (Map < String, Object > requestParamsMap, Map < String, Object > fortResponseMap)
    {    
    } 
}

```

## Standard checkout UI sample code
Below we include a complete sample code section illustrating the full checkout process when you use the standard checkout UI. The sample includes capturing payment card data, initializing the Amazon Payment Services Android SDK, submitting payment card data and processing a transaction.


```
public class PayFortSdkSample extends Activity {
    private FortCallBackManager fortCallback = null;
    String deviceId = "", sdkToken = "";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

     // create Fort callback instance
        fortCallback = FortCallback.Factory.create();

        // Generating deviceId
        deviceId = FortSdk.getDeviceId(PayFortSdkSample.this);
        Log.d("DeviceId ", deviceId);

        // prepare payment request
        FortRequest fortrequest = new FortRequest();
        fortrequest.setRequestMap(collectRequestMap("PASS_THE_GENERATED_SDK_TOKEN_HERE"));
        fortrequest.setShowResponsePage(true); // to [display/use] the SDK response page
        // execute payment request callSdk(fortrequest);
    }

    private Map<String, Object> collectRequestMap(String sdkToken) {
        Map<String, Object> requestMap = new HashMap<>();
        requestMap.put("command", "PURCHASE");
        requestMap.put("customer_email", "Sam@gmail.com");
        requestMap.put("currency", "SAR");
        requestMap.put("amount", "100");
        requestMap.put("language", "en");
        requestMap.put("merchant_reference", "ORD-0000007682");
        requestMap.put("customer_name", "Sam");
        requestMap.put("customer_ip", "172.150.16.10");
        requestMap.put("payment_option", "VISA");
        requestMap.put("eci", "ECOMMERCE");
        requestMap.put("order_description", "DESCRIPTION");
        requestMap.put("sdk_token", sdkToken);
        return requestMap;
    }

    private void callSdk(FortRequest fortrequest) {

        FortSdk.getInstance().registerCallback(PayFortSdkSample.this, fortrequest,
                FortSdk.ENVIRONMENT.TEST, 5, fortCallback, new FortInterfaces.OnTnxProcessed() {
                    @Override
                    public void onCancel(Map<String, Object> requestParamsMap, Map<String, Object> responseMap) {
                        Log.d("Cancelled ", responseMap.toString());
                    }
                    @Override
                    public void onSuccess(Map<String, String> requestParamsMap, Map<String, Object> fortResponseMap) {    //TODO: handle me
                        Log.i("Success ", fortResponseMap.toString());
                    }
                    @Override
                    public void onFailure(Map<String, String> requestParamsMap,
                                          Map<String, Object> fortResponseMap) {
                        Log.e("Failure ", fortResponseMap.toString());
                    }

                });


    }


    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        fortCallback.onActivityResult(requestCode, resultCode, data);
    }

}
```




# Custom Checkout Implementation
Alternatively, you can choose to build your own payment processing UI by custom-coding an in-app payment processing feature. With this mobile SDK feature we allow Amazon Payment Services merchants to integrate and implement a native app checkout experience without displaying a default payment screen.

Using this integration method your customers can securely enter their payment card details on a fully customized merchant checkout landing page using the your customized payment UI.

Stage 1: Generate an SDK token
You need to generate an SDK token before you can start processing payments using your custom payment processing UI. Refer to the SDK token section earlier in this document for instructions on creating an SDK token.

Stage 2: Creating the card components
You create your custom payment screen by using the following five components included in the Amazon Payment Services Android SDK:

Table of components
The Android Mobile SDK allows merchants to securely integrate the payment functions through Custom Components:

FortCardNumberView (Check CardBrand,CardNumber)

CardCvvView (Check if cvv match cardBrand)

CardExpiryView (Check date)

CardHolderNameView

PayfortPayButton (Collect data from previous Components)



## Card components


```
<!--CardNumber-->
   < com.payfort.fortpaymentsdk.views.FortCardNumberView
                android:id="@+id/etCardNumberView"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:boxBackgroundShape="outline"
                />
 
   <!--CardExpiryView-->
    < com.payfort.fortpaymentsdk.views.CardExpiryView
                    android:id="@+id/etCardExpiry"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    app:boxBackgroundShape="outline" />
 
              <!--CardCvvView-->
                < com.payfort.fortpaymentsdk.views.CardCvvView
                    android:id="@+id/etCardCvv"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    app:boxBackgroundShape="outline" />
 
        <!--CardHolderNameView-->
       < com.payfort.fortpaymentsdk.views.CardHolderNameView
                android:id="@+id/cardHolderNameView"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:boxBackgroundShape="outline" />


Stage 3: Initiate the actual payment.
PayfortPayButton

Responsible for collecting card data from Card Components above and Submit Successful payment with simple Few steps also it's had the capability to do Direct Pay without needs for Card Components



<!--PayfortPayButton-->
    < com.payfort.fortpaymentsdk.views.PayfortPayButton
        android:id="@+id/btntPay"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        />
```



## PayfortPayButton Methods

```
/**
 * Responsible for Save token or not
 * @param isRememberMe Boolean
 */
fun isRememberMeEnabled(isRememberMe: Boolean)

/**
 * Update Request After doing Setup
 * @param fortRequest FortRequest
 */
fun updateRequest(fortRequest: FortRequest)


/**
 * this Setup used with Pay with Full Components from outside SDK
 * @param environment String
 * @param request FortRequest
 * @param payComponents PayComponents
 * @param payFortCallback PayFortCallback
 */
fun setup(environment: String,
          request: FortRequest,
          payComponents: PayComponents,
          payFortCallback: PayFortCallback)

/**
 * this Setup used with DirectPay only
 * @param environment String
 * @param request FortRequest
 * @param payFortCallback PayFortCallback?
 */
fun setup(environment: String,
          request: FortRequest,
          payFortCallback: PayFortCallback?)
```



## Sample Code
In this example we explain how to use PayfortPayButton with Card Components in the custom UI screen.

Kotlin

```
//You Need to Create PayComponents Object that holds all Card Components
val payComponents = PayComponents(etCardNumberView, cvvView = etCardCvv, etCardExpiry, holderNameView = cardHolderNameView)

//then you need to Create FortRequest via FortRequest.class
val fortRequest= FortRequest() // fill all the parameters required 

//then you need to choose the environment Via FortSdk.ENVIRONMENT it's contains TEST and PRODUCTION ENVIRONMENTS 
val  environment=  FortSdk.ENVIRONMENT.TEST

// Finally you need to decleare a PayFortCallback 
var callback=  object : PayFortCallback {
    override fun startLoading() {   }
    override fun onSuccess(requestParamsMap: Map<String, Any>, fortResponseMap: Map<String, Any>) {}
    override fun onFailure(requestParamsMap: Map<String, Any>, fortResponseMap: Map<String, Any>) { }
}

btnPay.setup(environment, fortRequest, payComponents, callback)
```




Java

```
//You Need to Create PayComponents Object that holds all Card Components
PayComponents payComponents = new PayComponents(etCardNumberView,  etCardCvv, etCardExpiry,  cardHolderNameView);

//then you need to Create FortRequest via FortRequest.class
FortRequest fortRequest = new FortRequest(); // fill all the parameters required 

//then you need to chsose the environment Via FortSdk.ENVIRONMENT it's contains   TEST and PRODUCTION ENVIRONMENTS
String environment = FortSdk.ENVIRONMENT.TEST;

// Finally you need to decleare a PayFortCallback 
PayFortCallback callback = new PayFortCallback() {
    @Override
    public void startLoading() { }
    @Override
    public void onSuccess(@NotNull Map<String, ?> requestParamsMap, @NotNull Map<String, ?> fortResponseMap) { }
    @Override
    public void onFailure(@NotNull Map<String, ?> requestParamsMap, @NotNull Map<String, ?> fortResponseMap) { }
};

    btnPay.setup(environment, fortRequest, payComponents, callback)

```
