# Authorize.net Configuration

!!! note

    You can select Authorize.net AIM (Advanced Integration Method) as the Payment Processor for your CiviCRM online contributions, membership sign-up and event registration as of version 1.7. This page describes the configuration requirements and steps.


## PHP Configuration Requirements

The following PHP extensions must be enabled in order for CiviCRM to interface with Authorize.Net's payment services:

* PHP Perl Compatible Regular Expressions extension for PHP 4.3.0+ and higher
* PHP cURL extension for PHP 4.3.0+ and higher with SSL support

Use phpInfo() to check for these extensions. The easiest way to do this is to create info.php in the doc root. (The doc root is the primary directory for your website.) The text for the info.php file is:

Go to [your_website_url]/info.php. The phpinfo function returns **a lot** of information. Use your browser's search function to look for:

![Screenshot of curl support indicated in phpinfo](../../img/phpinfo-curl.png)

and

![Screenshot of PCRE support indicated in phpinfo](../../img/phpinfo-pcre.png)

These represent the following lines of code:

```
PCRE (Perl Compatible Regular Expressions) Support =&gt; enabled
CURL support =&gt; enabled
```

The MHash extension is also recommended, although not required.

## Live vs Test

Authorize.Net has a test server on which you can sign up for a Developer Test Account (see [https://developer.authorize.net/testaccount/](https://developer.authorize.net/testaccount/)). A developer test account behaves like a regular merchant account but processes no live credit card transactions and, in fact, will accept certain "fake" credit cards that you can use for test transactions. A developer test account has its own URL and login credentials, separate from those of your merchant account.

Transactions submitted to the test environment using a developer test account are not submitted to financial institutions for authorization and are not permanently stored in the Merchant Interface, although they will appear in the Unsettled Transactions area for the test account immediately after you submit them (with one caveat: see following).

As an alternative, you can set your Merchant Account to run in **Test Mode**. In this case, all transactions are considered to be tests and no live credit card transactions will be processed. In this way, you can use the live server and login for testing.

And as a third way, you can use your Merchant Account in regular live mode but with the addition of adding a single field (x_test_request) in the information being sent to Authorize.Net, which indicates that the transaction is a test and no live credit card charge will be processed.

Note, however, that when x_test_request is set to TRUE, the transaction will _not_ be recorded in the merchant interface of Authorize.net (whether you are using a developer test account on their development server or your live merchant account on the live server).

CiviCRM's default behavior is to set x_test_request to TRUE for all "test-drive" transactions, regardless of which server you have specified in the Payment Processor settings.

Further details about the different test settings in Authorize.net may be found at [http://developer.authorize.net/guides/DPM/Test_transactions.html](http://developer.authorize.net/guides/DPM/Test_transactions.html).

## Advanced Integration Method vs Simple Integration Method

For Authorize.Net **Advanced Integration Method (AIM)**, card information is submitted to the server. The server then takes all submitted information, and submits it to Authorize.Net as an HTTP Post form. A reply is then received, which consists of a single CSV line. This reply is parsed to determine success or failure, and if failure, the reason for failure.

For **Simple Integration Method (SIM)**, the client submits the form to Authorize.Net. Authorize.Net can optionally be configured to contact the server with the results, and display a response page generated by the server. If this option is not used, though, there will be no way to track contributions, registrations, etc.

**At this time, CiviCRM only supports the Advanced Integration Method.**

## Configure API Access

**NOTE:** The following assumes that you already have an Authorize.Net merchant account. If you don't, you can go to [http://www.authorize.net](http://www.authorize.net) to sign up.

If you do not already have an API login and Transaction Key, generate these now from the Authorize.Net account page, under Settings, General Security Settings.

After this is done, create the Authorize.net Payment Processor configuration in CiviCRM::

* Log into CiviCRM as an administrator
* Select **Administer** ?? **System Settings** ?? **Payment Processors**
* Select **Add Payment Processor**
* For **Payment Processor Type** , choose **Authorize.Net**
* Enter the **API Login ID** and **Transaction Key** values (Live and/or Test as needed)
* The **MD5 Hash** is best left empty.

If you would like to use an Authorize.net Developer Test Account, you can sign up for one at [http://developer.authorize.net/](http://developer.authorize.net/). You can have and use both; in fact, that's highly recommended. Use your Merchant Account for live transactions (upper set of credentials in Payment Processor configuration), and use your Developer Test Account for test-drive transactions (second set of credentials in Payment Processor configuration).

!!! warning "Site URL for Test Mode"

    If you are using a Developer Test Account, then you must use the following test server in your Payment Processor Settings for **Site URL** :

    ```
    https://test.authorize.net/gateway/transact.dll
    ```

    Once your Merchant Account is Live - you may need to modify the CiviCRM Payment Processor settings to use the live server **Site URL** for both test-drive and live payments:

    ```
    https://secure.authorize.net/gateway/transact.dll
    ```


!!! danger

    Remember that credit card information is being transmitted to the server. **Unless the site is specifically being used for testing, and no live transactions will take place, it is highly recommended that [Force Secure URLs](https://docs.civicrm.org/sysadmin/en/latest/setup/security/#setting-up-ssl) be set**


### Automated Recurring Billing

Authorize.net offers a recurring payment system called Automated Recurring Billing (ARB) for an additional fee. Your **Settings - Payment Processor** page will automatically populate with the standard Recurring Payments URL, but you will need to enable the ARB service in order for recurring contributions to work.

CiviCRM will need to be notified of the success of recurring contributions via a **Silent Post URL**.

Follow the instructions here: [IPN Notify URL](recurring.md#IPN%20notify%20URL) to work out the correct URL to use.

Within Authorize.net, go to **Account > Settings > Silent Post URL** (within the Transaction Format Settings section). On that page, enter the appropriate IPN / Webhook URL.

If you fail to do this, one-time contributions will succeed normally, and recurring contributions will be processed successfully by Authorize.net, but the contribution status will be stuck at Pending.

Important: if you are using a different domain for sandbox/staging or a local installation, you can create test transactions from your local that will be handled by Auth.net (hopefully you're doing this under one of the various test configurations described above), but note that Auth.net will attempt to use the above URL (your production site) to notify CiviCRM, so your staging/local installation will never hear back from CiviCRM unless you change the URL in Auth.net (which you probably don't want to do if you have a live site running).

!!! note

    The Authorize.net Silent-Post is pretty simplistic, so you should confirm the URL you configure. For instance, you do not want to use a URL that will require a redirect for SSL (https) or that enforces the use of "www". See below on [Testing Recurring Transactions](#testing-of-recurring-transactions) to confirm you have the correct URL to use.

## Other Authorize.Net settings

* **Card Code Verification (CCV)** and **Address Verification Service** are free and part of your account. They do need to be configured to be used. If used, they will help prevent credit card fraud.
* **Email Receipt** : The emailCustomer AuthorizeNet parameter is hard-coded in CRM/Core/Payment/AuthorizeNet.php to TRUE, so anyone who pays will get an email notification.

## Accepted Credit Cards

* To see the list of credit cards that Authorize.Net will process, select **Merchant Profile** in the Authorize.Net **Account Settings** screen.
* In the **Administer CiviCRM** screen, select **Accepted Credit Cards**
* Disable any cards which are not accepted. If there are any cards accepted which are not listed, add them.

## Testing

The following card numbers can be used for testing:

* American Express: 370000000000002
* Discover: 6011000000000012
* MasterCard: 5424000000000015
* Visa: 4007000000027 (Yes, that's shorter than a normal credit card number. Nevertheless, it works with the test server.)
* Second Visa Test Card: 4012888818888

These numbers will always succeed in test mode as long as the expiration date is in the future. You can enter any value for the Security Code (CVV) when testing.

## Testing of recurring transactions

Testing recurring transactions is problematic because of the long delay between entering transactions and the time (usually about 3AM mountain standard time daily) that Authorize.net actually processes the transactions and calls back to civicrm with the results.

### Shell script testing method

Developers can use a shell script like the one below to call the authorizeIPN.php script to simulate a callback for a recurring transaction from authorize.net and test that recurring transactions are handled properly by civicrm.

Note that you must modify the URL to match your own web site and various other parameters to match an actual existing recurring transaction on your site:

```bash
#!/bin/bash

# Confirm the URL to use in the Authorize.net Silent-Post URL:
# last URL segment is the payment processor ID
THE_URL="https://awesome.org/civicrm/payment/ipn/1"

X_AMOUNT="5.00"
X_CUST_ID="3430"  # cvicrm contact id
X_INVOICE_NUM="6692"  # civicrm contribution id
X_SUBSCRIPTION_ID="40018319"
X_TRANS_ID="40926580513"     # x_trans_id is a unique number that authorize.net returns to identify this transaction

curl $THE_URL -d x_response_code=1 \
    -d x_subscription_id=$X_SUBSCRIPTION_ID \
    -d x_cust_id=$X_CUST_ID \
    -d x_amount=$X_AMOUNT \
    -d x_invoice_num=$X_INVOICE_NUM \
    -d x_trans_id=$X_TRANS_IDtest
```

_Note See alternate testing method below for examples of other parameters that can be included._

### Online form testing method

Another way to test the authorize.net IPN system for recurring transactions is to create a web page with a form as in the code below. Edit the input fields to match a recurring transaction that has been submitted and that is in your database. Then upload the form to a website and click the submit button to similate an authorize.net IPN response. Note that you'll need to change the x_trans_id each time you submit the form, and each time you submit the form CiviCRM will create a new contribution.

* x_invoice_num is the civicrm contribution id that exists in your database. For testing, create a recurring transaction and use that contribution id & contact id.
* x_cust_id must match a contact id in the CiviCRM database (the contact with the x_invoice_num / contribution id that you created for testing the IPN).
* x_amount should be the same dollar amount as entered in your text contribution (x_cust_id, x_invoice_num, and x_amount should all match an actual contribution you have created in CiviCRM).
* x_trans_id is a unique number that authorize.net returns to identify this transaction. It must not match any existing transaction id in your database???meaning that you must change this value every time you submit the form and you should be careful to use no x_trans_id that exist in your system or that could be returned by authorize.net in the future. Suggested: Use a transaction id with a different number of digits that authorize.net uses, or add 'test' to the beginning or end.
* Change the URL of the action to the actual location of the authorizeIPN.php file on your website.
* This will create real contribution transactions in your CiviCRM database, so you'll want to clean those up in CiviCRM when you're done. This form has no effect on actual authorize.net transactions, only the CiviCRM database.

One common problem with IPN responses is that your web host may accept connections internally but not from an external host. So the form may work when hosted on your own web site, but not when hosted externally (or called from authorize.net's servers). You can post the form on a site like [http://jsfiddle.net](http://jsfiddle.net) to test IPN calls from an outside host.

```html
<form action="http://example.org/sites/all/modules/civicrm/extern/authorizeIPN.php" method="post">
  <input type="hidden" name="x_response_code" value="1"/>
  <input type="hidden" name="x_response_subcode" value="1"/>
  <input type="hidden" name="x_response_reason_code" value="1"/>
  <input type="hidden" name="x_response_reason_text" value="This transaction has been approved."/>
  <input type="hidden" name="x_auth_code" value=""/>
  <input type="hidden" name="x_avs_code" value="P"/>
  <input type="hidden" name="x_trans_id" value="6456235754test"/>
  <input type="hidden" name="x_invoice_num" value="13514"/>
  <input type="hidden" name="x_description" value=""/>
  <input type="hidden" name="x_amount" value="1.00"/>
  <input type="hidden" name="x_method" value="CC"/>
  <input type="hidden" name="x_type" value="auth_capture"/>
  <input type="hidden" name="x_cust_id" value="102"/>
  <input type="hidden" name="x_first_name" value="John"/>
  <input type="hidden" name="x_last_name" value="Smith"/>
  <input type="hidden" name="x_company" value=""/>
  <input type="hidden" name="x_address" value=""/>
  <input type="hidden" name="x_city" value=""/>
  <input type="hidden" name="x_state" value=""/>
  <input type="hidden" name="x_zip" value=""/>
  <input type="hidden" name="x_country" value=""/>
  <input type="hidden" name="x_phone" value=""/>
  <input type="hidden" name="x_fax" value=""/>
  <input type="hidden" name="x_email" value=""/>
  <input type="hidden" name="x_ship_to_first_name" value=""/>
  <input type="hidden" name="x_ship_to_last_name" value=""/>
  <input type="hidden" name="x_ship_to_company" value=""/>
  <input type="hidden" name="x_ship_to_address" value=""/>
  <input type="hidden" name="x_ship_to_city" value=""/>
  <input type="hidden" name="x_ship_to_state" value=""/>
  <input type="hidden" name="x_ship_to_zip" value=""/>
  <input type="hidden" name="x_ship_to_country" value=""/>
  <input type="hidden" name="x_tax" value="0.0000"/>
  <input type="hidden" name="x_duty" value="0.0000"/>
  <input type="hidden" name="x_freight" value="0.0000"/>
  <input type="hidden" name="x_tax_exempt" value="FALSE"/>
  <input type="hidden" name="x_po_num" value=""/>
  <input type="hidden" name="x_MD5_Hash" value="A375D35004547A91EE3B7AFA40B1E727"/>
  <input type="hidden" name="x_cavv_response" value=""/>
  <input type="hidden" name="x_test_request" value="false"/>
  <input type="hidden" name="x_subscription_id" value="21930455"/>
  <input type="hidden" name="x_subscription_paynum" value="1"/>
  <input type="submit"/>
</form>
```

## Additional Documentation

For more information on the Authorize.Net Advanced Integration Method, see the AIM guide: [http://www.authorize.net/support/AIM_guide.pdf](http://www.authorize.net/support/AIM_guide.pdf)


## eProcessing Network Configuration (via Authorize.net Emulation)

!!! note

    To interface with **eProcessing Network** via Authorize.net Emulation, you can select Authorize.net AIM (Advanced Integration Method) as the Payment Processor for your CiviCRM online contributions, membership sign-up and event registration. This page describes the configuration requirements and steps.


### PHP Configuration Requirements

The following PHP extensions must be enabled in order for CiviCRM to interface with the eProcessing Network's payment services:

* PHP Perl Compatible Regular Expressions extension for PHP 4.3.0+ and higher
* PHP cURL extension for PHP 4.3.0+ and higher with SSL support

Use phpInfo() to check for these extensions. You should see the following lines:

```
PCRE (Perl Compatible Regular Expressions) Support =&gt; enabled
CURL support =&gt; enabled
```

The MHash extension is also recommended, although not required.

### Advanced Integration Method vs Simple Integration Method

For eProcessing.com (Authorize.Net Emulation) **Advanced Integration Method (AIM)**, card information is submitted to the server. The server then takes all submitted information, and submits it to Authorize.Net as an HTTP Post form. A reply is then received, which consists of a single CSV line. This reply is parsed to determine success or failure, and if failure, the reason for failure.

**At this time, CiviCRM only supports the Advanced Integration Method.**

### Configure API Access

**NOTE:** The following assumes that you already have an eProcessing Network merchant account. If you don't, you can go to [http://www.eprocessingnetwork.com/](http://www.eprocessingnetwork.com/) to sign up.

If you do not already have an **RestrctKey** , generate this now from the eProcessing Network account page.

After this is done, create the eProcessing Network Payment Processor configuration in CiviCRM::

* Log into CiviCRM as an administrator
* Select **Administer CiviCRM** ?? **Global Settings** ?? **Payment Processors**
* For **Payment Processor Type** , choose **Authorize.Net - AIM**
* Enter the **Account ID** in the **API Login** field, enter the **RestrctKey** in both the **Payment Key** and **MD5 Hash** fields.
* Enter [https://www.eProcessingNetwork.Com/cgi-bin/an/order.pl](https://www.eProcessingNetwork.Com/cgi-bin/an/order.pl) in the **Site URL** field.

Remember that credit card information is being transmitted to the server. **Unless the site is specifically being used for testing, and no live transactions will take place, it is highly recommended that Force Secure URLs be set**

### Testing

The following card numbers can be used for testing:

* American Express: 370000000000002
* Discover: 6011000000000012
* MasterCard: 5424000000000015
* Visa: 4007000000000027 (NOTE: This test card number doesn't validate on CiviCRM's contribution page)

These numbers will always succeed in test mode as long as the expiration date is in the future. You can enter any value for the Security Code (CSC) when testing.

### Additional Documentation

For more information on the eProcessing Network's Integration and Devloper utilities, see: [http://www.eprocessingnetwork.com/Utilities.html](http://www.eprocessingnetwork.com/Utilities.html)

For more information on the Authorize.Net Advanced Integration Method, see the AIM guide: [http://www.authorize.net/support/AIM_guide.pdf](http://www.authorize.net/support/AIM_guide.pdf)
