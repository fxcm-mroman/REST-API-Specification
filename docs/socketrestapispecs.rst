FXCM Socket REST API User Guide
===============================

.. tabularcolumns:: |p{1cm}|p{2cm}|p{3cm}|
	
.. csv-table:: Revision History
   :file: _files/revhist.csv
   :header-rows: 1
   :class: longtable
   :widths: 1 1 1
   :align: center

 	 
Contents 

1. Overview
	
2. Getting Started	

	2.1. Prerequisites	
		
	2.2. Logon and Authentication
			
	2.3. Message Flow	
		
3. Authentication Messages	

	3.1. Open Socket and get socket_id by passing access_token	
		
4. Market Data	

	4.1. Request a list of all available symbols	
		
	4.2. Subscribe to Market Data stream	
		
	4.3. Unsubscribe from Market Data stream	
		
	4.4. Price updates	
		
5. Trading Tables		

	5.1. Subscribe to trading tables	
		
	5.2. Unsubscribe from trading tables
			
	5.3. Request a snapshot of trading tables
			
	5.4. Trading table responses	
	
	
		5.4.1. Offers table	
			
		5.4.2. Open Positions table	
			
		5.4.3. Closed Positions table	
			
		5.4.4. Orders table	
			
		5.4.5. Summary table
				
		5.4.6. Accounts table
				
6. Trading Orders

	
	6.1. /trading/open_trade	
		
	6.2. /trading/close_trade	
		
	6.3. /trading/change_order	
		
	6.4. /trading/delete_order	
		
	6.5. /trading/create_entry_order
			
	6.6. /trading/simple_oco
			
	6.7. /trading/add_to_oco
		
	6.8. /trading/remove_from_oco
			
	6.9. /trading/edit_oco	
		
	6.10. /trading/change_trade_stop_limit	
		
	6.11. /trading/change_order_stop_limit	
		
	6.12. /trading/close_all_for_symbol	
		
7. Historical Data	

  
1 Overview 
----------

FXCM offers a web-based REST API which can be used to establish secure connectivity with FXCM’s trading systems for the purpose of receiving market data and trading.   
  
This document provides an overview of this API, optional tools and sample implementations.  

2 Getting Started 
-----------------
2.1 Prerequisites
^^^^^^^^^^^^^^^^^ 
To use the REST API, you will need: 

* Access Token generated with Trading Station Web https://tradingstation.fxcm.com/  

* Socket.IO client library:  https://socket.io/docs/client-api/ 

	* using JavaScript: https://www.npmjs.com/package/socket.io 
	
	* using Python:  https://pypi.python.org/pypi/socketIO-client 
	
2.2 Logon and Authentication 
----------------------------
REST API uses a persistent Access Token. You can generate this token by logging into Trading Station Web at https://tradingstation.fxcm.com/. 

2.3 Message Flow 
----------------
 
Clients should establish a persistent WebSocket connection using socket.io library. All non-solicited updates will be sent over this connection. Client requests are to be sent via normal HTTP messages. Every HTTP message must contain following parameters: 
REQUEST 
 
.. tabularcolumns:: |p{1cm}|p{2cm}|p{3cm}|p{4cm}|
	
.. csv-table:: Message Flow
   :file: _files/messageflow.csv
   :header-rows: 1
   :class: longtable
   :widths: 1 1 1 1
   :align: center 
  
3 Authentication Messages 
-------------------------
3.1 Open Socket and get socket_id by passing access_token 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Socket connection can be established automatically using socket.io-client library. The connection needs to have access_token obtained from Trading Station Web. 
REQUEST 
 
