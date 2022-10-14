v1.0.0
----
- Base API for adding events

v1.0.1
----
- Refactoring for improvements on event and value API

v1.0.2
----
- Update for POST instead of GET
- Add transaction API for sending data to `/purchase` API - includes currency, quantity, unit price, and productID

v1.1.0
----
- Allow SDK to accept INSTALL_REFERRER and pass it on connect
- Update the OS_VERSION to reflect the integer version of the OS_VERSION

v1.1.1
----
- Pass referrer on the first ping for referral installs

v1.2.0
----
- Check to make sure that eventValue is an integer

v1.3.0
----
- Add overloaded `transaction` method that accepts `purchaseData` and `signature` to validate in app purchases

v1.4.0
----
- Include Amazon app store support

v1.4.1
----
- Make sure to check params are valid before sending events
- Add a method to accept `int` values in `eventWithNameAndValue`

v1.4.2
----
- Add `device` parameter to pass `MANUFACTURER` and `MODEL`

v1.5.0
----
- Add jar delete/build tasks
- Allow users who use the Facebook SDK to fetch a deeplink to pass in the deferred deeplink from FB.  (Added `deeplink_url` parameter for deferred deeplink)
- Add `timezone` parameter for device timezone
- Handle HTTP errors in HttpConnection

v1.7.1
----
- Add native attribution deeplink url support for tenjin sdk

v1.7.2
----
- Add logic to retry requests for non-200 response.
- Add logic for `getUser` thread to only happen after `connect` thread

v1.7.3
----
- Always send getDeeplink callback regardless of having deeplink

v1.7.4
----
- Make sure deferred deeplinks are propery decoded and encoded once

v1.7.5
----
- Move to HTTPS

v1.7.6
----
- Fix crashes in HttpConnection: http://crashes.to/s/d335e1fc6a7, http://crashes.to/s/3cdaa402bd5

v1.7.7
----
- Properly queue HTTP calls. Make sure `connect` runs before all other events.  Eliminate duplicate calls.

v1.7.8
----
- Encode POST params

v1.7.9
----
- Add App Device User Agent params

v1.8.0 - v1.8.2
----
- Bug fixes:
    * Fix double encoding
    * Clean up parameters

v1.8.3
----
Added support for Google Play Install Referrer: https://developer.android.com/google/play/installreferrer/library.html

v1.9.3
----
- Fix bug where not correctly closing BufferedReader

v1.10.2
----
- CI Unification

v1.11.0
----
- Prepend wrapper version to sent sdk_version on outgoing requests

v1.12.0
----
- Redesign parameter collection
- Heavy refactoring of TenjinSDK class / file.
- Reduce time to first request
- Async startup operations running from HandlerThread instance, instead of AsyncEvent

v1.12.1
----
- Clean up AndroidManifest.xml to allow tools:replace

v1.12.2
----
- Remove tools:replace

v1.12.3
----
- Add JAR file back to build

v1.12.4
----
- Support OAID
- Huawei Install Referrer Support

v1.12.5
----
- OAID Support

v1.12.6
----
- Fix MSA OAID

v1.12.7
----
- Replace depcreated play-services-analytics with play-services-ads-identifier
- Update Android dependencies
    - Java support version from 8 to 11
    - installreferrer from 1.0 to 2.2
    - Android Compile Sdk version from 29 to 30
    - Gradle version from 6.5 to 7.3
    - Android Gradle Plugin version from 3.6.4 to 7.0.3

v1.12.8
----
- Update play-services-ads-identifier from 16.0.0 to 17.1.0
- Add support for AndroidX
- Downgrade dependencies and project settings
    - Gradle version from 7.3 to 6.5
    - Android Gradle Plugin version from 7.0.3 to 3.6.4
    - Android Build Tools version from 30.0.2 to 29.0.2
    - Android Compile Sdk version from 30 to 29
    - Java support version from 11 to 8
    - junit from 4.13.2 to  4.12
    - mockito-core from 4.0.0 to 1.10.19

v1.12.9
----
- Refactor AdvertiserInfo class with new set methods for advertisingId and limitAdTracking
- Bugfix for referral parameter not being sent on first start of the SDK
- AppLovin Impression Level Ad Revenue

v1.12.10
----
- IronSource Impression Level Ad Revenue
- Refactor tenjin paramter ad_revenue_network to ad_revenue_mediation

v1.12.11
----
- HyperBid Impression Level Ad Revenue

v1.12.12
----
- AdMob Impression Level Ad Revenue
- Added public method `getAttributionInfo`

v1.12.13
----
- TopOn Impression Level Ad Revenue

v1.12.14
----
- Bug fix for non-numeric values in `publisher_revenue_decimal` and `publisher_revenue_micro` ILRD parameters
- Deprecate support for mopub ILRD

v1.12.15
----
- Bug fix for not getting `advertising_id` on Huawei devices

v1.12.16
----
- Added retry logic in case `getAttributionInfo` data can't be fetched

v1.12.17
----
- Improved `getAttributionInfo` retry logic
- Improved Google Ads dependency management

v1.12.18
----
- Improved `getAttributionInfo` retry logic
