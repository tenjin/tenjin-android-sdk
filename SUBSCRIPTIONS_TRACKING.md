# Tenjin – Android Subscription Tracking

Track Google Play subscription purchases with Tenjin for server-side verification and attribution.

Requires Tenjin Android SDK **1.22.0+** and Google Play Billing Library **5.0+**.

> **Subscription tracking is opt-in.** Connecting/initializing the SDK does **not** automatically capture subscription or purchase events. You must explicitly call one of the methods below from your Play Billing purchase-handling code.
>
> **Troubleshooting:** if general events (session, custom events, etc.) are arriving on the dashboard but subscription events are not, this is the most common cause. Check that one of the methods below is actually being called at purchase time.

> **Dashboard prerequisite:** subscriptions are verified against the Google Play Developer API, so Tenjin needs Google Play Developer API access for your app, configured on the In-App Purchases & Subscriptions credentials page of the <a href="https://www.tenjin.com/dashboard/apps" target="_new">Tenjin dashboard</a>. Without those credentials the SDK request is accepted but the subscription is dropped before it reaches reporting. Contact support@tenjin.com if you are unsure whether your app is set up.

## Methods

### `subscription(Object purchase, ...)` — Play Billing `Purchase`

The recommended path. Pass the `com.android.billingclient.api.Purchase` you receive in
`PurchasesUpdatedListener`; the SDK reads the product ID, purchase token, purchase time, original
JSON and signature off it.

```java
TenjinSDK.getInstance(context, "<API_KEY>").subscription(
    Object purchase,   // Play Billing Purchase (Billing Library 5.0+)
    double price,      // Price (e.g., 9.99)
    String currency    // ISO 4217 currency code (e.g., "USD")
);
```

### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `purchase` | `Object` | Play Billing `Purchase` (Billing Library 5.0+) |
| `price` | `double` | Price (e.g., 9.99) |
| `currency` | `String` | ISO 4217 currency code (e.g., "USD") |

> **Note:** the parameter is typed `Object`, not `Purchase`, on purpose. Play Billing is an *optional* dependency of the Tenjin SDK (`compileOnly`, never bundled), and declaring a billing type in a public signature breaks apps that don't ship Play Billing. Just pass the `Purchase` — it is cast internally.

`price` and `currency` are **not** available on the `Purchase` object. Take them from the matching
`ProductDetails` pricing phase (`priceAmountMicros / 1_000_000.0` and `priceCurrencyCode`), which
you already have from `queryProductDetailsAsync`.

---

### `subscription(String productId, ...)` — Manual Parameters

Pass the purchase fields yourself. Use this when you don't have a Play Billing `Purchase` object —
for example when a third-party IAP library brokers the purchase, or when it is handled on your own
backend.

```java
TenjinSDK.getInstance(context, "<API_KEY>").subscription(
    String productId,      // Product ID
    String purchaseToken,  // Google Play purchase token
    double price,          // Price (e.g., 9.99)
    String currency,       // ISO 4217 currency code (e.g., "USD")
    long purchaseDate,     // Purchase time, epoch milliseconds
    String receipt,        // Purchase JSON (Purchase.getOriginalJson())
    String signature       // Purchase signature (Purchase.getSignature())
);
```

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `productId` | `String` | ✅ | Subscription product identifier |
| `purchaseToken` | `String` | ✅ | Google Play purchase token — how Tenjin resolves the subscription |
| `price` | `double` | | Price (e.g., 9.99) |
| `currency` | `String` | | ISO 4217 currency code (e.g., "USD") |
| `purchaseDate` | `long` | | Purchase time in epoch **milliseconds** |
| `receipt` | `String` | | Purchase JSON; URL-encoded by the SDK before sending |
| `signature` | `String` | | Purchase signature; URL-encoded by the SDK before sending |

`productId` and `purchaseToken` are required — the call is dropped (with a logcat warning) if
either is empty. The remaining fields are optional and are omitted when null or empty.

---

## What Tenjin sends

Both methods POST to `https://track.tenjin.com/v1/subscriptions` with the SDK's standard device and
app parameters plus:

| Parameter | Source |
|-----------|--------|
| `google_play_purchase_token` | `Purchase.getPurchaseToken()` |
| `product_id` | `Purchase.getProducts().get(0)` |
| `price` | your `price` argument |
| `currency` | your `currency` argument |
| `purchase_date` | `Purchase.getPurchaseTime()`, epoch ms |
| `receipt` | `Purchase.getOriginalJson()`, URL-encoded |
| `signature` | `Purchase.getSignature()`, URL-encoded |

