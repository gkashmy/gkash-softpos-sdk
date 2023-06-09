# Gkash Payment SDK for Android

This library allows you to integrate Gkash Payment Gateway into your Android App.


## Usage

Ensure jcenter is in the root level build.gradle file of an Android Project.

```Gradle
repositories {
    mavenCentral()
}
```


Include the library reference in the build.gradle file.

```Gradle
dependencies {
     implementation 'io.github.gkashmy:gkash-softpos-sdk:2.0.1'
}
```


Implement the library as follows. 

```Java
//Configure Config
GkashSDKConfig gkashSDKConfig = new GkashSDKConfig().setUsername(username).setPassword(password).setTestingEnvironment(testingEnv).setCertPath("/GkashSDKCert/t1clientcert.pfx");

//Get Gkash sdk current instance
final GkashSoftPOSSDK gkashSoftPOSSDK = GkashSoftPOSSDK.getInstance();

//Request permission
gkashSoftPOSSDK.checkAndRequestPermission(MainActivity.this, 10001);

//Initialize Gkash sdk
gkashSoftPOSSDK.init(gkashSDKConfig, new GkashSoftPOSSDK.GkashStatusCallback() {
@Override
public void TransactionResult(TransactionDetails details) {
        //Handle transaction result
        Intent intent = new Intent(getApplicationContext(), ResultActivity.class);
        intent.putExtra("Status", details.getStatus());
        intent.putExtra("TransferAmount", details.getTransferAmount());
        intent.putExtra("ApplicationId", details.getApplicationId());
        intent.putExtra("Message", details.getMessage());
        intent.putExtra("AuthIDResponse", details.getAuthIDResponse());
        intent.putExtra("CardNo", details.getCardNo());
        intent.putExtra("TraceNo", details.getTraceNo());
        intent.putExtra("MID", details.getMID());
        intent.putExtra("RemID", details.getRemID());
        intent.putExtra("TID", details.getTID());
        intent.putExtra("TVR", details.getTVR());
        intent.putExtra("ResponseOrderNumber", details.getResponseOrderNumber());
        intent.putExtra("Method", details.getMethod());
        intent.putExtra("TransferDate", details.getTransferDate());
        intent.putExtra("TransferCurrency", details.getTransferCurrency());
        intent.putExtra("SettlementBatchNumber", details.getSettlementBatchNumber());
        intent.putExtra("CardType", details.getCardType());
        intent.putExtra("TxType", details.getTxType());
        intent.putExtra("CartId", details.getCartID());
        startActivity(intent);
        }

@Override
public void SocketStatus(GkashSoftPOSSDK.SocketConnectivityCallback connectivityCallback) {
    runOnUiThread(() -> {
        socketStatusTV.setText(connectivityCallback.name());

        if(connectivityCallback == GkashSoftPOSSDK.SocketConnectivityCallback.ONLINE){
            ipAddressTV.setText("IP address: " + gkashSoftPOSSDK.getIpAddress());
        }
    });
}

@Override
public void TransactionEvent(GkashSoftPOSSDK.TransactionEventCallback transactionEventCallback) {
    runOnUiThread(() -> transactionEventTV.setText(transactionEventCallback.name()));
}

@Override
public void QueryTransactionResult(TransactionDetails details) {
        //Handle transaction result
        Intent intent = new Intent(getApplicationContext(), ResultActivity.class);
        intent.putExtra("Status", details.getStatus());
        intent.putExtra("TransferAmount", details.getTransferAmount());
        intent.putExtra("ApplicationId", details.getApplicationId());
        intent.putExtra("Message", details.getMessage());
        intent.putExtra("AuthIDResponse", details.getAuthIDResponse());
        intent.putExtra("CardNo", details.getCardNo());
        intent.putExtra("TraceNo", details.getTraceNo());
        intent.putExtra("MID", details.getMID());
        intent.putExtra("RemID", details.getRemID());
        intent.putExtra("TID", details.getTID());
        intent.putExtra("TVR", details.getTVR());
        intent.putExtra("ResponseOrderNumber", details.getResponseOrderNumber());
        intent.putExtra("Method", details.getMethod());
        intent.putExtra("TransferDate", details.getTransferDate());
        intent.putExtra("TransferCurrency", details.getTransferCurrency());
        intent.putExtra("SettlementBatchNumber", details.getSettlementBatchNumber());
        intent.putExtra("CardType", details.getCardType());
        intent.putExtra("TxType", details.getTxType());
        intent.putExtra("CartId", details.getCartID());
        startActivity(intent);
}

//send payment request to softpos
requestPaymentBtn.setOnClickListener(view -> {
    Date c = Calendar.getInstance().getTime();
    SimpleDateFormat df = new SimpleDateFormat("yyyyMMddHHmmss", Locale.getDefault());
    String formattedDate = df.format(c);
    
    referenceNoET.setText(formattedDate);
    
    String paymentType = paymentTypeET.getText().toString();
    PaymentRequestDto requestDto = new PaymentRequestDto();
    requestDto.setAmount(amountET.getText().toString());
    requestDto.setPaymentType(GkashSoftPOSSDK.PaymentType.valueOf(paymentType));
    requestDto.setReferenceNo(formattedDate);
    requestDto.setPreAuth(false);
    requestDto.setEmail("YourEmail@email.com");
    requestDto.setMobileNo("0123456789");
    
    queryBtn.setEnabled(true);
    gkashSoftPOSSDK.requestPayment(requestDto);
});

queryBtn.setOnClickListener(new View.OnClickListener() {
@Override
public void onClick(View view) {
        String referenceNo = referenceNoET.getText().toString();
    
        if(!referenceNo.isEmpty()){
           gkashSoftPOSSDK.queryTransactionStatus(referenceNo);
        }
    }
});
```

## Getting permission callback

when requested for permission, implement TransStatusCallback to obtain the permission status.

```Java
//Request permission
gkashSoftPOSSDK.checkAndRequestPermission(MainActivity.this, 10001);

@Override
public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    if (requestCode == 10001) {
        if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
        // Permission is granted
        // Do the operation that requires this permission
        } else {
        // Permission is not granted
        // Display a message to the user explaining why the permission is needed
        }
    }
}
```

## License
[Apache 2.0](https://choosealicense.com/licenses/apache-2.0/)
