# BidmadSDK(v1.14.1)
### Shortcuts
1. [SDK Settings](#1-SDK-Settings)
    - [Gradle](#Gradle)
    - [AndroidManifest.xml](#AndroidManifest.xml)
2. [How to use](#2-How-to-use)
    - [Banner Ads](#Banner-Ads)
    - [Interstitial Ads](#Interstitial-Ads)
    - [Reward Ads](#Reward-Ads)
    - [Native Ads](#Native-Ads)
    - [Offerwall Ads](#Offerwall-Ads)
    - [RewardInterstitial Ads](#RewardInterstitial-Ads)
    - [AppOpen Ads](#AppOpen-Ads)
3. [Class Reference](#3-Class-Reference)
    - [Banner Class Reference](#Banner-Class-Reference)
    - [Interstitial Class Reference](#Interstitial-Class-Reference)
    - [Reward Class Reference](#Reward-Class-Reference)
    - [NativeAd Class Reference](#NativeAd-Class-Reference)
    - [Offerwall Class Reference](#Offerwall-Class-Reference)
    - [RewardInterstitial Class Reference](#RewardInterstitial-Class-Reference)
    - [AppOpen Class Reference](#AppOpen-Class-Reference)
4. [Note](#4-Note)
5. [Download the latest sample project](https://github.com/bidmad/Bidmad-Android/archive/master.zip)
---
### 1. SDK Settings
#### *Minimum requirements for using the SDK
- Gradle Plugin 3.5.4 or higher
- minSdkVersion 19 or higher

#### *Gradle
1. Declares the repository in the build.gradle file located at the top-level of the project.

```java
allprojects {
   repositories {
       ...
       google()
       jcenter()
       maven { url 'http://devrepo.kakao.com:8088/nexus/content/groups/public/' } //Adift
       maven {
          url "s3://repo.cauly.net/releases"
          credentials(AwsCredentials) {
              accessKey "AKIAWRZUK5MFKYVSUOLB"
              secretKey "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"
          }
       } //Cauly
       maven { url "https://bidmad-sdk.s3.amazonaws.com/" } //bidmad
       maven { url "https://sdk.tapjoy.com/" } //Tapjoy
       maven {url "https://artifact.bytedance.com/repository/pangle"} //Pangle
       maven {url "https://jitpack.io"} //Adpie

}
```
3. Declares SDK in dependencies of build.gradle file located in project App-Level.

```java
dependencies {
    ...
    implementation 'com.adop.sdk:bidmad-androidx:1.14.1'
}
```
4. Declare the options below in the android tag of the build.gradle file located in the project App-Level.

```java
android {
    ...
    defaultConfig {
        ...
        multiDexEnabled true
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

*Bidmad uses the AndroidX library. If it is not an AndroidX project, please migrate to AndroidX.

#### *AndroidManifest.xml

1. Declare the following code in the application tag of AndroidManifest.xml in the project.<br>
 *Please check the value of com.google.android.gms.ads.APPLICATION_ID on the Admob dashboard.

```xml
<application
   android:usesCleartextTraffic="true"
   ...
>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />
   <activity android:name="com.google.android.gms.ads.AdActivity" android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"/>
   <meta-data android:name="android.webkit.WebView.EnableSafeBrowsing" android:value="false" />
   <meta-data android:name="com.google.android.gms.ads.APPLICATION_ID" android:value="APPLICATION_ID"/>
   ...
</application>
```
2. Declare permission in AndroidManifest.xml in project.

```xml
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
```

### 2. How to use

#### *Banner Ads

1. Add a layout to expose the banner.
```java
...
<androidx.constraintlayout.widget.ConstraintLayout
    android:id="@+id/bannerLayout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"/>
...
```

2. To request a banner ad, create a BaseAdView, set the ZoneId, and call the load function.
3. Add a BaseAdView to the view created above to expose a banner ad.
```java
ConstraintLayout layout;
BaseAdView mAdView;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_banner);

    //Require
    mAdView = new BaseAdView(this);
    mAdView.setAdInfo("YOUR ZONE ID"); //ADOP ZONE ID Setting
    mAdView.setAdViewListener(new AdViewListener() {
        @Override
        public void onLoadAd() {
            //onLoad Callback
        }

        @Override
        public void onFailedAd() {
            //onFailedAd Callback
        }

        @Override
        public void onClickAd() {
            //onClickAd Callback
        }
    });

    // mAdView.setInterval(120); //Refresh Interval 60~120s
    mAdView.load(); //Banner Ad Load

    layout = findViewById(R.id.bannerLayout);
    layout.addView(mAdView); //attach Banner
}
```

#### *Interstitial Ads

1. To request interstitial ad, create BaseInterstitial, set ZoneId and call load function.
2. Call show to expose the interstitial ad. At this time, you need to check whether you have received an advertisement through isLoaded.
```java
BaseInterstitial mInterstitial;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_interstitial);

    //Require
    mInterstitial = new BaseInterstitial(this);
    mInterstitial.setAdInfo("YOUR ZONE ID");//ADOP ZONE ID Setting
    mInterstitial.setInterstitialListener(new InterstitialListener() {
        @Override
        public void onLoadAd() {
           //onLoad Callback
        }

        @Override
        public void onShowAd() {
            //onShowAd Callback
            mInterstitial.load(); //Ad Reload
        }

        @Override
        public void onFailedAd() {
           //onFailedAd Callback
        }

        @Override
        public void onCloseAd() {
            //onCloseAd Callback
        }
    });

    mInterstitial.load();

    //Ad Show Event Setting
    findViewById(R.id.showIntersitial).setOnClickListener(v -> {
        if(mInterstitial.isLoaded()){
            mInterstitial.show();
        }
    });
}
```

#### *Reward Ads

1. To request a rewarded advertisement, create a BaseReward, set the ZoneId, and call the load function.
2. Call show to expose rewarded advertisement. At this time, you need to check whether you have received an advertisement through isLoaded.
```java
BaseReward mReward;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_reward);

    //Require
    mReward = new BaseReward(this);
    mReward.setAdInfo("YOUR ZONE ID"); //ADOP ZONE ID Setting
    mReward.setRewardListener(new RewardListener() {
        public void onLoadAd(String zoneId) {
            //onLoad Callback
        }

        @Override
        public void onShowAd(String zoneId) {
            //onShowAd Callback
            mReward.load(); //Ad Reload
        }

        @Override
        public void onFailedAd(String zoneId) {
            //onFailedAd Callback
        }

        @Override
        public void onCompleteAd(String zoneId) {
            //onCompleteAd Callback
        }

        @Override
        public void onOpenAd(String zoneId) {
            //onOpenAd Callback
        }

        @Override
        public void onCloseAd(String zoneId) {
            //onCloseAd Callback
        }

        @Override
        public void onClickAd(String zoneId) {
            //onClickAd Callback
        }

        @Override
        public void onSkippedAd(String zoneId) {
            //onSkippedAd Callback
        }
    });

    mReward.load();

    //Ad Show Event Setting
    findViewById(R.id.showReward).setOnClickListener(v -> {
        if(mReward.isLoaded()){
            mReward.show();
        }
    });
}
```

#### *Native Ads

1. Configure the layout according to the [Native Ad Layout Setting Guide] (https://github.com/bidmad/Bidmad-Android/wiki/Native-Ad-Layout-Setting-Guide%5BENG%5D).
2. To request a native ad, we create a BaseNativeAd, set the layout configured through setNativeAdContainer and registerViewForInteraction, and call the load function.
3. Add BaseNativeAd to the View created above to expose native ads.
```java
    CustomNativeAdLayout layoutNative;
    BaseNativeAd nativeAd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_native);

        //Require
        nativeAd = new BaseNativeAd(this);
        nativeAd.setAdInfo("YOUR ZONE ID"); //ADOP ZONE ID Setting
        layoutNative = findViewById(R.id.native_ad_container);
        nativeAd.setNativeAdContainer(layoutNative, R.layout.newslist_native_item_ad);
        nativeAd.setNativeAdListener(new NativeListener() {
            @Override
            public void onSuccessHouseAd() {
                //onSuccessHouseAd Callback
            }

            @Override
            public void onSuccessAd() {
                //onSuccessAd Callback
            }

            @Override
            public void onFailedAd() {
                //onFailedAd Callback
            }

            @Override
            public void onClickedAd(){
                //onClickedAd Callback
            }
        });

        nativeAd.registerViewForInteraction(
                R.id.mediaView_common,
                R.id.mediaView,
                R.id.img_icon_common,
                R.id.img_icon,
                R.id.txt_body,
                R.id.txt_title,
                R.id.adCallToActionButton,
                R.id.adChoicesContainer,
                R.id.img_icon_privacy);

        nativeAd.load();
        layoutNative.addView(nativeAd);
```

#### *Offerwall Ads

1. In order to request offerwall advertisement, BaseOfferwall constructor is called, and if a response is received with onInitSuccess, then load is called.
2. Call show to provide a list of offer wall advertisements. At this time, you need to check whether you have received an advertisement through isLoaded.
3. In the case of offer wall advertisement, Currency are received according to whether the conditions for receive of Currency are met for advertisements provided in the list. The received Currency can be consumed through spendCurrency Function.
(*The received Currency can be checked through getCurrencyBalance Function.)
```java
BaseOfferwall mOfferwall;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_offerwall);

    //Require
    mOfferwall = new BaseOfferwall(this, "YOUR ZONE ID", new OfferwallInitListener() {
        @Override
        public void onInitSuccess() {
            //onInitSuccess Callback
        }

        @Override
        public void onInitFail(String error) {
            //onInitFail Callback
        }
    });

    mOfferwall.setOfferwallAdListener(new OfferwallAdListener() {
        @Override
        public void onLoadAd() {
            //onLoadAd Callback
        }

        @Override
        public void onShowAd() {
            //onShowAd Callback
        }

        @Override
        public void onFailedAd() {
            //onFailedAd Callback
        }

        @Override
        public void onCloseAd() {
            //onCloseAd Callback
        }
    });

    mOfferwall.setOfferwallCurrencyListener(new OfferwallCurrencyListener() {
        @Override
        public void onGetCurrencyBalanceSuccess(String currencyName, int balance) {
            //onGetCurrencyBalanceSuccess Callback
        }

        @Override
        public void onGetCurrencyBalanceFail(String error) {
            //onGetCurrencyBalanceFail Callback
        }

        @Override
        public void onSpendCurrencySuccess(String currencyName, int balance) {
            //onSpendCurrencySuccess Callback
        }

        @Override
        public void onSpendCurrencyFail(String error) {
             //onSpendCurrencyFail Callback
        }
    });

    findViewById(R.id.loadOfferwall).setOnClickListener(v -> {
        if(mOfferwall.isSDKInit()){
            mOfferwall.load();
        }
    });

    findViewById(R.id.showOfferwall).setOnClickListener(v -> {
        if(mOfferwall.isLoaded()){
            mOfferwall.show();
        }else{
            Log.d("", "Ad Not Load");
        }
    });

    findViewById(R.id.getCurrencyBalance).setOnClickListener(v -> {
        mOfferwall.getCurrencyBalance();
    });

    findViewById(R.id.spendCurrency).setOnClickListener(v -> {
        ...
        if(amount > 0){
            mOfferwall.spendCurrency(amount);
        }else{
            Log.d("","Please enter an amount greater than 0.");
        }
    });
}
```

#### *RewardInterstitial Ads

1. To request RewardInterstitial advertisement, call the BaseRewardInterstitial constructor and call the load function after setting the ZoneId.
2. Creates and calls Popup Class for expose RewardInterstitial advertisement. At this point, You should give the user enough time to read the instructions displayed in the popup and decide whether to watch the ad or not.
3. Call show when the user wants to watch an ad.<br>

*Interstitial compensatory advertisements should inform users in advance that advertisements will be exposed through popups, etc. You should provide an option for users not to see ads if they don't want to.
```java
BaseRewardInterstitial mRewardInterstitial;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_rewardinterstitial);

    //Require
    mRewardInterstitial = new BaseRewardInterstitial(this);
    mRewardInterstitial.setAdInfo("YOUR ZONE ID"); //ADOP ZONE ID Setting
    mRewardInterstitial.setRewardInterstitialListener(new RewardInterstitialListener() {
        @Override
        public void onLoadAd() {
            //onLoadAd Callback
        }

        @Override
        public void onShowAd() {
            //onShowAd Callback
            mRewardInterstitial.load(); //Ad Reload

        }
        @Override
        public void onFailedAd() {
            //onFailedAd Callback

        }
        @Override
        public void onCloseAd() {
            //onCloseAd Callback

        }
        @Override
        public void onSkipAd() {
            //onSkipAd Callback

        }
        @Override
        public void onCompleteAd() {
            //onCompleteAd Callback

        }
    });

    mRewardInterstitial.load();

    findViewById(R.id.popupCall).setOnClickListener(v -> {
        alertMessage();
    });
}
public void alertMessage(){   
    AlertPopup ap = new AlertPopup(this, new AlertPopup.OnClickListener() {
        @Override
        public void OnNegativeButton() {

        }

        @Override
        public void OnPositiveButton() {
            if(mRewardInterstitial.isLoaded()){
                mRewardInterstitial.show();
            }
        }
    });
    ap.show();
}

```

#### *AppOpen Ads

1. Call BaseAppOpenManager constructor to request AppOpenAd. At this time, set the ZoneId and set the advertisement Orientation option.
2. When start is called, BaseAppOpenManager requests and displays advertisements when onStart occurs according to the lifecycle of the application..<br>

*App open ads expose ads when the app state changes from background to foreground.<br>
*If you want to change the ad call according to lifecycle, implement AppOpen Ad using BaseAppOpen.
```java
BaseAppOpenManager mAppOpen;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_appopen);

    mAppOpen = new BaseAppOpenManager(this.getApplication(), "YOUR ZONE ID", BaseAppOpen.ORIENTATION_PORTRAIT);
    mAppOpen.setAppOpenListener(new AppOpenListener() {
        @Override
        public void onLoadAd() {
            //onLoadAd Callback
            mAppOpen.adShow();
        }
        @Override
        public void onShowAd() {
            //onShowAd Callback
        }
        @Override
        public void onFailedAd() {
            //onFailedAd Callback
        }
        @Override
        public void onCloseAd() {
            //onCloseAd Callback
        }
        @Override
        public void onExpired() {
            //onExpired Callback
            mAppOpen.adLoad();
        }
    });

    mAppOpen.start();
}
```

### 3. Class Reference
#### *Banner Class Reference

- BaseAdView

Function|Description
---|---
BaseAdView(Activity)|BaseAdView constructor.
void setAdInfo(String)|Set the Banner Ad ZoneId.
void setAdViewListener(AdViewListener)|Set listener to receive event callbacks for Banner ad.
void setInterval(int)| Set the banner ad refresh interval.(60s~120s)
void load()|Request a Banner ad. Re-requests the advertisement every set interval.
void onceLoad()|Request a Banner ad. Regardless of the set interval, only one request.
void setChildDirected(boolean)|If AD Network (ex:Admob) supports an interface for COPPA, it passes a value to the interface.
void onPause()|Pause Banner ads.
void onResume()|Resume Banner ads.

- AdViewListener

Function|Description
---|---
void onLoadAd(String networkName)|An event occurs when a banner ad is loaded. Returns the Ad NetworkName.
void onFailedAd()|An event occurs when a banner ad loading fails. 
void onClickedAd()|An event occurs when a banner ad is clicked.
---
#### *Interstitial Class Reference

- BaseInterstitial

Function|Description
---|---
BaseInterstitial(Activity)|BaseInterstitial constructor.
void setAdInfo(String)|Set the Interstitial Ad ZoneId.
void setInterstitialListener(InterstitialListener)|Set listener to receive event callbacks for interstitial ad.
void load()|Request an interstitial ad.
void show()|The loaded interstitial ad is displayed on the screen. 
boolean isLoaded()|Check whether interstitial ad is loaded or not.
void setMute()|Mute setup for video interstitial ads. This option only works on some Ad Networks.
void setChildDirected(boolean)|If AD Network (ex:Admob) supports an interface for COPPA, it passes a value to the interface.

- InterstitialListener

Function|Description
---|---
void onLoadAd()|An event occurs when an interstitial ad is loaded. 
void onShowAd()|An event occurs when an interstitial ad is shown. 
void onFailedAd()|An event occurs when interstitial ad loading fails. 
void onClickedAd()|An event occurs when a interstitial ad is clicked.
---
#### *Reward Class Reference

- BaseReward

Function|Description
---|---
BaseReward(Activity)|BaseReward constructor.
void setAdInfo(String)|Set Reward Ad ZoneId.
void setRewardListener(RewardListener)|Set listener to receive event callbacks for reward ad.
void load()|Request a reward ad.
void show()|The loaded reward ad is displayed on the screen. 
boolean isLoaded()|Checks whether the reward ad is loaded or not.
void setMute()|Perform Mute setting for reward ad. This option only works on some Ad Networks.
void setChildDirected(boolean)|If AD Network (ex:Admob) supports an interface for COPPA, it passes a value to the interface.

- RewardListener

Function|Description
---|---
void onLoadAd(String)|An event occurs when a reward ad is loaded, and the ZoneId is returned.
void onShowAd(String)|An event occurs when a reward ad is shown, an event occurs, and the ZoneId is returned.
void onFailedAd(String)|An event occurs when reward ad loading fails, and the ZoneId is returned.
void onCompleteAd(String)|In the reward ad, when the reward condition is satisfied, an event occurs and the ZoneId is returned.
void onSkippedAd(String)|In the reward ad, an event occurs when the ad ends when the reward condition is not satisfied and the ZoneId is returned.
void onOpenAd(String)|An event occurs when the video of the reward ad starts, and the ZoneId is returned.
void onCloseAd(String)|An event occurs when the reward ad ends, and the ZoneId is returned..
void onClickedAd(String)|An event occurs when a reward ad is clicked, and the ZoneId is returned.
---
#### *NativeAd Class Reference

- BaseNativeAd

Function|Description
---|---
BaseNativeAd(Activity)|BaseNativeAd constructor.
void setAdInfo(String)|Set the Native Ad ZoneId.
void setNativeListener(NativeListener)|Set listener to receive event callbacks for native ad.
void setNativeAdContainer(CustomNativeAdLayout, Int)| Set the layout (CustomNativeAdLayout) to expose native ad and the layout that composes ad image/text/button, etc.
void registerViewForInteraction(Int, Int, Int, Int, Int, Int, Int, Int, Int)|Register detailed elements for Layout that composes Native ad registered in setNativeAdContainer.
void load()|Request Native Ad.
void setMute()|Perform Mute setting for native advertisement. This option only works on some Ad Networks.
void setChildDirected(boolean)|If AD Network (ex:Admob) supports an interface for COPPA, it passes a value to the interface.

- NativeListener

Function|Description
---|---
void onSuccessHouseAd()|Deprecate event.
void onSuccessAd()|An event occurs when native ad is loaded. 
void onFailedAd()|An event occurs when loading of native ads fails.
void onClickedAd()|An event occurs when a native ad is clicked. 
---
#### *Offerwall Class Reference

- BaseOfferwall

Function|Description
---|---
BaseOfferwall(Activity activity, String zoneId, OfferwallInitListener listener)|BaseOfferwall constructor. Set the offerwall ad ZoneId and Listener to receive events for Init together.
void isSDKInit()|Check whether BaseOfferwall ad is initialize or not
void setOfferwallAdListener(OfferwallAdListener)|Set listener to receive event callbacks for Offerwall ad.
void setOfferwallCurrencyListener(OfferwallCurrencyListener)|Set listener so that you can receive event callbacks for received Currency as offerwall ad.
void load()|Request Offerwall ad.
void show()|The loaded Offerwall ad is displayed on the screen. 
boolean isLoaded()|Checks whether Offerwall ad is loaded or not.
int getCurrencyBalance()|Check the Currency received by the Offerwall ads.
void spendCurrency(int)|Spends the Currency received by the Offerwall ads.

- OfferwallInitListener

Function|Description
---|---
void onInitSuccess()|An event occurs when the Initialize operation performed when the BaseOfferwall constructor is called is successful. 
void onInitFail(String)|An event occurs when the Initialize operation performed when the BaseOfferwall constructor is called fails. Return Error Message.

- OfferwallAdListener

Function|Description
---|---
void onLoadAd()|An event occurs when Offerwall ad is loaded. 
void onShowAd()|When an Offerwall ad is shown, an event occurs and the ZoneId is returned.
void onFailedAd()|An event occurs when Offerwall ad load fails. 
void onCloseAd()|An event occurs when Offerwall ad ends. 

- OfferwallCurrencyListener

Function|Description
---|---
void onGetCurrencyBalanceSuccess(String, int)|An event occurs when the search for currency received through the Offerwall advertisement is successful. 
void onGetCurrencyBalanceFail(String)|An event occurs when the inquiry of the Currency received by the Offerwall advertisement fails. Return Error Message.
void onSpendCurrencySuccess(String, int)|An event occurs when the consumption of the Currency received by the Offerwall advertisement is successful. 
void onSpendCurrencyFail(String)|An event occurs when the consumption of the Currency received by the Offerwall advertisement fails. Return Error Message.
---
#### *RewardInterstitial Class Reference

- BaseRewardInterstitial

Function|Description
---|---
BaseRewardInterstitial(Activity activity)|BaseRewardInterstitial constructor.
void setAdInfo(String)|Set RewardInterstitial Ad ZoneId.
void setRewardInterstitialListener(RewardInterstitialListener)|Set up listener to receive event callbacks for RewardInterstitial ads.
void load()|Request RewardInterstitial Ads.
void show()|Display the loaded RewardInterstitial advertisement on the screen. 
boolean isLoaded()|Check whether RewardInterstitial ads are loaded.
void setMute()|Set Mute for RewardInterstitial ads. This option only works with some Ad Networks.
void setChildDirected(boolean)|If AD Network (ex:Admob) supports an interface for COPPA, it passes a value to the interface.

- RewardInterstitialListener

Function|Description
---|---
void onLoadAd()|Event occurs when the RewardInterstitial ad is loaded.
void onShowAd()|Event occurs when the RewardInterstitial ad is shown.
void onFailedAd()|Event occurs when the RewardInterstitial ad is fails.
void onCompleteAd()|Event occurs when the reward payment conditions are met in the RewardInterstitial ad.
void onSkippedAd()|Event occurs when the RewardInterstitial ad ends without the reward payment conditions being met.
void onCloseAd()|Event occurs when the RewardInterstitial ad ends.
---
#### *AppOpen Class Reference

- BaseAppOpenManager

Function|Description
---|---
BaseAppOpenManager(Application, String, int)|BaseAppOpenManager constructor. Set AppOpenAd ZoneId and Orientation.
void setAppOpenListener(AppOpenListener)|Set up a listener to receive event callbacks for AppOpen ads.
void setAppOpenLifecycleListener(AppOpenLifecycleListener)|Set up a listener to receive event callbacks for the Lifecycle.
void start()|Register a LifecycleObserver to request and expose AppOpen ads according to the Lifecycle.
void end()|Delete the registered LifecycleObserver.
void adLoad()|Request an AppOpen ad.
boolean isAdLoaded()|Check whether AppOpen ads are loaded.
void adShow(boolean)|Display the loaded AppOpen advertisement on the screen. 

- BaseAppOpen

Function|Description
---|---
BaseAppOpen(Activity)|BaseAppOpen constructor.
void setAdInfo(String)|Set AppOpen Ad ZoneId.
void setOrientation(int)|Set AppOpen Ad Orientation.
void setAppOpenListener(AppOpenListener)|Set up a listener to receive event callbacks for AppOpen ads.
void load()|Request AppOpen Ads.
void show()|Display the loaded AppOpen advertisement on the screen. 
boolean isLoaded()|AppOpen to check whether the ad is loaded.

- AppOpenListener

Function|Description
---|---
void onLoadAd()|Event occurs when the RewardInterstitial ad is loaded.
void onShowAd()|Event occurs when the RewardInterstitial ad is shown.
void onFailedAd()|Event occurs when the RewardInterstitial ad is fails.
void onCloseAd()|Event occurs when the RewardInterstitial ad ends.
void onExpired()|Event occurs when a show is call after 3 hours or more have elapsed after loading the AppOpen ad.

- AppOpenLifecycleListener

Function|Description
---|---
void onActivityForGround()|The event is fired when onActivityForGround of Application.ActivityLifecycleCallbacks is called. 
void onActivityCreated(Activity, Bundle)|The event is fired when onActivityCreated of Application.ActivityLifecycleCallbacks is called.
void onActivityStarted(Activity)|The event is fired when onActivityStarted of Application.ActivityLifecycleCallbacks is called. 
void onActivityResumed(Activity)|The event is fired when onActivityResumed of Application.ActivityLifecycleCallbacks is called. 
void onActivityPaused(Activity)|The event is fired when onActivityPaused of Application.ActivityLifecycleCallbacks is called. 
void onActivityStopped(Activity)|The event is fired when onActivityStopped of Application.ActivityLifecycleCallbacks is called. 
void onActivitySaveInstanceState(Activity, Bundle)|The event is fired when onActivitySaveInstanceState of Application.ActivityLifecycleCallbacks is called. 
void onActivityDestroyed(Activity)|The event is fired when onActivityDestroyed of Application.ActivityLifecycleCallbacks is called. 
---
#### *Common Class Reference

- Common

Function|Description
---|---
String getSDKVersion()|Get SDK version information.
void setDebugging(boolean)|When called with a true value, the log of the SDK is output.
void setGgTestDeviceid()|Register with Google TEST device to receive test ads for Google ads.

---
### 4. Note

- [GDPR Guide](https://github.com/bidmad/Bidmad-Android/wiki/Android-GDPR-Guide-%5BENG%5D)
