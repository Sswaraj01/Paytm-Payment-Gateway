# Paytm Payment Gateway Integration

This sirvice provide complete Integration of **Paytm Payment Gateways Check-out**, It also include **MySql** intregration to keep track of 
Transaction.

## These are list of required Technologies and Tools have been used for this Service.

1. STS(Spring Tool Suite) IDE. *You can chose any IDE*
2. JAVA 8 
3. Maven 
4. Checksum Grenerator provided by Paytm.[Click here to Get it!](https://github.com/Paytm-Payments/Paytm_Web_Sample_Kit_Java)
5. MerchantId. **You can get it by registering yourself as merchant at paytm.**[Sign Up/Sign-in](https://developer.paytm.com/docs/v1/payment-gateway)
6. MerchantKey. **You can get it by registering yourself as merchant at paytm.**[Sign Up/Sign-in](https://developer.paytm.com/docs/v1/payment-gateway)
7. CallBack Url. **Need to create so that Paytm will revert the response on this url.**

### Payment Master Table for tracking Transaction.

BANKNAME | BANKTXNID | CHECKSUMHASH | CURRENCY | GATEWAYNAME | MID | ORDERID | PAYMENTMODE | RESPCODE | RESPMSG | STATUS | TXNAMOUNT | TXNDATE | TXNID
---------|-----------|--------------|----------|-------------|-----|---------|-------------|----------|---------|--------|-----------|---------|------
AXIS BANK|BANK0101020|chakfgt1021@IsXXX|INR|WALLET|MER0101201|OD01020152|CC|TXN_SUCCESS|SUCCESS|STATUS_SUCCESS|101|2019-02-28|TXN2315225654
WALLETS|BANK0101020|chakfgt1021@IsXXX|INR|WALLET|MER0101201|OD01020152|CC|TXN_SUCCESS|SUCCESS|STATUS_SUCCESS|101|2019-02-28|TXN2315225654
XXXXXXXXX|XXXXXXXXXXX|XXXXXXXXXXXXXX|XXXXXXXXXX|XXXXXXXXXXXXX|XXXXX|XXXXXXXXX|XXXXXXXXXXXXX|XXXXXXXXXX|XXXXXXXXX|XXXXXXXX|XXXXXXXXXXX|XXXXXXXXX|XXXXXX

#### If you have done with followings requirement,You could start.Happy Coding!

- [x] Installed STS,JAVA,Maven
- [x] Accquired MerchantId and Merchant key
- [x] Downloaded jar for checksum generation from Paytm
- [x] Made a callBack url

## Implementation 

#### Step 1.
**At the click of payment button by customer on our website, create an order in our system and generate the required payload for payment request. Parameters of payload are provided below**

REQUEST ATTRIBUTES	|  	ATTRIBUTES DEFINATION	
--------------------|-------------------------
MID String(20) Mandatory | This is a unique identifier provided to every merchant by Paytm. MID is part of your account credentials and is different on staging and production environment. Your staging MID is available here and production MID will be available once your activation is complete		
ORDER_ID String(50) Mandatory | Unique reference ID for a transaction which is generated by merchant Special characters allowed in Order ID are: @, -, _, .		
CUST_ID String(64) Mandatory  |	Unique reference ID for every customer which is generated by merchant Special characters allowed in Cust_ID are @, ! ,_ ,$, .		
TXN_AMOUNT String(10) Mandatory	| Amount in INR payable by customer. Should contain digits up to two decimal points. Only special character allowed is (“.”)		
CHANNEL_ID String(3) Mandatory	| This parameter is used to control the theme of the payment page. Based on the channel passed, Paytm will render the layout suitable for that specific platform For websites, the value is WEB For Mobile websites/App, the value is WAP		
WEBSITE String(30) Mandatory	| For staging environment: For staging environment: WEBSTAGING For production environment: Will be available here once your activation is complete		
CHECKSUMHASH String(108) Mandatory	| Signature to avoid tampering. Generated using server side checksum utility available here		
MOBILE_NO String(15) Optional	| Customer's mobile number. Passing this enables faster login for customer into his/her Paytm account. When the customer clicks on login, the mobile number comes pre-filled on our payment page		
EMAIL String(50) Optional	| Customer's email ID 
INDUSTRY_TYPE_ID String(20) Mandatory	|For staging environment: "Retail" For production environment: Will be available here once your activation is complete		
CALLBACK_URL String(255) Mandatory	| On completion of transaction, Paytm payment gateway will send the response on this URL.. Sample URL is - https://merchant.com/callback/		
PAYMENT_MODE_ONLY String(3) Optional | If merchant wants to allow payment mode selection on his website or restrict the payment to a particular paymode, then value to be passed is “YES”. For example, with this parameter merchant can ensure that customer only sees credit card as a paymode when he lands on Paytm's cashier page.		
AUTH_MODE String(10) Conditional |	Required If PAYMENT_MODE_ONLY = YES, then For Credit/Debit card - 3D For Wallet, Net Banking – USRPWD		
PAYMENT_TYPE_ID String(15) Conditional	|Required If PAYMENT_MODE_ONLY = Yes, then Credit card payment mode – CC Debit card payment mode - DC Net banking payment mode - NB Paytm wallet – PPI EMI - EMI UPI - UPI		
BANK_CODE String(5) Conditional	|Required If PAYMENT_MODE_ONLY = Yes PAYMENT_TYPE_ID = NB List of Bank Codes provided in this PDF

#### Step 2.
**Generate checksumhash using Paytm library with parameters in key value pairs. Using the payload and checksumhash make an HTML form post and redirect customer to Paytm server. Code snippets and Github links for the checksum utility and HTML form post are provided below.**
  
- Endpoints:
  - Staging: https://securegw-stage.paytm.in/theia/processTransaction
  - Production: https://securegw.paytm.in/theia/processTransaction
  
**HTML CODE SAMPLE WHICH WILL BE GENERATED**

```javascript
<html>
    <head>
        <title>Merchant Check Out Page</title>
    </head>
    <body>
        <center><h1>Please do not refresh this page...</h1></center>
        <form method="post" action="https://securegw-stage.paytm.in/theia/processTransaction?ORDER_ID=order1" name="f1">
            <table border="1">
                <tbody>
                    <input type="hidden" name="MID" value="rxazcv89315285244163">
                    <input type="hidden" name="WEBSITE" value="WEBSTAGING">
                    <input type="hidden" name="ORDER_ID" value="order1">
                    <input type="hidden" name="CUST_ID" value="cust123">
                    <input type="hidden" name="MOBILE_NO" value="7777777777">
                    <input type="hidden" name="EMAIL" value="username@emailprovider.com">
                    <input type="hidden" name="INDUSTRY_TYPE_ID" value="Retail">
                    <input type="hidden" name="CHANNEL_ID" value="WEB">
                    <input type="hidden" name="TXN_AMOUNT" value="100.12">
                    <input type="hidden" name="CALLBACK_URL" value="https://Merchant_Response_URL>">
                    <input type="hidden" name="CHECKSUMHASH" value="ZWdMJOr1yGiFh1nns2U8sDC9VzgUDHVnQp">
                </tbody>
            </table>
        <script type="text/javascript">
            document.f1.submit();
        </script>
        </form>
    </body>
</html>
```

#### Step 3:
**Customer fills the payment details and is redirected to bank page for authorization. Once the transaction is authorized, Paytm receives the response from the bank and returns a status to your website via your callback URL. Response attributes description and sample HTML form post is provided below**

RESPONSE ATTRIBUTES	|  	ATTRIBUTES DEFINATION	
--------------------|-------------------------
MID String(20)	| This is a unique identifier provided to every merchant by Paytm	
TXNID String(64)	|This is a unique Paytm transaction ID that is issued by Paytm for each transaction	
ORDERID String(50)	|Unique reference ID for a transaction which is generated by merchant and sent in the request	
CUST_ID String(64)	|Unique reference ID for every customer which is generated by merchant and sent in request	
BANKTXNID String	|The transaction ID sent by the bank. In case of Paytm proprietary instruments too, there is unique reference number generated by Paytm's system. In case the transaction does not reach the bank, this will be NULL or empty string. Primary reason for this is user dropping out of the payment flow before the transaction reaches to bank to servers	
TXNAMOUNT String(10)	|Amount paid by customer in INR	
CURRENCY String(3)	|Currency in which the transaction has taken place. Currently only "INR" is the supported currency of transaction	
STATUS String(20)	|This contains the transaction status and has only three values: TXN_SUCCESS, TXN_FAILURE and PENDING	
RESPCODE String(10)	|Codes refer to a particular reason of payment failure/success. List in this PDF	
RESPMSG String(500)	|Description message is linked with each respcode. List in this PDF	
TXNDATE DateTime	|Date and Time of transaction in the format "yyyy-MM-dd HH:mm:ss.S"Example: "2015-11- 02 11:40:46.0"	
GATEWAYNAME String(15)	|Gateway used by Paytm to process the transactions. By paymodes, the details are provided below Credit, debit cards UPI - Gateway used to process the transaction. For example, if HDFC gateway has been used to process SBI credit card transactions, the value will be HDFC Net banking - Netbanking transactions are not routed via gateway. Hence issuing bank name is passed in this field Paytm Wallet - Wallet	
BANKNAME String(500)	|Name of issuing bank of the payment instrument used by customer. By paymodes, the details are provided below Credit, debit cards, net banking - Name of the issuing bank. Example in case customer uses SBI's credit card, the value will be "SBI" Paytm Wallet - Wallet Note that in case of UPI - This parameter will be not be present in the response	
PAYMENTMODE String(15)	|The payment mode used by customer for transaction Credit card – CC Debit card - DC Net banking - NB UPI - UPI Paytm wallet – PPI	
CHECKSUMHASH String(108)|Security parameter to avoid tampering. Verified using server side checksum utility provided by Paytm. Utilities to generate checksumhash is available here	
BIN_NUMBER String(6)	|Starting 6 digit of Credit Card/Debit Card which used in transaction.For other modes, this field not provided in response	
CARD_LAST_NUMS String(4) |	Last 4 digit of Credit Card/Debit Card which used in transaction.For other modes, this field not provided in response

## HTML CODE SAMPLE FOR RESPONSE

```
<html>
   <head>
     <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
     <title>Paytm Secure Online Payment Gateway</title>
   </head>
   <body>
      <table align='center'>
            <tr>
            <td><STRONG>Transaction is being processed,</STRONG></td>
            </tr>
            <tr>
            <td><font color='blue'>Please wait ...</font></td>
            </tr>
            <tr>
            <td>(Please do not press 'Refresh' or 'Back' button</td>
            </tr>
      </table>
      <FORM NAME='TESTFORM' ACTION='https://<Merchant_Response_URL>' METHOD='POST'>
            <input type='hidden' name='CURRENCY' value='INR'>
            <input type='hidden' name='CUST_ID' value='cust123'>
            <input type='hidden' name='GATEWAYNAME' value='WALLET'>
            <input type='hidden' name='RESPMSG' value='Txn Success'>
            <input type='hidden' name='BANKNAME' value='WALLET'>
            <input type='hidden' name='PAYMENTMODE' value='PPI'>
            <input type='hidden' name='MID' value='rxazcv89315285244163'>
            <input type='hidden' name='RESPCODE' value='01'>
            <input type='hidden' name='TXNID' value='20180821111212800110168085600021958'>
            <input type='hidden' name='TXNAMOUNT' value='100.12'>
            <input type='hidden' name='ORDERID' value='order1'>
            <input type='hidden' name='STATUS' value='TXN_SUCCESS'>
            <input type='hidden' name='BANKTXNID' value='5357590'>
            <input type='hidden' name='TXNDATE' value='2018-08-21 15:16:11.0'>
            <input type='hidden' name='CHECKSUMHASH'   value='YjtlLUVs6gQhR8RuUltwOsGnGXBg7gpdMRAKYU/ qiTZCeJZmwbciUFmwtT6RmwBmpwVswSiknJK7iEBch27q627uzTXKxJ0vzoMs68AE9A='>
      </FORM>
   </body>
 <script type="text/javascript">  document.forms[0].submit();</script>    
</html> 
```

#### Step 4:
**Checksumhash received in response of transaction needs to verified on merchant server using Paytm library with all the parameters in key value pairs. Code snippets and Github links for the checksum utility are provided below**

We need to verify the checksum to be assured that payment is not misleaded or Fraud.

```
public boolean validateCheckSum(TreeMap<String, String> parameters, String paytmChecksum,String merchantKey) throws Exception {
		return CheckSumServiceHelper.
				getCheckSumServiceHelper().
				verifycheckSum(merchantKey,parameters,paytmChecksum);
	}
```

#### Step 5 :
**Validate transaction response via server side request using *Transaction Status API*. This API requires checksumhash in request and its verification in response. The status should be treated as the final status of the transaction**

In case of network issue or powercut,It might the case that we don't get any response from paytm
in that case we need to call their Transaction Status API.

**Code Sample to get status.

```
public String verifyTxn(String orderID)  {
		String transactionURL = "https://securegw-stage.paytm.in/merchant-status/getTxnStatus";
		TreeMap<String, String> paytmParams = new TreeMap<String, String>();
		paytmParams.put("MID", paytmDetails.getMerchantId());
		paytmParams.put("ORDERID", orderID);
		String responseData = "";
		 DataOutputStream requestWriter=null;
		 BufferedReader responseReader=null;
		try {
		    String paytmChecksum = CheckSumServiceHelper.getCheckSumServiceHelper().genrateCheckSum(paytmDetails.getMerchantKey(), paytmParams);
		    paytmParams.put("CHECKSUMHASH", paytmChecksum);
		    JSONObject obj = new JSONObject(paytmParams);
		    String postData = "JsonData=" + obj.toString();
		    URL url = new URL(transactionURL);
		    Proxy proxy = new Proxy(Type.HTTP, new InetSocketAddress("proxy.cognizant.com", 6050));
		    HttpURLConnection connection = (HttpURLConnection) url.openConnection(proxy);
		    connection.setRequestMethod("POST");
		    connection.setRequestProperty("contentType", "application/json");
		    connection.setUseCaches(false);
		    connection.setDoOutput(true);
		    
		    requestWriter = new DataOutputStream(connection.getOutputStream());
		    requestWriter.writeBytes( postData);
		    InputStream is = connection.getInputStream();
		    responseReader = new BufferedReader(new InputStreamReader(is));
		    if((responseReader.readLine()) != null) {
		    	responseData=responseReader.readLine();
		    }
		} catch (Exception exception) {
		    exception.printStackTrace();
		}finally{
			try {
				requestWriter.close();
				responseReader.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		return responseData;
	}
  ```
  
  ### References
  - https://developer.paytm.com/docs/v1/payment-gateway
