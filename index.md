# Contract

### Encryption

**᛫** API KEY and API SECRET are required to access the API

**᛫** API KEY and API SECRET are case-sensitive

᛫ Interface parameter `signature` encrypts all request parameters with `HMAC-sha256`

**᛫ Request time-limiting** The signature timestamp is valid for 20 seconds, and it is invalid when it is outdated

### Sample request

**GET** /xstc-third/ex-order/create

Request Parameters: Request data type `application/x-www-form-urlencoded`

**Headers:**

| Name      | Type   | Parameter description        |
|:----------|:-------|:------------|
| APIKEY    | string | your apiKey |
| signature | string | Signature string       |

**Params:**

| Name      | Type   | Parameter description        |
|:--------------|:--------|:---------|
| clientOrderId | string  | Client order ID  |
| isBuy         | boolean | Whether to buy or not      |
| isMarket      | boolean | Whether it is a market order   |
| pairName      | string  | Pair name    |
| price         | string  | The price, the market price is not transmitted |
| quantity      | string  | Number of orders placed     |
| timestamp     | long    | Request time     |

**paramsString**：`clientOrderId=1&isBuy=1&isMarket=1&pairName=BTC/USDT&price=1999.99&quantity=1&timestamp=1703818410000`  
**API_SECRET**：your apiSecret  
**signature = HMAC-sha256(paramsString,API_SECRET)**

### Websocket Subscription

**᛫** Subscription required API KEY and API SECRET
**᛫** Subscription method is **SUBSCRIBE**，Cancel Subscription is **UNSUBSCRIBE**

**Contract order subscriptions**  
Request parameter type: json

```json
{
  "method": "SUBSCRIBE",
  "event": "api_contract_depth",
  "apiKey": "your apiKey",
  "pair": "BTC/USDT",
  "period": "1SECOND",
  "timestamp": 1703818987497,
  "signature": "HMacUtil.sha256(String.valueOf(timestamp), apiSecret)"
}
```

Response parameters

```json
{
  "event": "api_contract_depth",
  "pair": "",
  "data": {
    "asks": [
      {
        "price": decimal,
        "quantity": decimal
      }
    ],
    "bids": [
      {
        "price": decimal,
        "quantity": decimal
      }
    ]
  }
}
```

**Contract Notification: Order change subscription**  
The type of request parameter：json

```json

{
  "method": "SUBSCRIBE",
  "event": "api_contract_entrust",
  "apiKey": "your apiKey",
  "pair": "BTC/USDT",
  "timestamp": timestamp,
  "signature": "HMacUtil.sha256(String.valueOf(timestamp), appSecret)"
}
```

Response parameters

```json
{
  "event": "api_contract_entrust",
  "data": {
    "changeType": "NEW",
    //Type NEW/CANCELED/TRADE
    "quantity": decimal,
    "dealQuantity": decimal,
    "contractPairId": long,
    "clientOrderId": "",
    "direction": "LONG",
    //LONG , SHORT
    "currentEntrustId": long
  }
}
```

### Contract API

**Get trading pairs**  
`GET /xstc-third/info/pairs`   
Request parameters: None
Response Parameters:

```json
{
  "code": 0,
  "data": [
    {
      "deliveryMarketPrice": decimal,
      //Mark price for delivery	
      "deliveryStatus": integer,
      //Delivery status: 1 Not Expired, 2 Expired, 3 Delivered, 4 Delivery Completed；	
      "deliveryTime": string(date-time),
      //Delivery time
      "faceMultiplier": decimal,
      //Face Value Multiplier: The exchange rate of the dual currency exchange currency, the currency exchange price/transaction currency price, which cannot be modified
      "id": long,
      //Increment the ID of the primary key
      "isDelivery": boolean,
      "isDual": boolean,
      "makerTradeFeeRate": decimal,
      "maxCount": decimal,
      "minCount": decimal
      "pair": string,
      "settleCoinId": long,
      "settleCoinName": string,
      "settleDecimal": integer(int32),
      "swapCoinId": long,
      "swapCoinName": string,
      "symbol": string,
      "takerTradeFeeRate": decimal,
      "tradeCoinId": long,
      "tradeCoinName": string,
      "tradeDecimal": integer(int32)
    }
  ],
  "message": "",
  "success": true
}
```

**Place an order**
`GET /xstc-third/order/create `  
The type of data requested `application/x-www-form-urlencoded`  
Request Header: Same as example
Request parameters:

| Name      | Type   | Parameter description                                                                       |
|:--------------|:---------|:--------------------------------------------------------------------------------------------|
| clientOrderId | string	  | Client order ID                                                                             |
| isLong        | boolean  | 	Whether or not not long		                                                                  |
| isMarket      | boolean  | 	Whether it is a market order		                                                             |
| lever         | integer  | 	Level                                                                                      |
| matchType     | integer	 | 1 GTC is valid until cancelled; 2 IOC Immediate Transaction or Cancellation 3 FOK is fully filled or cancelled; 4 POST_ONLY only make maker orders;                             |
| pairName      | string	  | Pair name                                                                                       |
| positionType  | integer  | 	Position type: 1. Isolated and independent position (each order holds an independent position - the current version is invalid); 2. Isolated position, two-way position (the current trading pair is divided into long/short positions - the current version is invalid); 3. Isolated Warehouse Single-Position (Current Trading Pair's Single Position); 4. Full Warehouse Single-Position (Current Trading Pair's Single Position);	 |	
| price         | string	  | The price, the market price is not transmitted                                                                                   |	
| quantity      | string	  | Number of orders placed		                                                                                      |
| timestamp     | string   | 	Timestamp (millisecond level)                                                                             |

Response parameters：`json`
```json
{
	"code": 0,
	"data": {
		"corderId": "", 
		"orderId": 0, 
		"pairName": "" 
	},
	"message": "",
	"success": true
}

```

**Cancel the order**  
`POST /xstc-third/order/cancelEntrust`  
The type of data requested `application/x-www-form-urlencoded`    
Request Header: Same as example
Request parameters:

| Name       | Parameter   | description                                                                            |
|:-----------|:--------|:-----|
| entrustId	 | integer |Order ID|
| timestamp  | string	 |Timestamp (millisecond level)|

Response parameters：`json`
```json
{
  "code": 0,
  "data": true,
  "message": "",
  "success": true
}
```