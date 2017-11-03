---
layout: page
title: Integrate Audience Network on iOS
description: Integrate Audience Network on iOS
pid: 1
top_nav_section: prebid-mobile
nav_section: prebid-mobile-facebook
---

<div class="bs-docs-section" markdown="1">

# Integrate Audience Network on iOS
{:.no_toc}

This page shows how to integrate demand from Facebook's [Audience Network SDK](https://developers.facebook.com/docs/audience-network) with your iOS app.  The method shown here introduces a new way of rendering the ad in the demand adapter's SDK.

It requires:

+ Setting up line items to work with the new targeting keys
+ Making changes to your existing Prebid line items (if any)
+ Adding some code to your app that uses reflection to call into the Audience Network SDK

See below for instructions.

* TOC
{:toc }

## Prerequisites

+ [Prebid Mobile for iOS](https://github.com/prebid/prebid-mobile-ios) already integrated with your app (<span style="color: rgb(255,0,0);">FIXME</span>: <strong>is this true?  or is this part of the prebid mobile sdk?  nicole said it was on a branch</strong>)
+ [A placement ID from your Facebook account](https://developers.facebook.com/docs/audience-network/getting-started/)

## Step 1. Set up Prebid Server

Get a [placement ID from your Facebook account](https://developers.facebook.com/docs/audience-network/getting-started/), and set up your Prebid Server account using the configuration shown below.

```javascript
[{
    "params": {
        "placementId": "1959066997713356_1959836684303054"
    },
    "bidder": "audienceNetwork"
}]
```

For full Prebid Server account setup instructions, see [Get Started with Prebid Server]({{site.baseurl}}/prebid-mobile/prebid-mobile-pbs.html).

## Step 2. Set up line items


### Overview

To take advantage of this new feature:

1. For existing line items, update them to add one more targeting keyword: `hb_creative_loadtype`, with the value set to `html`.

2. For new line items targeting Facebook, please add line items as shown below.

{: .alert.alert-warning :}
In order to show ads using Audience Network demand, you must:  
- Set up separate line items for iOS and Android  
- For each of iOS and Android, set up separate line items to target banners and interstitials  
- Tag all of your previously set up Prebid Mobile line items with the key `hb_creative_loadtype` set to `html`

+ [DFP](#DFP)
+ [MoPub](#MoPub)

### DFP

1. Create a new line item

2. In the line item's *Key-values* targeting, set `hb_creative_loadtype` set to `demand_sdk`

3. Add a creative to the line item.  Set the type to *SDK mediation*, and choose a size.

4. Set the creative's ad network to *Custom Event*

5. Set the creative's class name to:

   - `PrebidCustomEventBannerDFP` for banner demand
   - `PrebidCustomEventInterstitialDFP` for interstitial demand

### MoPub

1. Create a new line item

2. In line item's bid price keyword settings, set `hb_creative_loadtype` to `demand_sdk`.  Use `AND` statement to join this with the other Prebid keywords.

3. Set the line item's bidding type to *Network*, and choose *Custom Native Network*

4. Set the line item's *Class* to:

   - `PrebidCustomEventBannerDFP` for banner demand
   - `PrebidCustomEventInterstitialDFP` for interstitial demand

## Step 3. Add code to your app

Use the following Cocoapods to add the Audience Network demand to your existing Prebid Mobile setup:

```ruby
# For pubs using DFP as primary ad server
pod "PrebidDemandSDKLoaderForDFP"

# For pubs using MoPub as primary ad server
pod "PrebidDemandSDKLoaderForMoPub"
```

To enable Audience Network demand in your app call:

```objective-c
[[PrebidMobileDemandSDKLoaderSettings sharedInstance] enableDemandSources:@[@(PBDemandSourceFacebook)]];
```

This method uses reflection to check your Audience Network SDK installation to make sure it has the right methods available for Prebid Mobile to use.  If there is any issue, the application will crash during development.  This is intended behavior, so bugs don't make their way into your production release.

From there, assuming everything works correctly, use Prebid Mobile to load ads as you normally would, as described in [Code Integration for iOS]({{site.baseurl}}/prebid-mobile/code-integration-ios.html).

If you run into any issues, turn on logging as described in [Logging & Troubleshooting]({{site.baseurl}}/prebid-mobile/logging-troubleshooting-ios.html).

</div>
