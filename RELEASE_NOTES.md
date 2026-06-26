# Changelog

All notable changes to this project will be documented in this file.

## 1.21.0 (2026-06-26)


### Features

* add persisted connect-dedup window to Android SDK
* cap in-memory event queue at 500 with FIFO eviction
* flush event queue on connect dedup


### Bug Fixes

* rename RELEASE_NOTES to RELEASE_NOTES.md in external repo sync
* set HTTP connect timeout to 30s and read timeout to 60s

## 1.20.0 (2026-06-10)


### Features

* Add subscriptions tracking support

## 1.19.0 (2026-05-27)


### Features

* Add `eventAdImpressionCustom:` method to send ILRD impressions for custom mediation providers without a dedicated Tenjin integration

## 1.18.0 (2026-04-21)


### Features

* integrated CloudX ILRD


### Bug Fixes

* change release-please type to simple

## [1.17.4]
- Add support for CloudX ILRD
- Improve advertising ID retrieval and fallback mechanism
- Fix crash on API 23 by replacing getOrDefault

## [1.17.3]
- Improve concurrency between `connect()` and `getAttributionInfo()` calls

## [1.17.2]
- Update compileSdkVersion and targetSdkVersion to improve support for plugins

## [1.17.1]
- Update Java version to improve support for plugins

## [1.17.0]
- Add User Profile - LiveOps Analytics metrics

## [1.16.8]
- Update CI processes

## [1.16.7]
- Add extra layer of security to HTTP requests

## [1.16.6]
- Improve the process for retrieving Install Referrer

## [1.16.5]
- Improved the process for retrieving Google Advertising ID (GAID)

## [1.16.3]
- Update Meta Install Referrer implementation

## [1.16.2]
- Adjust attribution retry times
- Deprecate `eventWithNameAndValue` receiving string value

## [1.16.1]
- Add support for Meta Install referrer

## [1.15.1]
- Add opt out (`optOutGoogleDMA`) and opt in (`optInGoogleDMA`) methods for Google DMA

## [1.15.0]
- Capture and send Google DMA parameters `ad_personalization` and `ad_user_data`
- Add `deferred_deeplink_url` and `click_id` to `getAttributionInfo` method

## [1.14.0]
- Add `optInOutUsingCMP` method to manage GDPR opt-in/opt-out through CMP consents

## [1.13.0]
- Added Amazon IAP transactions

## [1.12.24]
- Added support for Java 8

## [1.12.23]
- Added TradPlus Impression Level Ad Revenue

## [1.12.22]
- Added CAS Impression Level Ad Revenue

## [1.12.21]
- Added support for retry/cache of events and IAP
- Added `remote_campaign_id` parameter to `getAttributionInfo()` response

## [1.12.20]
- Added new methods `setCustomerUserId` and `getCustomerUserId`

## [1.12.19]
- Added `creative_name` and `site_id` parameters to `getAttributionInfo()` response

## [1.12.18]
- Improved `getAttributionInfo` retry logic

## [1.12.17]
- Improved `getAttributionInfo` retry logic
- Improved Google Ads dependency management

## [1.12.16]
- Added retry logic in case `getAttributionInfo` data can't be fetched

## [1.12.15]
- Bug fix for not getting `advertising_id` on Huawei devices

## [1.12.14]
- Bug fix for non-numeric values in `publisher_revenue_decimal` and `publisher_revenue_micro` ILRD parameters
- Deprecate support for mopub ILRD

## [1.12.13]
- TopOn Impression Level Ad Revenue

## [1.12.12]
- AdMob Impression Level Ad Revenue
- Added public method `getAttributionInfo`

## [1.12.11]
- HyperBid Impression Level Ad Revenue

## [1.12.10]
- IronSource Impression Level Ad Revenue
- Refactor tenjin paramter ad_revenue_network to ad_revenue_mediation

## [1.12.9]
- AppLovin Impression Level Ad Revenue
- Refactor AdvertiserInfo class with new set methods for advertisingId and limitAdTracking
- Bugfix for referral parameter not being sent on first start of the SDK

