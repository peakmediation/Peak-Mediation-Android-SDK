# Peak-Android-SDK

## Integration instructions 
Download peak-sdk-X.X.aar file and import it to your project:

  - File -> New -> New Module
  - Import .JAR/.AAR package
  - Pick the peak-sdk-X.X.aar file

Then include Google Play Services to your application's build.gradle for better compatibility:

        dependencies {
            ...
            compile 'com.google.android.gms:play-services-ads:8.4.0'
            compile 'com.google.android.gms:play-services-location:8.4.0'
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

        PeakSdk.showInterstitial(AD_ZONE_ID);

5. Get banner view and insert it to your banner container view:

        View banner = PeakSdk.showBanner(AD_ZONE_ID);
        if (banner != null) {
          bannerContainer.addView(banner);
        }

6. Show native ad:

        private void showNativeAd() {
            PeakNativeAd peakNativeAd = PeakSdk.showNativeAd(NATIVE_AD_ID);
            if (peakNativeAd != null) {
                //notify SDK that ad was shown
                PeakSdk.trackNativeAdShown(NATIVE_AD_ID);
                bindNativeAdToViews(peakNativeAd);
            }
        }

        private void bindNativeAdToViews(PeakNativeAd nativeAd) {
            //fill views with received native ad data
            RequestManager glide = Glide.with(this);
            glide.load(nativeAd.getMainImage()).into(mainImageView);
            glide.load(nativeAd.getIcon()).into(logoImageView);
            titleTextView.setText(nativeAd.getTitle());
            descriptionTextView.setText(nativeAd.getText());
            adActionButton.setText(nativeAd.getActionText());

            adActionButton.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    //notify SDK that ad was clicked by user
                    PeakSdk.handleNativeAdClicked(NATIVE_AD_ID);
                }
            });
        }