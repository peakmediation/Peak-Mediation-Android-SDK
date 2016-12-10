# Peak-Android-SDK

## Integration instructions 
Download peak-sdk-X.X.aar file and import it to your project:

  - File -> New -> New Module
  - Import .JAR/.AAR package
  - Pick the peak-sdk-X.X.aar file

Then include Google Play Services to your application's build.gradle for better compatibility:

        dependencies {
            ...
            compile 'com.google.android.gms:play-services-ads:9.4.0'
            compile 'com.google.android.gms:play-services-location:9.4.0'
            ...
        }


## Using Peak sdk

1. Create an instance of PeakSdkUiHelper in activity and call PeakSdkUiHelper.resume(), PeakSdkUiHelper.destroy() and PeakSdkUiHelper.pause() in appropriate activity methods:
        
        ...
        PeakSdkUiHelper uiHelper = new PeakSdkUiHelper(MainActivity.this);
        ...
        
        @Override
        protected void onPause() {
          uiHelper.pause();
          super.onPause();
        }
         
        @Override
        protected void onResume() {
          super.onResume();
          uiHelper.resume();
        }
         
        @Override
        protected void onDestroy() {
          uiHelper.destroy();
          super.onDestroy();
        }

2. Initialize peak sdk in your activity with your application id, PeakSdkUiHelper and PeakSdkListener. Use null for PeakSdkListener if you don't want to handle callbacks:

        PeakSdk.initialize(PEAK_APP_ID, uiHelper, peakSdkListener);

3. Check ad availability for specified zone:

        boolean isAdAvailable = PeakSdk.checkAdAvailable(AD_ZONE_ID);

4. Show interstitial ad:

        if(PeakSdk.checkAdAvailable(AD_ZONE_ID)) {
            PeakSdk.showInterstitial(AD_ZONE_ID);
        }

5. Get banner view and insert it to your banner container view:

        if(PeakSdk.checkAdAvailable(AD_ZONE_ID)) {
            View banner = PeakSdk.showBanner(AD_ZONE_ID);
            if (banner != null) {
                bannerContainer.addView(banner);
            }
        }

6. Show native ad:

        private void showNativeAd() {
            if(PeakSdk.checkAdAvailable(AD_ZONE_ID)) {
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
  
7. Using async ad requests
  
        PeakAsyncAdRequestListener asyncAdRequestListener = new PeakAsyncAdRequestListener() {
            @Override
            public void onAdReady(String adZoneId) {
                //Show the ad with adZoneId
                //Request stops itself when the ad is ready.
            }
        };
        
        ..
        //Create an instance of PeakAsyncAdRequest with desired ad zone id and start it
        PeakAsyncAdRequest asyncAdRequest = PeakSdk.createAdRequest(AD_ZONE_ID);
        asyncAdRequest.start(asyncAdRequestListener);
        ..
        
        @Override
        protected void onPause() {
          ...
          //Cancel the request if you don't need it anymore
          asyncAdRequest.cancel();
          super.onPause();
        }
        
8. Using ad targeting.

    Peak SDK provides a way to set PREFFERED targeting age and gender to inrease eCPM. This will not restrict ads to show only targeted ads. 
    Please set targeting BEFORE the first sdk initialization.
    
    setTargetingAge(int age) accepts any number. By default it is set to -1, this value defines unspecified targeting age.
    
    setTargetingGender(PeakGender gender) accepts PeakGender enum that has three states : MALE, FEMALE and UNSPECIFIED. By default it is set to UNSPECIFIED.