## [1.12.8]
- Update play-services-ads-identifier to 17.1.0
- Add support for AndroidX
- Downgrade dependencies and project settings
    - Gradle version from 7.3 to 6.5
    - Android Gradle Plugin version from 7.0.3 to 3.6.4
    - Android Build Tools version from 30.0.2 to 29.0.2
    - Android Compile Sdk version from 30 to 29
    - Java support version from 11 to 8
    - junit from 4.13.2 to  4.12
    - mockito-core from 4.0.0 to 1.10.19
- Internal test app updates
  - Update espresso-core from 3.0.2 to 3.1.0
  - Update support:multidex from 1.0.1 to 1.0.3

## [1.12.7]
- Replace depcreated play-services- analytics with play-services-ads-identifier
- Update dependencies and project settings
    - Gradle version from 6.5 to 7.3
    - Android Gradle Plugin version from 3.6.4 to 7.0.3
    - Android Build Tools version from 29.0.2 to 30.0.2
    - Android Compile Sdk version from 29 to 30
    - Java support version from 8 to 11
    - installreferrer from 1.0 to 2.2
    - junit from 4.12 to 4.13.2
    - mockito-core from 1.10.19 to 4.0.0
    - espresso-core from 2.2.2 to 3.0.2

## [1.12.6]
- Fix MSA OAID

## [1.12.5]
- Support MSA OAID

## [1.12.4]
- Support OAID
- Huawei Install Referrer Support

## [1.12.3]
- Add JAR file back to build

## [1.12.2]
- Remove tools:replace

## [1.12.1]
- Clean up AndroidManifest.xml to allow tools:replace

## [1.12.0]
- Redesign parameter collection
- Heavy refactoring of TenjinSDK class / file.
- Reduce time to first request
- Async startup operations running from HandlerThread instance, instead of AsyncEvent

## [1.11.0]
- Prepend wrapper version to sent sdk_version on outgoing requests

## [1.10.2]
- CI Unification

## [1.10.1]
- MoPub Impression Level Ad Revenue

## [1.9.4]
- CI integration

## [1.8.3]
- Added support for Google Play Install Referrer: https://developer.android.com/google/play/installreferrer/library.html

## [1.8.2]
- Bug fixes: Fix double encoding, Clean up parameters

## [1.8.1]
- Bug fixes: Fix double encoding, Clean up parameters

## [1.8.0]
- Bug fixes: Fix double encoding, Clean up parameters

## [1.7.9]
- Add App Device User Agent params

## [1.7.8]
- Encode POST params

## [1.7.7]
- Properly queue HTTP calls. Make sure `connect` runs before all other events.  Eliminate duplicate calls.

## [1.7.6]
- Fix crashes in HttpConnection: http://crashes.to/s/d335e1fc6a7, http://crashes.to/s/3cdaa402bd5

## [1.7.5]
- Move to HTTPS

## [1.7.4]
- Make sure deferred deeplinks are propery decoded and encoded once

## [1.7.3]
- Always send getDeeplink callback regardless of having deeplink

## [1.7.2]
- Add logic to retry requests for non-200 response.
- Add logic for `getUser` thread to only happen after `connect` thread

## [1.7.1]
- Add native attribution deeplink url support for tenjin sdk

## [1.5.0]
- Add jar delete/build tasks
- Allow users who use the Facebook SDK to fetch a deeplink to pass in the deferred deeplink from FB.  (Added `deeplink_url` parameter for deferred deeplink)
- Add `timezone` parameter for device timezone
- Handle HTTP errors in HttpConnection

## [1.4.2]
- Add `device` parameter to pass `MANUFACTURER` and `MODEL`

## [1.4.1]
- Make sure to check params are valid before sending events
- Add a method to accept `int` values in `eventWithNameAndValue`

## [1.4.0]
- Include Amazon app store support

## [1.3.0]
- Add overloaded `transaction` method that accepts `purchaseData` and `signature` to validate in app purchases

## [1.2.0]
- Check to make sure that eventValue is an integer

## [1.1.1]
- Pass referrer on the first ping for referral installs

## [1.1.0]
- Allow SDK to accept INSTALL_REFERRER and pass it on connect
- Update the OS_VERSION to reflect the integer version of the OS_VERSION

## [1.0.2]
- Update for POST instead of GET
- Add transaction API for sending data to `/purchase` API - includes currency, quantity, unit price, and productID

## [1.0.1]
- Refactoring for improvements on event and value API

## [1.0.0]
- Base API for adding events
