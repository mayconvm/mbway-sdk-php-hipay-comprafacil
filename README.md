# HiPay MB WAY SDK PHP

### Create a transaction

    use HipayMbway\MbwayClient;
    use HipayMbway\MbwayRequestTransaction;
    use HipayMbway\MbwayRequestTransactionResponse;
    
    /*
     * Account config
     * $sandbox false if account is a production account
     * $entity, $username, $password and $category are provided by customer management 
     */
    
    $sandbox = true;
    $entity = 11249;
    $username = "hpXXXXXX";
    $password = "xxxxxxxx";
    $category = 3;
    
    /*
     * Transaction parameters
     */
    $notificationUrl = 'http://mynotificationurl.com';
    $amount = 12;
    $customerPhone = "9xxxxxxxx";
    $customerEmail = "xxxxxxx@xxxxx.com";
    $merchantId = "xxxxxx";
    $orderDescription = "order description";
    $customerVATNumber = "000000000";
    $customerName = "John Doe";
    
    $mbway = new MbwayClient($sandbox);
    $mbwayRequestTransaction = new MbwayRequestTransaction($username, $password, $amount, $customerPhone, $customerEmail, $merchantId, $category, $notificationUrl, $entity);
    $mbwayRequestTransaction->set_description($orderDescription);
    $mbwayRequestTransaction->set_clientVATNumber($customerVATNumber);
    $mbwayRequestTransaction->set_clientName($customerName);
    
    $mbwayRequestTransactionResult = new MbwayRequestTransactionResponse($mbway->createPayment($mbwayRequestTransaction)->CreatePaymentResult);
    if ($mbwayRequestTransactionResult->get_Success() && $mbwayRequestTransactionResult->get_ErrorCode() == "0") {
        switch ($mbwayRequestTransactionResult->get_MBWayPaymentOperationResult()->get_StatusCode()) {
            case "vp1":
                $transactionId = $mbwayRequestTransactionResult->get_MBWayPaymentOperationResult()->get_OperationId();
                print "SUCCESS - TRANSACTION CREATED WITH ID: $transactionId" . PHP_EOL;
                break;
            case "vp2":
                print "Operation refused. Please try again or choose another payment method." . PHP_EOL;
                break;
            case "vp3":
                print "Operation refused. Limit exceeded. Please try again or choose another payment method." . PHP_EOL;
                break;
            case "er1":
                print "Operation refused. Invalid phone number. Please try again with another phone number or choose another payment method." . PHP_EOL;
                break;
            case "er2":
                print "Operation refused. Unassigned phone number. Please try again with another phone number or choose another payment method." . PHP_EOL;
                break;
            default:
                print "Operation refused. Please try again or choose another payment method." . PHP_EOL;
    }
    } else {
        print $mbwayRequestTransactionResult->get_ErrorDescription() . PHP_EOL;
    }  
        