Header 	Description 	Values 	Req’d 
Method 	HTTP method 	GET 	Y 
URI 	Resource 	/socket.io 	Y 
Parameter 	Description 	Values 	Req’d 
access_token 	access_token 	String, 40 digit hex value obtained in Error! Reference 	Y ource not found. 
Sample Request 
GET /socket.io/?access_token=cj5wedhq3007v61fe935ihqed&EIO=3&transport=polling&t=Lsd_lZY&b64=1 
HTTP/1.1 
User-Agent: node-XMLHttpRequest 
Accept: */* 
Host: api.fxcm.com 
Connection: close 
 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
Sid 	Socket ID 	40 digit hex value to be used as socket_id in all future requests 
upgrades 	Type of socket 	String. Internal variable of socket.io 
pingInterval 	Interval between pings 	Integer value in milliseconds. Internal variable of socket.io 
pingTimeout 	Timeout of pings 	Integer value in milliseconds. Internal variable of socket.io 
Sample Response 
HTTP/1.1 200 OK 
Content-Type: text/plain; charset=UTF-8 
Content-Length: 100 
Access-Control-Allow-Origin: * 
Set-Cookie: io=HHGqC3Gao2ENa5tNAAEu 
Date: Thu, 03 Aug 2017 12:08:11 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure 
Last-Modified: Thu, 03 Aug 2017 12:08:11 UTC 
 
97:0{"sid":"HHGqC3Gao2ENa5tNAAEu","upgrades":["websocket"],"pingInterval":25000,"pingTimeout":60 000} 
4 Market Data 
4.1 Request a list of all available symbols 
We need to know which symbols are available for subscription. 
REQUEST 
 

Header 	Description 	Values 
Req’d 
Method 	HTTP method 	GET 
Y 
URI 	Resource 	/trading/get_instruments 
Y 
Parameter 	Description 	Values 
Req’d 
Sample Request 
GET /trading/get_instruments/? HTTP/1.1 
User-Agent: request 
Accept: application/json 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api-demo.fxcm.com 
Connection: close 

 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
data 	Data of the response 	Object 
instrument 	List of instruments 	Array 
symbol 	Representation of the instrument 	Symbol 
visivle 	Is symbol visible in Offers table 	Boolean 
order 	Ordering number 	Number 
Sample Response 
HTTP/1.1 200 OK 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 2869 
ETag: W/"b35-wJL+EwQsbuYSjtvqcCGh1g" 
Date: Tue, 16 Jan 2018 17:27:44 GMT 
Connection: close 
Last-Modified: Tue, 16 Jan 2018 17:27:44 UTC 
Vary: Accept-Encoding  
{"response":{"executed":true},"data":{"instrument":[{"symbol":"EUR/USD","visible":true,"order":1
},… 
4.2 Subscribe to Market Data stream 
After subscribing, market price updates will be pushed to the client via the socket. 
REQUEST 
 


Header 
Description 
Values 
Req’d 
Method 
HTTP method 
POST 
Y 
URI 
Resource 
/subscribe 
Y 
Parameter 
Description 
Values 
Req’d 
pairs 
Symbols to be subscribed to 
String, representation of the requested symbol 
Y 
Sample Request 
POST /subscribe HTTP/1.1 User-Agent: request 
Accept: application/json 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 55 
pairs=EUR%2FUSD 
0 
 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
error 	Type of socket 	String. Internal variable of socket.io 
pairs 	Symbol information 	Object containing information about the symbol 
Updated 	Timestamp of last update 	Number, Epoch timestamp in milliseconds 
Rates 	Current rates 	Array of Numbers, [Bid, Ask, Session High, Session Low] 
Symbol 	Symbol 	String representing the symbol 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 157 
ETag: W/"9d-YFo+3OmAbWV4Q8hgjKPcMA" 
Date: Fri, 28 Jul 2017 22:48:36 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Fri, 28 Jul 2017 22:48:36 UTC 
 
{"response":{"executed":true,"error":""},"pairs":"{\"Updated\":1501275479,\"Rates\":[1.17481,1.1 7513,1.17653,1.167,1.17481,1.17513],\"Symbol\":\"EUR/USD\"}"} 
4.3 Unsubscribe from Market Data stream 
REQUEST 
 


Header 
Description 
Values 
Req’d 
Method 
HTTP method 
POST 
Y 
URI 
Resource 
/unsubscribe 
Y 
Parameter 
Description 
Values 
Req’d 
pairs 
Symbols to be unsubscribed from 
String representing the symbol 
Y 
Sample Request 
POST /unsubscribe HTTP/1.1 
User-Agent: request 
Accept: application/json 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 55 
pairs=EUR%2FUSD 
0 
 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
Sid 	Socket ID 	40 digit hex value to be used as socket_id in all future requests 
upgrades 	Type of socket 	String. Internal variable of socket.io 
pingInterval 	Interval between pings 	Integer value in milliseconds. Internal variable of socket.io 
pingTimeout 	Timeout of pings 	Integer value in milliseconds. Internal variable of socket.io 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 59 
ETag: W/"3b-3vU5f74yKnWdV/dyTgPejA" 
Date: Fri, 28 Jul 2017 22:48:40 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure 
Last-Modified: Fri, 28 Jul 2017 22:48:40 UTC 
 
{"response":{"executed":true,"error":""},"pairs":"EUR/USD"} 
4.4 Price updates 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
Updated 	Time of the update 	Integer epoch time in seconds 
Rates 	Price information 	[Bid, Ask, Session High, Session Low] 
Symbol 	Instrument of the update 	String representation of the instrument 
Sample Response 
["EUR/USD","{\"Updated\":1503314642,\"Rates\":[1.17614,1.17637,1.1771,1.17298],\"Symbol\":\"EUR/
USD\"}"] 
5 Trading Tables 
5.1 Subscribe to trading tables 
Subscribes to the updates of the data models. Update will be pushed to client via the socket. 
REQUEST 	 
Header 
Description 
Values 
Req’d 
Method 
HTTP method 
POST 
Y 
URI 
Resource 
/trading/subscribe 
Y 
Parameter 
Description 
Values 
Req’d 
models 
Name of the table model to be subscribed to 
String, one or more of: 'OpenPosition', 
'ClosedPosition', 'Order',  'Account',  
'Summary' 
Y 
Sample Request 
POST /trading/subscribe HTTP/1.1 
User-Agent: request 
Accept: application/json 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 52 
models=Order 
0 

 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 30 
ETag: W/"1e-/mvovEuhtN1hYjWJCVVEGQ" 
Date: Fri, 28 Jul 2017 23:19:59 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Fri, 28 Jul 2017 23:19:59 UTC 
 
{"response":{"executed":true}} 
5.2 Unsubscribe from trading tables 
Unsubscribes from the updates of the data models that are being pushed via the socket. 
REQUEST 
 


Header 
Description 
Values 
Req’d 
Method 
HTTP method 
POST 
Y 
URI 
Resource 
/trading/unsubscribe 
Y 
Parameter 
Description 
Values 
Req’d 
models 
Name of the table model to be unsubscribed to 
String, one or more of: 'Offer', 
'OpenPosition', 'ClosedPosition', 'Order',  
'Account',  'Summary', 'LeverageProfile', 'Properties' 
Y 
Sample Request 
POST /trading/unsubscribe HTTP/1.1 
User-Agent: request 
Accept: application/json 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 52 
models=Order 
0 
 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 30 
ETag: W/"1e-/mvovEuhtN1hYjWJCVVEGQ" 
Date: Fri, 28 Jul 2017 23:20:05 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Fri, 28 Jul 2017 23:20:05 UTC 
 
{"response":{"executed":true}} 
5.3 Request a snapshot of trading tables 
In case continuous updates of the trading tables is not needed, it is possible to request a one-time snapshot. 
Gets current content snapshot of the specified data models. 
Model choices: 'Offer', 'OpenPosition', 'ClosedPosition', 'Order', 'Summary', 'LeverageProfile', 'Account', 'Properties'. 
REQUEST 
 


Header 
Description 
Values 
Req’d 
Method 
HTTP method 
GET 
Y 
URI 
Resource 
/trading/get_model 
Y 
Parameter 
Description 
Values 
Req’d 
models 
Name of the table model to be unsubscribed to 
String, one or more of: 'Offer', 
'OpenPosition', 'ClosedPosition', 'Order',  
'Account',  'Summary', 'LeverageProfile', 'Properties' 
Y 
Sample Request 
GET 
/trading/get_model/?models=Offer&models=OpenPosition&models=ClosedPosition&models=Order&models=S
ummary&models=Account&models=LeverageProfile&models=Properties HTTP/1.1 
Host: api.fxcm.com Connection: close 
 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
<models> 	Requested tables 	Table models. See chapter 5.4 
Sample Response 
HTTP/1.1 200 OK 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 12264 
ETag: W/"2fe8-79HiV0VI0lZqcM4i27YT1w" 
Date: Mon, 14 Aug 2017 15:01:06 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Mon, 14 Aug 2017 15:01:06 UTC 
Vary: Accept-Encoding 
 
{"response":{"executed":true},(…) 
5.4 Trading table responses 
5.4.1 Offers table 
This section describes the Offers table that contains information about trading instruments, current prices, and high/low trading day prices. 
RESPONSE 
 
 
Parameter 
Description 
Values 
t 
ID number of the table 
0 
ratePrecision 
The price precision of the instrument. It defines number of digits after the decimal point in the instrument price quote. 
Number 
offerId 
The unique identification number of the instrument. 
Number 
rollB 
The interest amount added to the account balance for holding a one lot long (buy) position overnight. In the case of FX instruments, lot size is determined by the system base unit size. In the case of CFD instruments, lot size equals to one contract. The interest amount is expressed in the account currency and can be positive or negative. 
Number 
rollS 
The interest amount added to the account balance for holding a one lot short (sell) position overnight. In the case of FX instruments, lot size is determined by the system base unit size. In the case of CFD instruments, lot size equals to one contract. The interest amount is expressed in the account currency and can be positive or negative. 
Number 
fractionDigits 
The price precision of the instrument. It defines number of digits after the decimal point in the instrument price quote. 
Number 

	pip 	The size of one pip. It used to define the smallest move the instrument can 	Number 
make. In the case of FX instruments, it is expressed in the instrument counter currency. In the case of CFD instruments, it is expressed in the instrument native currency. 
	defaultSortOrder 	Sorting index of the instrument 	Number 
	currency 	The symbol of the instrument. 	String 
	instrumentType 	The type of the instrument. The possible values are: 	Number 
1	– Forex 
2	– Indices 
3	– Commodity 
4	– Treasury 
5	– Bullion 
6	– Shares 
7	– FXIndex 
	valueDate 	The simulated delivery date. The date and time when the position opened 	String 
in the instrument could be automatically closed. The value of this field is provided in the yyyyMMdd format. It is applicable only when instrument trades on account with the day netting trading. Otherwise, the value of this field is blank. 
	time 	The date and time of the last update of the instrument. ISO 8601 format. 	String 
	sell 	The current market price the instrument can be sold at. 	Number 
	buy 	The current market price the instrument can be bought at. 	Number 
sellTradable 	The usage of the sell price. It defines whether the sell price of the 	Boolean instrument is available for trading or not. 
buyTradable 	The usage of the buy price. It defines whether the buy price of the 	Boolean instrument is available for trading or not. 
	high 	The highest buy price of the instrument for the current trading day. 	Number 
	low 	The lowest sell price of the instrument for the current trading day. 	Number 
volume 	The tick volume of the current minute. The value of this field represents 	Number the number of ticks happened during the current minute. 
	pipFraction 	Minimum price change for the instrument. 	Number 
	spread 	Difference between Buy and Sell price in pips. 	Number 
	mmr 	Maintenance margin level. 	Number 
	emr 	Entry margin level. 	Number 
	lmr 	Limitation margin level. 	Number 
pipCost 	The cost of one pip per lot. It is expressed in the account currency and 	Number used to calculate the P/L value in the account currency. 
	action 	Type of update. Only applicable to updates through the socket. 	String 
Possible values are: 
I – Insert 
U – Update 
D – Delete 
Sample Response 
"offers":[{"t":0,"ratePrecision":5,"offerId":1,"rollB":-
2.208,"rollS":1.053,"fractionDigits":5,"pip":0.0001,"defaultSortOrder":100,"currency":"EUR/USD", "instrumentType":1,"valueDate":"09152017","time":"2017-09-
13T15:26:49.000Z","sell":1.18983,"buy":1.19008,"sellTradable":true,"buyTradable":true,"high":1.1 9962,"low":1.18977,"volume":1,"pipFraction":0.1,"spread":2.5,"mmr":0.013,"emr":0,"lmr":0,"pipCos t":0.0001}] 
5.4.1.1 Changing symbols subscribed to in Offers table 
Offers table will show only symbols that we have subscribed to using update_subscriptions. For a list of symbols available for subscription please see 4.1 
REQUEST 
 

Header 	Description 	Values 
Req’d 
Method 	HTTP method 	POST 
Y 
URI 	Resource 	/trading/update_subscriptions 
Y 
Parameter 	Description 	Values 
Req’d 
symbol 	Requested symbol 	String 
Y 
visible 	Should the symbol be visible in Offers table  	Boolean 
 
Sample Request 
POST /trading/update_subscriptions HTTP/1.1 
User-Agent: request 
Accept: application/json 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api-demo.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 1d 
symbol=EUR%2FUSD&visible=true 0 

 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 30 
ETag: W/"1e-/mvovEuhtN1hYjWJCVVEGQ" 
Date: Tue, 16 Jan 2018 17:45:51 GMT 
Connection: close 
Last-Modified: Tue, 16 Jan 2018 17:45:50 UTC 
 
{"response":{"executed":true}} 
5.4.2 Open Positions table 
This section describes the Open Positions table that contains open positions data such as floating profit/loss, charged commission, cumulative interest, and so on. 
RESPONSE 
 
 
Parameter 
Description 
Values 
t 
ID number of the table 
1 
ratePrecision 
The price precision of the instrument. It defines number of digits after the decimal point in the instrument price quote. 
Number 

tradeId 
The unique identification number of the open position. The number is unique within the same database that stores the account the position is opened on. 
String 
accountName 
The unique name of the account the position is opened on. The name is unique within the database where the account is stored. 
String 
accountId 
The unique identification number of the account the position is opened on. The number is unique within the database where the account is stored. 
String 
roll 
The cumulative amount of funds that is added the account balance for holding the position overnight. 
Number 
com 
The amount of funds subtracted from the account balance to pay for the broker's service in accordance with the terms and conditions of the account trading agreement. 
Number 
open 
The price the position is opened at. 
Number 
valueDate 
The simulated delivery date. The date when the position could be automatically closed. The date is provided in the yyyyMMdd format. It is applicable only for positions opened on accounts with the day netting trading mode. Otherwise, the value of this field is blank. 
String 
grossPL 
The current profit/loss of the position. It is expressed in the account currency. 
Number 
close 
The price at which the position can be closed at the moment. 
Number 
visiblePL 
The current profit/loss per one lot of the position. It is expressed in the account currency. 
Number 
isDisabled 
 
Boolean 
currency 
The symbol of the instrument. 
String 
isBuy 
The trade operation the position is opened by. The possible values are: 
True – Buy 
False – Sell 
Boolean 
amountK 
The amount of the position in thousand units. 
Number 
currencyPoint 
? 
Number 
time 
The date and time when the position was opened. 
String 
usedMargin 
The amount of funds currently committed to maintain the position. 
Number 
stop 
The price of the associated stop order (loss limit level). 
Number 
stopMove 
The number of pips the market should move before the stop order moves the same number of pips after it. 
If the trailing order is dynamic (automatically updates every 0.1 of a pip), then the value of this field is 1. 
If the order is not trailing, the value of this field is 0. 
Number 
limit 
The price of the associated limit order (profit limit level). 
Number 
isTotal 
Indicates the row is a summary of for whole table. 
Boolean 
action 
Type of update. Only applicable to updates through the socket. Possible values are: 
I – Insert 
U – Update 
D – Delete 
String 
Sample Response 
"open_positions":[{"t":1,"ratePrecision":5,"tradeId":"122743073","accountName":"01027808","accou ntId":"1027808","roll":0,"com":5,"open":1.19719,"valueDate":"","grossPL":1.74,"close":1.19632,"v isiblePL":8.7,"isDisabled":false,"currency":"EUR/USD","isBuy":false,"amountK":2,"currencyPoint": 0.2,"time":"09152017143932","usedMargin":52,"stop":0,"stopMove":0,"limit":0}] 
 
5.4.3 Closed Positions table 
This section describes the Closed Positions table that contains information about the positions closed during the current trading day such as realized profit/loss, charged commission, cumulative interest, and so on. 
RESPONSE 
 
 
Parameter 
Description 
Values 
t 
ID number of the table 
2 
ratePrecision 
The price precision of the instrument. It defines number of digits after the decimal point in the instrument price quote. 
Number 
tradeId 
The unique identification number of the open position. The number is unique within the same database that stores the account the position is opened on. 
String 
accountName 
The unique name of the account the position is opened on. The name is unique within the database where the account is stored. 
String 
roll 
The cumulative amount of funds that is added the account balance for holding the position overnight. 
Number 
com 
The amount of funds subtracted from the account balance to pay for the broker's service in accordance with the terms and conditions of the account trading agreement. 
Number 
open 
The price the position is opened at. 
Number 
valueDate 
The simulated delivery date. The date when the position could be automatically closed. The date is provided in the yyyyMMdd format. It is applicable only for positions opened on accounts with the day netting trading mode. Otherwise, the value of this field is blank. 
String 
grossPL 
The current profit/loss of the position. It is expressed in the account currency. 
Number 
close 
The price at which the position can be closed at the moment. 
Number 
visiblePL 
The current profit/loss per one lot of the position. It is expressed in the account currency. 
Number 
currency 
The symbol of the instrument. 
String 
isBuy 
The trade operation the position is opened by. The possible values are: True – Buy 
False – Sell 
Boolean 
amountK 
The amount of the position in thousand units. 
Number 
currencyPoint 
 
Number 
openTime 
The date and time when the position was opened. 
String 
closeTime 
The date and time when the position was closed. 
String 
isTotal 
Indicates the row is a summary of for whole table. 
 
action 	Type of update. Only applicable to updates through the socket. Possible 	String values are: I – Insert 
U – Update 
D – Delete 
Sample Response 
"closed_positions":[{"t":2,"ratePrecision":5,"tradeId":"122643271","accountName":"01027808","rol l":3.85,"com":10,"open":1.19446,"valueDate":"","grossPL":-30.3,"close":1.19749,"visiblePL":-
30.3,"currency":"EUR/USD","isBuy":false,"amountK":10,"currencyPoint":1,"openTime":"0906201715274 9","closeTime":"09152017143928"} 
5.4.4 Orders table 
This section describes the Orders table that contains information about orders. The data is kept in this table until all the orders are executed. 
RESPONSE 
 
 
Parameter 
Description 
Values 
t 
ID number of the table 
3 
ratePrecision 
The price precision of the instrument. It defines number of digits after the decimal point in the instrument price quote. 
Number 
orderId 
The unique identification number of the order. The number is unique within the same database that stores the account the order is placed on. 
String 
time 
The time when the order was created. 
String 
accountName 
The unique name of the account the position is opened on. The name is unique within the database where the account is stored. 
String 
accountId 
The unique identification number of the account the position is opened on. The number is unique within the database where the account is stored. 
String 
timeInForce 
The time-in-force option of the order. The possible values are: 
GTC – Good Till Cancelled 
IOC – Immediate Or Cancel 
FOK – Fill Or Kill  
DAY – Day Order 
GTD – Good Till Date 
String 
expireDate 
Time at which the order will expire. 
Number 
currency 
The symbol of the instrument. 
String 
isBuy 
The trade operation the position is opened by. The possible values are: 
True – Buy 
False – Sell 
Boolean 
buy 
The price the order is placed at. 
Number 
sell 
The price the order is placed at. 
Number 

type 
The order type. The possible values are: 
S – Stop 
ST – Trailing Stop  
L – Limit 
SE – Entry Stop 
LE – Entry Limit 
STE – Trailing Entry Stop 
LTE – Trailing Entry Limit 
C – Close 
CM – Close Market 
CR – Close Range 
O – Open 
OM – Open Market 
OR – Open Range 
M – Margin Call 
String 
status 
The state of the order. The possible values are: 
0	– Unknown 
1	– Waiting 
2	– InProcess 
3	– Canceled 
4	– Requoted 
5	– MarginCall 
6	– Executing 
7	– Pending 
8	– EquityStop 
9	– Executed 
10	– Activated 
Number 
amountK 
The amount of the position in thousand units. 
Number 
currencyPoint 
 
Number 
stopMove 
The number of pips the market should move before the stop order moves the same number of pips after it. 
If the trailing order is dynamic (automatically updates every 0.1 of a pip), then the value of this field is 1. 
If the order is not trailing, the value of this field is 0. 
Number 
stop 
The price of the associated stop order (loss limit level). 
Number 
stopRate 
 
Number 
limit 
The price of the associated limit order (profit limit level). 
Number 
limitRate 
 
Number 
isEntryOrder 
Indicates if the order is of Entry type (resting order). 
Boolean 
ocoBulkId 
The unique identifier of an existing OCO group which the order is linked to. The number is unique within the same database that stores the account the contingent order is placed on. 
Number 
isNetQuantity 
Indicates if the order is of Net Amount type. 
Boolean 
isLimitOrder 
Indicates if the order is of Limit type. 
Boolean 
isStopOrder 
Indicates if the order is of Stop type. 
Boolean 
isELSOrder 
Indicates if the order is of Entry with Limit and Stop type. 
Boolean 
stopPegBaseType 
 
Number 
limitPegBaseType 	 	Number 
range 	 	Number 
action 	Type of update. Only applicable to updates through the socket. 	String 
Possible values are: 
I – Insert 
U – Update 
D – Delete 
Sample Response 
"orders":[{"t":3,"ratePrecision":5,"orderId":"236780744","tradeId":"123022436","time":"101720171
03642","accountName":"01073265","accountId":"1073265","timeInForce":"GTD","expireDate":"10182017 205900","currency":"EUR/USD","isBuy":true,"buy":1.16079,"sell":0,"type":"LE","status":1,"amountK ":1,"currencyPoint":0.1,"stopMove":0,"stop":0,"stopRate":0,"limit":0,"limitRate":0,"isEntryOrder ":true,"ocoBulkId":0,"isNetQuantity":false,"isLimitOrder":true,"isStopOrder":false,"isELSOrder":
false,"stopPegBaseType":-1,"limitPegBaseType":-1,"range":0}] 
5.4.5 Summary table 
This section describes the Summary table that contains summarized information such as the average entry price, profit/loss, and so on for every instrument currently traded. 
RESPONSE 
 
 
Parameter 
Description 
Values 
t 
ID number of the table 
5 
ratePrecision 
The price precision of the instrument. It defines number of digits after the decimal point in the instrument price quote. 
Number 
offerId 
The unique identification number of the instrument. 
Number 
currency 
The symbol of the instrument. 
String 
plSell 
The current profit/loss of all Sell positions. It does not include commissions and interests. 
Number 
amountKSell 
The sum of amounts of Sell positions in thousand units. 
Number 
avgSell 
The average open price of Sell positions. 
Number 
closeBuy 
The current market price, at which Sell positions can be closed. 
Number 
closeSell 
The current market price, at which Buy positions can be closed. 
Number 
avgBuy 
The average open price of Buy positions. 
Number 
amountKBuy 
The sum of amounts of Buy positions in thousand units. 
Number 
rollSum 
The cumulative amount of funds that is added the account balance for holding the positions overnight. 
Number 
usedMarginSell 
The amount of funds currently committed to maintain Sell positions. 
Number 
usedMarginBuy 
The amount of funds currently committed to maintain Buy positions. 
Number 
isSellDisabled 
 
Boolean 
isBuyDisabled 
 
Boolean 
plBuy 
The current profit/loss of all Buy positions. It does not include commissions and interests. 
Number 
amountK 
The sum of amounts of all positions in thousand units. 
Number 
currencyPoint 
 
Number 
grossPL 
The current profit/loss of all positions. It does not include commissions and interests. 
Number 
netPL 	The current profit/loss of all positions. It includes commissions and 	Number interests. 
netStop 	 	Number 
netStopMove 	 	Number 
netLimit 	 	Number 
isTotal 	Indicates the row is a summary of for whole table. 	Boolean 
action 	Type of update. Only applicable to updates through the socket. Possible 	String values are: I – Insert 
U – Update 
D – Delete 
Sample Response 
"summary":[{"t":5,"ratePrecision":5,"offerId":1,"currency":"EUR/USD","plSell":6.09,"amountKSell" :7,"avgSell":1.19719,"closeBuy":1.19632,"closeSell":0,"avgBuy":0,"amountKBuy":0,"rollSum":0,"use dMarginSell":182,"usedMarginBuy":0,"isSellDisabled":false,"isBuyDisabled":true,"plBuy":0,"amount K":-7,"currencyPoint":-
0.7,"grossPL":6.09,"netPL":1.09,"netStop":0,"netStopMove":0,"netLimit":0}] 
5.4.6 Accounts table 
This section describes the Accounts table that contains the trading account data such as funds used in trading, idle funds, profits/losses, certain account limitations, and so on. 
RESPONSE 
 
 
Parameter 
Description 
Values 
t 
ID number of the table 
6 
ratePrecision 
The price precision of the instrument. It defines number of digits after the decimal point in the instrument price quote. 
Number 
accountId 
The unique identification number of the account the position is opened on. The number is unique within the database where the account is stored. 
String 
balance 
The amount of funds on the account. This amount does not include floating profit and loss 
Number 
usdMr 
The amount of funds used to maintain all open positions on the account. 
Number 
mc 
The limitation state of the account. Each state defines the operations that can be performed on the account. The possible values are: Y – Margin call (all positions are liquidated, new positions cannot be opened). 
W – Warning of a possible margin call (positions may be closed, new positions cannot be opened). 
Q – Equity stop (all positions are liquidated, new positions cannot be opened up to the end of the trading day). 
A – Equity alert (positions may be closed, new positions cannot be opened up to the end of the trading day). 
N – No limitations (no limitations are imposed on the account operations). 
String 
accountName 
The unique name of the account the position is opened on. The name is unique within the database where the account is stored. 
String 
usdMr3 
The amount of funds used to maintain all open positions on the account with the three-level margin policy. 
Number 
hedging 	The type of the position maintenance. It defines how trade operations 	String can be performed on the account. The possible values are: 
Y – Hedging is allowed. In other words, both buy and sell positions can be opened for the same instrument at the same time. To close each buy or sell position, an individual order is required. 
N – Hedging is not allowed. In other words, either a buy or a sell position can be opened for the same instrument at a time. Opening a position for the instrument that already has open position(s) of the opposite trade operation always causes closing or partial closing of the open position(s). 
0 – Netting only. In other words, for each instrument there exists only one open position. The amount of the position is the total amount of the instrument, either bought or sold, that has not yet been offset by opposite trade operations. 
D – Day netting. In other words, for each instrument there exists only one open position. Same as Netting only, but within a trading day. If the position is not offset during the same trading day it is opened, it is closed automatically on simulated delivery date. 
F – FIFO. Positions open and close in accordance with the FIFO (Firstin, First-out) rule. Hedging is not allowed. 
usableMargin3 	 	Number 
usableMarginPerc 	 	Number 
usableMargin3Perc 	 	Number 
equity 	The amount of funds on the account, including profits and losses of all 	Number open positions (the floating balance of the account). 
usableMargin 	The amount of funds available to open new positions or to absorb 	Number losses of the existing positions. 
dayPL 	The amount of profits and losses (both floating and realized) of the 	Number current trading day. 
grossPL 	The amount of profits and losses of all open positions on the account. 	Number 
isTotal 	Indicates the row is a summary of for whole table. 	Boolean 
action 	Type of update. Only applicable to updates through the socket. 	String 
Possible values are: 
I – Insert 
U – Update 
D – Delete 
Sample Response 
"accounts":[{"t":6,"ratePrecision":0,"accountId":"1027808","balance":39208.63,"usdMr":116,"mc":" N","accountName":"01027808","usdMr3":58,"hedging":"N","usableMargin3":39152.26234,"usableMarginP erc":99.70416,"usableMargin3Perc":99.85208,"equity":39210.26234,"usableMargin":39094.26234,"dayP L":-63.21766,"grossPL":1.63234}] 
6 Trading Orders 
REQUEST 
 


Header 
Description 
Values 
Req 
Method 
HTTP method 
POST 
Y 
6.1 /trading/open_trade 
URI 	Resource 	/trading/open_trade 	Y 
Parameter 	Description 	Values 	Req’d 
account_id 	The trade‘s account identifier. Can be found in 	String 	Y 
Accounts trading table as accountId 5.4.6. Not to be confused with accountName. 
symbol 	The trade’s currency pair or instrument. 	String 	Y 
is_buy 	Defines the order’s market side. Valid values: 	Boolean 	Y 
‘true’, ‘false’. If ‘true, order is a buy.  If ‘false’, order is a sell. 
amount 	The trade‘s amount in lots. 	String 	Y 
stop 	Rate of the stop order attached to the position 	Number 	N 
created by the AtMarket or MarketRange order’s execution. 
trailing_step 	Rate of the limit order attached to the position 	Number 	N 
created by the AtMarket or MarketRange order’s execution. 
limit 	Rate of the limit order attached to the position 	Number 	N 
created by the AtMarket or MarketRange order’s execution. 
is_in_pips 	Defines if the trade‘s stop/limit rate is in pips. 	Boolean 	N 
at_market 	For MarketRange orders, the ‘at_market’ value 	Number 	N 
is added to the BBO to define the market range. 
If not sent, a value of 0 is used.   
order_type 	The type of the order execution. Market Order 	String  	Y type choices: “AtMarket”, “MarketRange”. 
time_in_force 	For AtMarket orders, valid value are: DAY, GTC, 	String 	Y 
IOC, FOK.   
For MarketRange orders, valid values are: IOC, FOK. 
Sample Request 
POST /trading/open_trade HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 ba 
account_id=01027808&symbol=EUR%2FUSD&is_buy=false&rate=0&amount=10&at_market=0&order_type=AtMark et&time_in_force=FOK 
0 
 	 	 	 
RESPONSE 
 
 	 
Parameter 
Description 
Values 	 
executed 
Execution successful 
Boolean 
data 
Contains array of created orders. 
Array of Objects 
type 
Defines the type of the order. 
Number 
orderID 
The order identifier. 
Number 
With, X-HTTP-MethodOptions: nosniff
Content-Type: application/json; charset=utf-8 
Content-Length: 42 
ETag: W/"2a-hJHeaXvf4OSF9grD6BYptQ" 
Date: Fri, 18 Aug 2017 21:05:21 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Fri, 18 Aug 2017 21:05:21 UTC 
 
{"response":{"executed":true},"data":{"type":0,"orderId":81712802}} 
6.2 /trading/close_trade 
REQUEST 
 

Header 	Description 	Values 
Req 
Method 	HTTP method 	POST 
Y 
URI 	Resource 	/trading/close_trade 
Y 
Parameter 	Description 	Values 
Req’d 
trade_id 	The trade identifier 	String 
Y 
rate 	The trade‘s rate. 	Number 
N 
amount 	The trade‘s amount in lots. 	Number 
Y 
at_market 	Defines the market range. 	Number 
Y* 
order_type 	The type of the order execution. Market Order 	String  type choices: “AtMarket”, “MarketRange”. 
Y 
time_in_force 	The time in force of the order execution. Time 	String in force choices: “IOC”, “GTC”, “FOK”, “DAY”, “GTD”. 
Y 
Sample Request 
POST /trading/close_trade HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 9a 
trade_id=81713394&rate=0&amount=8&at_market=0&order_type=AtMarket&time_in_force=GTC 0 

Conditional Requirements 
*) at_market is required if order_type is set to “MarketRange” 

 	 	 	 
RESPONSE 
 

 	 
Parameter 
Description 

Values 	 
executed 
Execution successful 

Boolean 
data 
Contains array of created orders. 

Array of Objects 
type 
Defines the type of the order. 

Number 
orderID 
The order identifier. 

Number 
With, X-HTTP-MethodOptions: nosniff
Content-Type: application/json; charset=utf-8 
Content-Length: 42 
ETag: W/"2a-hJHeaXvf4OSF9grD6BYptQ" 
Date: Fri, 18 Aug 2017 21:06:49 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Fri, 18 Aug 2017 21:06:49 UTC 
 
{"response":{"executed":true},"data":{"type":0,"orderId":81713394}} 
6.3 /trading/change_order 
REQUEST 
 

Header 	Description 	Values 
Req 
Method 	HTTP method 	POST 
Y 
URI 	Resource 	/trading/change_order 
Y 
Parameter 	Description 	Values 
Req’d 
order_id 	The order identifier. 	String 
Y 
rate 	The order‘s new rate. 	Number 
Y 
range The order‘s range (is used for “RangeEntry” Number orders only). 
Y 
amount 	The trade‘s amount in lots. 	String 
Y 
trailing_step 	The trailing step for the stop rate. 	Number 
N 
Sample Request 
POST /trading/change_order HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 82 
order_id=235045369&rate=1.7&range=0&amount=1&trailing_step=2 0 

 	 	 	 
RESPONSE 
 
 	 
Parameter 
Description 
Values 	 
executed 
Execution successful 
Boolean 
Data 
Always null 
Null 
6.4 /trading/delete_order 
REQUEST 
 

Header 	Description 	Values 
Req 
Method 	HTTP method 	POST 
Y 
URI 	Resource 	/trading/delete_order 
Y 
Parameter 	Description 	Values 
Req’d 
order_id 	The order’s identifier 	String 
Y 
Sample Request 
POST /trading/delete_order HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 58 
order_id=235045369 
0 

With, X-HTTP-MethodOptions: nosniff
Content-Type: application/json; charset=utf-8 
Content-Length: 42 
ETag: W/"2a-hJHeaXvf4OSF9grD6BYptQ" 
Date: Tue, 22 Aug 2017 12:37:52 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Tue, 22 Aug 2017 12:37:52 UTC  
{"response":{"executed":true},"data":null} 
 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
Data 	Always null 	null 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 42 
ETag: W/"2a-hJHeaXvf4OSF9grD6BYptQ" 
Date: Fri, 18 Aug 2017 21:12:26 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Fri, 18 Aug 2017 21:12:26 UTC  
{"response":{"executed":true},"data":null} 
 
 
6.5 /trading/create_entry_order 
REQUEST 
 
Header 	Description 	Values 	Req 
Method 	HTTP method 	POST 	Y 
URI 	Resource 	/trading/create_entry_order 	Y 
Parameter 	Description 	Values 	Req’d 
account_id 	The trade‘s account identifier. Can be found 	String 	Y 
in Accounts trading table as accountId 5.4.6. 
Not to be confused with accountName. 
symbol 	The trade‘s symbol. 	String 	Y 
is_buy 	Defines the trade‘s market side (if true, then 	Boolean 	N 
buy trade, otherwise sell trade). Temporarily not required by the server and defaults to true but this will change. 
rate 	The trade‘s rate. 	Number 	N 
amount 	The trade‘s amount in lots. 	String 	Y 
stop 	The trade‘s stop rate. 	Number 	N 
trailing_step 	The trailing step for the stop rate. 	Number 	N 
trailing_stop_step 	The trailing step for the order stop rate. 	Number 	N 
limit 	The trade‘s limit rate. 	Number 	Y 
is_in_pips 	Defines if the trade‘s stop/limit rate is in pips. 	Boolean 	Y 
range 	The order’s range for “RangeEntry” 	Number 	N 
order_type 	The type of order =  “Entry” or “RangeEntry” 	String  	Y 
time_in_force 	Valid values: “GTC”,“DAY”,“GTD”,“IOC”,“FOK” 	String 	Y 
expiration 	The order’s expiration date for GTD. Format is 	String 	N 
“YYYY-MM-DD hh:mm” or “YYYY-MM-DD” in UTC. Not including time sets the expiration at start of trading day. 
Sample Request 
POST /trading/create_entry_order HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 be 
account_id=01027808&symbol=EUR%2FUSD&is_buy=true&rate=1.16&is_in_pips=false&amount=10&order_type
=Entry&time_in_force=GTC 
0 
 	 	 	 
RESPONSE 
 
 	 
Parameter 
Description 
Values 	 
executed 
Execution successful 
Boolean 
data 
Contains array of created orders. 
Array of Objects 
type 
Defines the type of the order. 
Number 
orderID 
The order identifier. 
Number 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept 
X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 42 
ETag: W/"2a-hJHeaXvf4OSF9grD6BYptQ" 
Date: Tue, 22 Aug 2017 12:51:08 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Tue, 22 Aug 2017 12:51:08 UTC 
 
{"response":{"executed":true},"data":{"type":0,"orderId":81716002}} 
6.6 /trading/simple_oco 
REQUEST 
 



Header 
Description 
Values 

Req 
Method 
HTTP method 
POST 

Y 
URI 
Resource 
/trading/open_trade 

Y 
Parameter 
Description 
Values 

Req’d 
account_id 
The trade‘s account identifier. Can be found in Accounts trading table as accountId 5.4.6. 
Not to be confused with accountName. 
String 

Y 
symbol 
The trade‘s symbol. 
String 

Y 
amount 
The trade‘s amount in lots. 
String 

Y 
is_in_pips 
Defines if the order‘s stop/limit rate is in pips. 
Boolean 

Y 
time_in_force 
The time in force of the order execution. Time in force choices: “IOC”, “GTC”, “FOK”, “DAY”, “GTD”. 
String 

Y 
expiration 
The order‘s expiration date. Format is 
“YYYY-MM-DD hh:mm” or “YYYY-MM-DD” in UTC. Not including time sets the expiration at start of trading day. 
String 

Y 
is_buy 
Defines the trade‘s market side (if true, then buy trade, otherwise sell trade). Temporarily not required by the server and defaults to true but this will change. 
Boolean 

N 
rate 
The trade‘s rate. 
Number 

Y 
stop 
The trade‘s stop rate. 
Number 

Y 
trailing_step 
The trailing step for the stop rate. 
Number 

Y 
trailing_stop_step 
The trailing step for the first order‘s stop rate. 
Number 

Y 
limit 
The trade‘s limit rate. 
Number 

Y 
is_in_pips 
Defines if the trade‘s stop/limit rate is in pips. 
Boolean 

Y 
at_market 
Defines the market range. 
Number 

Y 
order_type 	The type of the order execution. Market 	String  	Y 
Order type choices: “AtMarket”, “MarketRange”. 
is_buy2 	Defines the trade‘s market side (if true, 	Boolean 	N 
then buy trade, otherwise sell trade). Temporarily not required by the server and defaults to true but this will change. 
rate2 	The second order‘s rate. 	Number 	Y 
stop2 	The second order‘s stop rate. 	Number 	Y 
trailing_step2 	The trailing step for the second order‘s rate. 	Number 	Y 
trailing_stop_step	The trailing step for the second order‘s stop 	Number 	Y 
2 	rate. 
limit2 	The second order‘s limit rate. 	Number 	Y 
Sample Request 
POST /trading/simple_oco HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 12f 
account_id=01027808&symbol=EUR%2FUSD&amount=10&is_buy=true&is_in_pips=false&time_in_force=GTC&ra te=1.17&stop=1.16&trailing_step=0&trailing_stop_step=0&limit=1.18&is_buy2=false&rate2=1.09&stop2
=0&trailing_step2=0&trailing_stop_step2=0 0 
 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
Data 	Always null 	null 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 49 
ETag: W/"31-L3CcNxew4MPQRwvnuhu6jw" 
Date: Tue, 22 Aug 2017 12:53:30 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Tue, 22 Aug 2017 12:53:30 UTC 
 
{"response":{"executed":true},"data":[null,null]} 
6.7 /trading/add_to_oco 
REQUEST 
 





Header 
Description 


Values 

Req 
Method 
HTTP method 


POST 

Y 
URI 
Resource 


/trading/add_to_oco 

Y 
Parameter 
Description 


Values 

Req’d 
orderIds 	The list orders identifiers. 	String 
Y 
ocoBulkId 	The oco bulk identifier (if equals zero then new 	Number oco order will be created). 
Y 
Sample Request 
POST /trading/add_to_oco HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 77 
orderIds=235053902&orderIds=235053904&ocoBulkId=0 0 

 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
Data 	Always null 	null 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 49 
ETag: W/"31-L3CcNxew4MPQRwvnuhu6jw" 
Date: Sun, 20 Aug 2017 22:43:50 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure 
Last-Modified: Sun, 20 Aug 2017 22:43:50 UTC 
 
{"response":{"executed":true},"data":[null,null]} 
6.8 /trading/remove_from_oco 
REQUEST 
 

Header 	Description 	Values 
Req 
Method 	HTTP method 	POST 
Y 
URI 	Resource 	/trading/remove_from_oco 
Y 
Parameter 	Description 	Values 
Req’d 
orderIds 	The list orders identifiers. 	string[] 
Y 
Sample Request 
POST /trading/remove_from_oco HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 77 
orderIds=235053902&orderIds=235053904&ocoBulkId=0 0 

 	 	 	 
RESPONSE 	 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
Data 	Always null 	null 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 49 
ETag: W/"31-L3CcNxew4MPQRwvnuhu6jw" 
Date: Tue, 22 Aug 2017 12:22:17 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Tue, 22 Aug 2017 12:22:17 UTC 
 
{"response":{"executed":true},"data":[null,null]} 
6.9 /trading/edit_oco 
REQUEST 
 

Header 	Description 	Values 
Req 
Method 	HTTP method 	POST 
Y 
URI 	Resource 	/trading/edit_oco 
Y 
Parameter 	Description 	Values 
Req’d 
ocoBulkId 	The oco bulk identifier  	Number 
Y 
addOrderIds 	The list orders identifiers to add to the oco 	String order. 
Y 
removeOrderIds The list orders identifiers to remove from the String oco order. 
Y 
Sample Request 
POST /trading/edit__oco HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 77 
ocoBulkId=236175794&addOrderIds=235053904&ocoBulkId=0 0 

 	 	 	 
RESPONSE 
 

 	 
Parameter 	Description 

Values 	 
executed 	Execution successful 

Boolean 
Data 	Always null 

null 
Sample Response 
 


6.10 /trading/change_trade_stop_limit 
REQUEST 	 
Header 
Description 
Values 
Req 
Method 
HTTP method 
POST 
Y 
URI 
Resource 
/trading/change_trade_stop_limit 
Y 
Parameter 
Description 
Values 
Req’d 
trade_id 
The trade identifier. 
String 
Y 
is_stop 
Defines stop or limit should be changed (if true, then stop should be changed, otherwise limit). 
Boolean 
Y 
rate 
The new rate for the trade‘s stop/limit order. 
Number 
Y 
is_in_pips 
 Defines if the order‘s stop/limit rate is in pips. 
Boolean 
Y 
trailing_step 
The trailing step for the stop rate. 
Number 
Y 
Sample Request 
POST /trading/change_trade_stop_limit HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 77 
trade_id=122835946&is_stop=true&rate=1.19611&is_in_pips=false&trailing_step=0 0 

 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
Data 	Always null 	null 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 42 
ETag: W/"2a-hJHeaXvf4OSF9grD6BYptQ" 
Date: Fri, 18 Aug 2017 21:17:10 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure 
Last-Modified: Fri, 18 Aug 2017 21:17:10 UTC  
{"response":{"executed":true},"data":null} 
6.11 /trading/change_order_stop_limit 
REQUEST 
 



Header 
Description 

Values 
Req 
Method 
HTTP method 

POST 
Y 
URI 
Resource 

/trading/open_trade 
Y 
Parameter 
Description 

Values 
Req’d 
order_id 
The order identifier. 

String 
Y 
limit 	The new rate for the trade’s limit order. 
Number 
Y 
is_limit_in_pips 	Defines if the order’s limit rate is in pips. 
Boolean 
Y 
stop 	The new rate for the trade’s stop order. 
Number 
Y 
is_stop_in_pips 	Defines if the order’s stop rate is in pips. 
Boolean 
Y 
trailing_step 	The trailing step for the stop rate. 
Number 
N 
Sample Request 
POST /trading/change_order_stop_limit HTTP/1.1 
User-Agent: request 
Accept: application/json 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer 4hACTl5oon9EvtM8AARAa11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api-demo.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 53 
order_id=72513348&limit=1.19&is_limit_in_pips=false&stop=1.18&is_stop_in_pips=false 0 

 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful. 	Boolean 
data 	Contains array of modified orders. 	Array of Objects 
type 	Defines the type of the order. 	Number 
orderID 	The order identifier. 	Number 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 99 
ETag: W/"63-Yx45ecYLqzqI+h55XtkeqA" 
Date: Wed, 10 Jan 2018 16:44:21 GMT 
Connection: close 
Last-Modified: Wed, 10 Jan 2018 16:44:21 UTC 
 
{"response":{"executed":true},"data":[{"type":0,"orderId":72513878},{"type":0,"orderId":72513881 }]} 
6.12 /trading/close_all_for_symbol 
REQUEST 
 


Header 
Description 
Values 
Req 
Method 
HTTP method 
POST 
Y 
URI 
Resource 
/trading/close_all_for_symbol 
Y 
Parameter 
Description 
Values 
Req’d 
account_id 
The order’s account identifier. Can be found in Accounts trading table as accountId 5.4.6. Not to be confused with accountName. 
String 
Y 
forSymbol 
Defines if trades should be closed for the specified symbol. 
Boolean 
Y 
symbol 	The trades symbol. 	String 	Y 
order_type 	The type of the order execution. Market Order 	String 	Y type choices: “AtMarket”, “MarketRange”. 
time_in_force 	The time in force of the order execution. Time 	String 	Y in force choices: “IOC”, “GTC”, “FOK”, “DAY”, “GTD”. 
Sample Request 
POST /trading/close_all_for_symbol HTTP/1.1 
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: api.fxcm.com 
Connection: close 
Transfer-Encoding: chunked 
 9f 
account_id=01027808&forSymbol=true&symbol=EUR%2FUSD&order_type=AtMarket&time_in_force=GTC 0 
 	 	 	 
RESPONSE 
 	 	 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
Data 	Always null 	null 
Sample Response 
HTTP/1.1 200 OK 
Vary: X-HTTP-Method-Override 
Access-Control-Allow-Methods: GET,PUT,POST,DELETE 
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept X-Content-Type-Options: nosniff 
Content-Type: application/json; charset=utf-8 
Content-Length: 42 
ETag: W/"2a-hJHeaXvf4OSF9grD6BYptQ" 
Date: Fri, 18 Aug 2017 21:12:43 GMT 
Connection: close 
Set-Cookie: BIGipServerapi.fxcm.com=3698998282.42783.0000; path=/; Httponly; Secure Last-Modified: Fri, 18 Aug 2017 21:12:43 UTC  
{"response":{"executed":true},"data":null} 
7 Historical Data 
Allow user to retrieve candles for a given instrument at a given time frame. If time range is specified, number of candles parameter is ignored, but still required. 
REQUEST 
 


Header 
Description 
Values 
Req’d 
Method 
HTTP method 
GET 
Y 
URI 
Resource 
/candles/{offer_id}/{period_id} 
Y 
{offer_id} 
ID of requested symbol 
Integer from Offer trading table (see section 5.4.1) 
Y 
{period_id} 
Requested timeframe 
String, one of: 
m1,m5,m15,m30,H1,H2,H3,H4,H6,H8,D1,W1,M1 
Y 
Parameter 
Description 
Values 
Req’d 
num 
Number of candles requested 
Integer value between 1 and 10,000 
Y 
from 
Beginning of time range 
Integer representing Epoch time in seconds 
N 
	to 	End of time range 	Integer representing Epoch time in seconds 
N 
Sample  Request 
GET /candles/1/h1/?num=10 
HTTP/1.1  
User-Agent: request  
Accept: application/json  
Content-Type: application/x-www-form-urlencoded 
Authorization: Bearer n5BoWSRFJvYi2GEQAAKya11f7bc3d6b14ff77f65dd9d21df16ac1b4c41ea Host: www-beta3.fxcorporate.com  
Connection: close 

 
RESPONSE 
 
Parameter 	Description 	Values 	 
executed 	Execution successful 	Boolean 
error 	Error text. Empty if no error 	String 
instrument_id 	ID of requested symbol 	Integer from Offer trading table (see section 5.4.1) 
period_id 	Timeframe of the candles 	String, one of: m1,m5,m15,m30,H1,H2,H3,H4,H6,H8,D1,W1,M1 
candles 	Array of candles 	Format of candles: [timestamp (epoch), BidOpen, BidClose, 
BidHigh, BidLow, AskOpen, AskClose, AskHigh, AskLow, TickQty] 
Sample Response 
HTTP/1.1 200 OK  
Access-Control-Allow-Methods: GET,PUT,POST,DELETE  
Access-Control-Allow-Headers: X-Requested-With, X-HTTP-Method-Override, Content-Type, Accept  X-Content-Type-Options: nosniff  
Content-Type: application/json; charset=utf-8  
Content-Length: 903  
ETag: W/"387-HuKY4xMbqr0aSMecoNI5XA"  
Date: Thu, 22 Jun 2017 18:58:26 GMT  
Connection: close  
Last-Modified: Thu, 22 Jun 2017 18:58:26 UTC  
Cache-Control: public, max-age=86400  
Strict-Transport-Security: max-age=31536000; includeSubDomains  
Vary: Accept-Encoding    
{"response":{"executed":true,"error":""},"instrument_id":"1","period_id":"h1", 
"candles": 
[[1498125600,1.11641,1.11643,1.11664,1.11592,1.11664,1.11667,1.11688,1.11616,10423], 
[1498129200,1.11643,1.11641,1.11654,1.11558,1.11667,1.11664,1.11677,1.1158,11229], 
[1498132800,1.11641,1.116,1.11712,1.11578,1.11664,1.11622,1.11735,1.11602,16846], 
[1498136400,1.116,1.11586,1.1163,1.11513,1.11622,1.1161,1.11653,1.11537,18744], 
[1498136400,1.116,1.11586,1.1163,1.11513,1.11622,1.11611,1.11653,1.11537,18743], 
[1498140000,1.11586,1.11558,1.11613,1.11495,1.1161,1.11582,1.11637,1.11518,20571], 
[1498140000,1.11586,1.11558,1.11613,1.11495,1.1161,1.11582,1.11637,1.11518,20572], 
[1498143600,1.11558,1.11457,1.116,1.11442,1.11582,1.11481,1.11623,1.11466,15800], 
[1498147200,1.11457,1.11542,1.11549,1.11457,1.11481,1.11566,1.11572,1.11481,7491], 
[1498150800,1.11542,1.1142,1.11563,1.11382,1.11566,1.11444,1.11588,1.11406,8687]]} 
 
