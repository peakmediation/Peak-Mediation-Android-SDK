# Peak-Android-SDK

## Integration instructions 

Add to your root build.gradle: 
```
    allprojects {
       repositories {
           ...
       	   maven {
            	url "http://repos.peakmediation.com:8081/artifactory/libs-release-local/"
	   }
	   ...
	}
    }
```

Add to your application's build.gradle: 
```
    dependencies {
        ...
        compile 'com.peaksdk:sdk:0.21.0'
        ...
    }
```


Then include Google Play Services for better compatibility:

    dependencies {
        ...
        compile 'com.google.android.gms:play-services-ads:9.4.0'
        compile 'com.google.android.gms:play-services-location:9.4.0'
        ...
    }


## Using Peak sdk

1. Initialize peak sdk in your **activity where you want to show ads** with your application id and PeakSdkListener. Use null for PeakSdkListener if you don't want to handle callbacks:

        PeakSdk.initialize(PEAK_APP_ID, MyActivity.this, peakSdkListener);

2. Check ad availability for specified zone:

        boolean isInterstitialAdAvailable = PeakSdk.checkInterstitialAdAvailable(AD_ZONE_ID);
        
        or        
        
        boolean isNativeAdAvailable = PeakSdk.checkNativeAdAvailable(AD_ZONE_ID);

3. Show interstitial ad:

        if(PeakSdk.checkInterstitialAdAvailable(AD_ZONE_ID)) {
            PeakSdk.showInterstitial(AD_ZONE_ID);
        }
        
    **Please don't try to show ads on activity startup** because some ads with possible higher eCPM start to load on initialization with activity and it takes some time to fetch ads. If you want to show the ad when a new activity starts, show it before you start a new activity and then you can start the activity the interstitial is closed.


4. Get banner view and insert it to your banner container view:

        View banner = PeakSdk.showBanner(AD_ZONE_ID);
        if (banner != null) {
            bannerContainer.addView(banner);
        }

5. Show native ad:

        private void showNativeAd() {
            if(PeakSdk.checkNativeAdAvailable(AD_ZONE_ID)) {
                PeakNativeAdModel peakNativeAd = PeakSdk.showNativeAd(NATIVE_AD_ID);
                if (peakNativeAd != null) {
                    //notify SDK that ad was shown
                    PeakSdk.trackNativeAdShown(NATIVE_AD_ID);
                    bindNativeAdToViews(peakNativeAd);
                }
            }
        }
        
        private void bindNativeAdToViews(PeakNativeAdModel nativeAd) {
            //fill views with received native ad data
            
            // load the nativeAd.getMainImage() into your ImageView for the main image
            // load the nativeAd.getIcon() into your ImageView for icon image
            // set the nativeAd.getPrivacyIcon() into ImageView for privacy information icon image
            // set the nativeAd.getTitle() into you TextView for title
            // set the nativeAd.getText() into you TextView for description text
            // set the nativeAd.getActionText() into your Button
            // set onClickListener on your button 
            button.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    //notify SDK that ad was clicked by user
                    PeakSdk.handleNativeAdClicked(NATIVE_AD_ID);
                }
            });
            
            // set onClickListener on privacy information icon ImageView
            privacyInformationIconImageView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    //notify SDK that ad was clicked by user
                    PeakSdk.handleNativeAdPrivacyIconClicked(NATIVE_AD_ID);
                }
            });
        }
    
    Make sure your ads are clearly distinguished from the rest of your app’s content. You must include clearly visible text that reads “Promoted,” “Sponsored,” “Ad,” or a localized version thereof.
  
6. Using async ad requests
  
        PeakAsyncInterstitialAdRequestListener asyncInterstitialAdRequestListener = new PeakAsyncInterstitialAdRequest.AsyncAdRequestListener() {
            @Override
            public void onAdReady(String adZoneId) {
                //Show the ad with adZoneId
                //Request stops itself when the ad is ready.
            }
        };
        
        ..
        //Create an instance of PeakAsyncInterstitialAdRequest with desired ad zone id and start it
        PeakAsyncInterstitialAdRequest asyncInterstitialAdRequest = PeakSdk.createInterstitialAdRequest(AD_ZONE_ID);
        asyncInterstitialAdRequest.start(asyncInterstitialAdRequestListener);
        ..
        
        @Override
        protected void onPause() {
          ...
          //Cancel the request if you don't need it anymore
          asyncInterstitialAdRequest.cancel();
          super.onPause();
        }
        
    Please use PeakAsyncNativeAdRequestListener for native ads. Notice that PeakAdRequestListener class and PeakSdk.createAdRequest(String zoneId) method are deprecated now.

7. Using ad targeting.

  Peak SDK provides a way to set **preffered** targeting age and gender to inrease eCPM. This will not restrict ads to show only targeted ads. 
  **Please set targeting BEFORE the first sdk initialization.**
    
  **PeakSdk.setTargetingAge(int age)** accepts any number. By default it is set to -1, this value defines unspecified targeting age.    
  **PeakSdksetTargetingGender(PeakGender gender)** accepts *PeakGender* enum that has three states : MALE, FEMALE and UNSPECIFIED. By default it is set to UNSPECIFIED.
  
8. Permissions

Peak Android SDK needs permissions android.permission.WRITE_EXTERNAL_STORAGE and android.permission.ACCESS_FINE_LOCATION but on new Android versions (6.0+) these permissions are not granted for app by default. To make it work correct need to add runtime permissions request in app or manually grant these permissions in app settings.


