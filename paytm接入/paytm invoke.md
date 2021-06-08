## Paytm App Invoke Android

### Inputs from client:

* 1. amount - 订单金额
* 2. orderid -paytm的订单id
* 3. txnToken(textToken) - 订单唯一标识
* 4. mid - paytm的唯一商户id

### Parameters passed to client:

* 1. Result Code - Success/Failure, Cancel Transaction, Invalid Input Parameters (See below)
* 2. Message - General message to explain above Result Code
* 3. Response - JSON response of transaction done. Merchant need to parse the JSON
to know whether transaction was successful or Failure  

### Result Codes:
* 0 - Cancel Transaction
* 1 - Success/Failure
* 100 - Invalid Input Parameters

## Code Snippet for integration:

### 1. Create Intent to open Paytm Activity on Merchant App,if not installed Paytm app,webview post data to postUrl,then redirect to payment webpage


> posturl  

```
https://securegw.paytm.in/theia/api/v1/showPaymentPage
```

method:`Post`

> payInfo:

```
{"textToken":"","orderId":"","mid":"","amount":0.0}
```


```
try {
    if (versionCompare(
            getPaytmVersion(activity.applicationContext) ?: "",
            "8.6.0"
        ) < 0
    ) {
        val bundle = Bundle()
        bundle.putDouble("nativeSdkForMerchantAmount", payInfo.optDouble("amount"))
        bundle.putString("orderid", payInfo.optString("orderId"))
        bundle.putString("txnToken", payInfo.optString("textToken"))
        bundle.putString("mid", payInfo.optString("mid"))
        val paytmIntent = Intent()
        paytmIntent.component =
            ComponentName(PAYTM_APP_PACKAGE, "net.one97.paytm.AJRJarvisSplash")
        // You must have to pass hard coded 2 here, Else your transaction would not proceed.
        paytmIntent.putExtra("paymentmode", 2)
        paytmIntent.putExtra("bill", bundle)
        activity.startActivityForResult(paytmIntent, PAYTM_REQUEST_CODE)
    } else {
        val paytmIntent = Intent()
        paytmIntent.component = ComponentName(
            PAYTM_APP_PACKAGE,
            "net.one97.paytm.AJRRechargePaymentActivity"
        )
        paytmIntent.putExtra("paymentmode", 2)
        paytmIntent.putExtra("enable_paytm_invoke", true)
        paytmIntent.putExtra("paytm_invoke", true)
        paytmIntent.putExtra(
            "price",
            payInfo.optDouble("amount").toString()
        ) //this is string amount

        paytmIntent.putExtra("nativeSdkEnabled", true)
        paytmIntent.putExtra("orderid", payInfo.optString("orderId"))
        paytmIntent.putExtra("txnToken", payInfo.optString("textToken"))
        paytmIntent.putExtra("mid", payInfo.optString("mid"))
        activity.startActivityForResult(paytmIntent, PAYTM_REQUEST_CODE)
    }
} catch (e: ActivityNotFoundException) {
    val postData = StringBuilder()
    val postUrl =
        URL_PAYTM_PRODUCTION + "?mid=" + payInfo.optString("mid") + "&orderId=" + payInfo.optString(
            "orderId"
        )
    postData.append("MID=").append(payInfo.optString("mid"))
        .append("&txnToken=").append(payInfo.optString("textToken"))
        .append("&ORDER_ID=").append(payInfo.optString("orderId"))
    if (activity is WebActivity) {
        activity.runOnUiThread {
            activity.getWebView().postUrl(postUrl, postData.toString().toByteArray())
        }
    }
}
```

```
private fun versionCompare(str1: String, str2: String): Int {
    if (TextUtils.isEmpty(str1) || TextUtils.isEmpty(str2)) {
        return 1
    }
    val vals1 = str1.split("\\.".toRegex()).toTypedArray()
    val vals2 = str2.split("\\.".toRegex()).toTypedArray()
    var i = 0
    //set index to first non-equal ordinal or length of shortest version string
    while (i < vals1.size && i < vals2.size && vals1[i]
            .equals(vals2[i], ignoreCase = true)
    ) {
        i++
    }
    //compare first non-equal ordinal number
    if (i < vals1.size && i < vals2.size) {
        val diff = Integer.valueOf(vals1[i])
            .compareTo(Integer.valueOf(vals2[i]))
        return Integer.signum(diff)
    }
    //the strings are equal or one string is a substring of the other
    //e.g. "1.2.3" = "1.2.3" or "1.2.3" < "1.2.3.4"
    return Integer.signum(vals1.size - vals2.size)
}
```

### 2. Receive output parameters in onActivityResult:

```
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent
data) {
if (requestCode == ActivityRequestCode && data != null) {
	val response = data.getStringExtra("response");
	val message = data.getStringExtra("nativeSdkForMerchantMessage");
	val jsonObject = JSONObject(response ?: "{}")
	val status = jsonObject.optString("STATUS", "")
	if (status.isNotBlank()) {
	    Toast.makeText(
	        context,
	        status.replace("TXN_", ""),
	        Toast.LENGTH_SHORT
	    ).show()
	}
}
```
   