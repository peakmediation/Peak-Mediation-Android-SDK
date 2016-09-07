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
                PeakNativeAd peakNativeAd = PeakSdk.showNativeAd(NATIVE_AD_ID);
                if (peakNativeAd != null) {
                    //notify SDK that ad was shown
                    PeakSdk.trackNativeAdShown(NATIVE_AD_ID);
                    bindNativeAdToViews(peakNativeAd);
                }
            }
        }

        private void bindNativeAdToViews(PeakNativeAd nativeAd) {
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