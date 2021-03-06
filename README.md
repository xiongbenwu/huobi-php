### It is recommended that you read the official document first

Huobi docs [https://huobiapi.github.io/docs/spot/v1/cn/#api](https://huobiapi.github.io/docs/spot/v1/cn/#api)

All interface methods are initialized the same as those provided by huobi. See details [src/api](https://github.com/zhouaini528/huobi-php/tree/master/src/Api)

Most of the interface is now complete, and the user can continue to extend it based on my design, working with me to improve it.

[中文文档](https://github.com/zhouaini528/huobi-php/blob/master/README_CN.md)

### Other exchanges API

[Exchanges](https://github.com/zhouaini528/exchanges-php) It includes all of the following exchanges and is highly recommended.

[Bitmex](https://github.com/zhouaini528/bitmex-php)

[Okex](https://github.com/zhouaini528/okex-php)

[Huobi](https://github.com/zhouaini528/huobi-php)

[Binance](https://github.com/zhouaini528/binance-php)

[Kucoin](https://github.com/zhouaini528/Kucoin-php)

[Mxc](https://github.com/zhouaini528/mxc-php)

[Coinbase](https://github.com/zhouaini528/coinbase-php)

[ZB](https://github.com/zhouaini528/zb-php)

[Bitfinex](https://github.com/zhouaini528/zb-php)

[Bittrex](https://github.com/zhouaini528/bittrex-php)

[Gate](https://github.com/zhouaini528/gate-php)

[Bigone](https://github.com/zhouaini528/bigone-php)   

[Crex24](https://github.com/zhouaini528/crex24-php)   

#### Installation
```
composer require linwj/huobi
```

Support for more request Settings [More](https://github.com/zhouaini528/huobi-php/blob/master/tests/spot/proxy.php#L21)
```php
$huobi=new HuobiSpot();

//You can set special needs
$huobi->setOptions([
    //Set the request timeout to 60 seconds by default
    'timeout'=>10,
    
    //If you are developing locally and need an agent, you can set this
    'proxy'=>true,
    //More flexible Settings
    /* 'proxy'=>[
     'http'  => 'http://127.0.0.1:12333',
     'https' => 'http://127.0.0.1:12333',
     'no'    =>  ['.cn']
     ], */
    //Close the certificate
    //'verify'=>false,
]);
```

### Spot Trading API

Market related API [More](https://github.com/zhouaini528/huobi-php/blob/master/tests/spot/market.php)

```php
$huobi=new HuobiSpot();

//Get market data. This endpoint provides the snapshots of market data and can be used without verifications.
try {
    $result=$huobi->market()->getDepth([
        'symbol'=>'btcusdt',
        //'type'=>'step3'   default step0
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//List trading pairs and get the trading limit, price, and more information of different trading pairs.
try {
    $result=$huobi->market()->getTickers();
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}
```

Order related API [More](https://github.com/zhouaini528/huobi-php/blob/master/tests/spot/order.php)

```php
$huobi=new HuobiSpot($key,$secret);

//Place an Order
try {
    $result=$huobi->order()->postPlace([
        'account-id'=>$account_id,
        'symbol'=>'btcusdt',
        'type'=>'buy-limit',
        'amount'=>'0.001',
        'price'=>'100',
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}
sleep(1);

//Get order details by order ID.
try {
    $result=$huobi->order()->get([
        'order-id'=>$result['data'],
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}
sleep(1);

//Cancelling an unfilled order.
try {
    $result=$huobi->order()->postSubmitCancel([
        'order-id'=>$result['data']['id'],
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//***********************Customize the order ID
//Place an Order
try {
    $client_order_id=rand(10000,99999).rand(10000,99999);
    $result=$huobi->order()->postPlace([
        'account-id'=>$account_id,
        'symbol'=>'btcusdt',
        'type'=>'buy-limit',
        'amount'=>'0.001',
        'price'=>'1000',
        'client-order-id'=>$client_order_id,
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}
sleep(1);

//Get order details by order ID.
try {
    $result=$huobi->order()->getClientOrder([
        'clientOrderId'=>$client_order_id,
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}
sleep(1);

//Cancelling an unfilled order.
try {
    $result=$huobi->order()->postSubmitCancelClientOrder([
        'client-order-id'=>$client_order_id,
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}
```

Accounts related API [More](https://github.com/zhouaini528/huobi-php/blob/master/tests/spot/account.php)

```php
$huobi=new HuobiSpot($key,$secret);

//get the status of an account
try {
    $result=$huobi->account()->get();
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//Get the balance of an account
try {
    $result=$huobi->account()->getBalance([
        'account-id'=>$result['data'][0]['id']
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

```

[More use cases](https://github.com/zhouaini528/huobi-php/tree/master/tests/spot)

[More API](https://github.com/zhouaini528/huobi-php/tree/master/src/Api/Spot)

### Futures Trading API

Contract related API [More](https://github.com/zhouaini528/huobi-php/blob/master/tests/future/contract.php)

```php
$huobi=new HuobiFuture($key,$secret);

//Place an Order
try {
    $result=$huobi->contract()->postOrder([
        'symbol'=>'BTC',//string    false   "BTC","ETH"...
        'contract_type'=>'quarter',//   string  false   Contract Type ("this_week": "next_week": "quarter":)
        'contract_code'=>'BTC190628',// string  false   BTC180914
        'price'=>'100',//   decimal true    Price
        'volume'=>'1',//    long    true    Numbers of orders (amount)
        'direction'=>'buy',//   string  true    Transaction direction
        'offset'=>'open',// string  true    "open", "close"
        //'client_order_id'=>'',//long  false   Clients fill and maintain themselves, and this time must be greater than last time
        //lever_rate    int true    Leverage rate [if“Open”is multiple orders in 10 rate, there will be not multiple orders in 20 rate
        //order_price_type   string true    "limit", "opponent"
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//Get Information of an Order
try {
    $result=$huobi->contract()->postOrderInfo([
        'order_id'=>'xxxx',//You can also 'xxxx,xxxx,xxxx' multiple ID
        //'client_order_id'=>'xxxx',
        'symbol'=>'BTC'
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//Cancel an Order
try {
    $result=$huobi->contract()->postCancel([
        'order_id'=>'xxxx',//You can also 'xxxx,xxxx,xxxx' multiple ID
        //'client_order_id'=>'xxxx',
        'symbol'=>'BTC'
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}



//User`s position Information
try {
    $result=$huobi->contract()->postPositionInfo();
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//User`s Account Information
try {
    $result=$huobi->contract()->postAccountInfo();
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//Get Contracts Information
try {
    $result=$huobi->contract()->getContractInfo();
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}
```

Market related API [More](https://github.com/zhouaini528/huobi-php/blob/master/tests/future/market.php)

```php
$huobi=new HuobiFuture();

//The Last Trade of a Contract
try {
    $result=$huobi->market()->getTrade([
        'symbol'=>'BTC_CQ'
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//Request a Batch of Trade Records of a Contract
try {
    $result=$huobi->market()->getHistoryTrade([
        'symbol'=>'BTC_CQ',
        //'size'=>100
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//Get Market Depth
try {
    $result=$huobi->market()->getDepth([
        'symbol'=>'BTC_CQ',
        'type'=>'step1'
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}
```

[More use cases](https://github.com/zhouaini528/huobi-php/tree/master/tests/future)

[More API](https://github.com/zhouaini528/huobi-php/tree/master/src/Api/Futures)

### Coin Margined Swap API 

```php
$huobi=new HuobiSwap($key,$secret);
//or new
//$huobi=new HuobiLinear($key,$secret);

//Place an Order
try {
    $result=$huobi->trade()->postOrder([
        'contract_code'=>'ETH-USD',//   string  false   BTC180914
        'price'=>'100',//   decimal true    Price
        'volume'=>'1',//    long    true    Numbers of orders (amount)
        'direction'=>'buy',//   string  true    Transaction direction
        'offset'=>'open',// string  true    "open", "close"
        'order_price_type'=>'limit',//"limit", "opponent"
        'lever_rate'=>20,//int  true    Leverage rate [if“Open”is multiple orders in 10 rate, there will be not multiple orders in 20 rate
        
        //'client_order_id'=>'',//long  false   Clients fill and maintain themselves, and this time must be greater than last time
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//Get Information of an Order
try {
    $result=$huobi->trade()->postOrderInfo([
        'order_id'=>$result['data']['order_id'],//You can also 'xxxx,xxxx,xxxx' multiple ID
        //'client_order_id'=>'xxxx',
        'contract_code'=>'ETH-USD'
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}

//Cancel an Order
try {
    $result=$huobi->trade()->postCancel([
        'order_id'=>$result['data'][0]['order_id'],//You can also 'xxxx,xxxx,xxxx' multiple ID
        //'client_order_id'=>'xxxx',
        'contract_code'=>'ETH-USD'
    ]);
    print_r($result);
}catch (\Exception $e){
    print_r($e->getMessage());
}
```

[More Test](https://github.com/zhouaini528/huobi-php/tree/master/tests/swap)

[More Api](https://github.com/zhouaini528/huobi-php/tree/master/src/Api/Swap)