`transaction_id` and `original_transaction_id` are deliberately **not** sent: Tenjin resolves them
server-side from the purchase token via the Google Play Developer API.

---

## Using Google Play Billing (Direct Integration)

### Handling Purchases

Send the subscription from your `PurchasesUpdatedListener` once the purchase reaches the
`PURCHASED` state. Acknowledge the purchase as well — Google Play refunds subscriptions that are
not acknowledged within three days.

```kotlin
import com.android.billingclient.api.*
import com.tenjin.android.TenjinSDK

class BillingManager(
    private val context: Context,
    private val billingClient: BillingClient,
) : PurchasesUpdatedListener {

    private val tenjin = TenjinSDK.getInstance(context, "<API_KEY>")

    // Cache ProductDetails from queryProductDetailsAsync; price/currency are not on Purchase.
    private val productDetailsCache = mutableMapOf<String, ProductDetails>()

    override fun onPurchasesUpdated(result: BillingResult, purchases: List<Purchase>?) {
        if (result.responseCode != BillingClient.BillingResponseCode.OK) return
        purchases?.forEach { handlePurchase(it) }
    }

    private fun handlePurchase(purchase: Purchase) {
        if (purchase.purchaseState != Purchase.PurchaseState.PURCHASED) return

        val productId = purchase.products.firstOrNull() ?: return
        val phase = productDetailsCache[productId]
            ?.subscriptionOfferDetails
            ?.firstOrNull()
            ?.pricingPhases
            ?.pricingPhaseList
            ?.firstOrNull()

        val price = (phase?.priceAmountMicros ?: 0L) / 1_000_000.0
        val currency = phase?.priceCurrencyCode.orEmpty()

        tenjin.subscription(purchase, price, currency)

        if (!purchase.isAcknowledged) {
            val params = AcknowledgePurchaseParams.newBuilder()
                .setPurchaseToken(purchase.purchaseToken)
                .build()
            billingClient.acknowledgePurchase(params) { /* handle result */ }
        }
    }
}
```

In Java:

```java
@Override
public void onPurchasesUpdated(BillingResult result, List<Purchase> purchases) {
    if (result.getResponseCode() != BillingClient.BillingResponseCode.OK || purchases == null) {
        return;
    }
    for (Purchase purchase : purchases) {
        if (purchase.getPurchaseState() != Purchase.PurchaseState.PURCHASED) {
            continue;
        }
        TenjinSDK.getInstance(context, "<API_KEY>").subscription(purchase, price, currencyCode);
    }
}
```

### Restored and Existing Purchases

`onPurchasesUpdated` only fires for purchases made in the current session. To also pick up
subscriptions bought on another device, or before your Tenjin integration shipped, send the ones
returned by `queryPurchasesAsync`:

```kotlin
val params = QueryPurchasesParams.newBuilder()
    .setProductType(BillingClient.ProductType.SUBS)
    .build()

billingClient.queryPurchasesAsync(params) { result, purchases ->
    if (result.responseCode == BillingClient.BillingResponseCode.OK) {
        purchases.forEach { handlePurchase(it) }
    }
}
```

---

## Notes

- **Call `connect()` first.** A subscription sent before `connect()` completes is queued and replayed once the session starts, but the SDK must be initialized.
- **Send once per subscription, not once per renewal.** Tenjin resolves renewals, trials and cancellations server-side from the purchase token via the Google Play Developer API. Repeat sends for the same purchase token are de-duplicated.
- **Play Billing stays optional.** `subscription(purchase, …)` logs an error and returns if the billing library isn't on the runtime classpath; the manual overload works without Play Billing at all.
- **Verify your integration** with the <a href="https://www.tenjin.com/dashboard/sdk_diagnostics">Live Test Device Data Tool</a>.

### Testing on a device

Google Play only surfaces subscription products for builds it recognizes:

- Upload the app to an internal/closed testing track. A locally installed debug build is debug-signed and Play rejects the purchase as "item not found".
- The test account must be listed as a **License tester** in the Play Console.
- The installed `versionCode` must be ≥ the latest on the track.
- The subscription needs an active base plan available in the tester's region.
