
# Amazon and Flipkart design - High Level Design

### Func Requirement:
- Cart/Wishlist
- Checkout
- Search
- View order

### Non Func Requirement:
- Low latency
- High availability
- High consumption

![Amzn-1.png](..%2Fimages%2FAmzn-1.png)

![Amzn-2.png](..%2Fimages%2FAmzn-2.png)

- InBound Service - It talks to various service on the supplier front. It gets data such as supplier
  wants to onboard new data. It puts various messages in kafka which various service consumes to onboard 
  new item.
- Item Service - It is used for CRUD operation on items.
- Search consumer - it responsible to let new items to be visible to consumer. 
  It stores data into elastic search.
- Search service - it talks to frontend or client to provide items detail or who to want to search items 
  using elastic search. Also, When a cust searches an item, search text is sent to the kafka against user
  so that recommendation service uses these data to recommend items. Also, to get user details. it first searches
  redis for the data, if data is not present, then it calls UserService.
- Serviceability + TAT service - When a user wants to search for an items, it's imp to check whether that
  item is available at particular location. So, this service is used to identify where the product is and check whether
  it is deliverable to customer or not. For TAT - It also calculates how much time item will take to get 
  delivered to the customer. Using these filter, only eligible items are shown to the customer.
  This service also calls warehouse service to get repository of all the items in the warehouse. And also it calls
  logistic service to get details of courier agent. And using this, it calculates the time item will take if an order is placed.[Using graph]
- User service - It is used to provide user info to their clients. For eg: search service uses customer info params
  through which it calls serviceability service.
- Wishlist/Cart service - It is responsible to store details of items in DB.[Repository]
- Logistic service - Here, operation such as order/cancellation of items takes place. Also, it provides location/pincode
  of all the courier partners. features involve such as transportation etc.
- Warehouse service - It keeps tracks of items in a particular warehouse such as stocks level, item location and order fulfillment,
  shipping and receiving[inbound and outbound].
- Spark streaming - it responsible for generating reports such as what was the most bought product in 30 mins.
- Hadoop cluster - Spark streaming service tells which user bought/search/like which product etc.
- Spark cluster - It has ML model which can be used to recommend items on the basis of [user and category].
  Eg - for UserA and product electronics - XYZ is recommended items. The recommend service pulls data from spark cluster for the recommended product.


- Order taking service - It is responsible for taking order by user. It uses MySQL db.
  Why MySql - As User/Item/Order/Payment has a relationship with each other, it is important to have atomicity ie. there is a
  transaction in which upsert operation is required in each tables. If any operation fails, then all every other operation
  is rolled back. When an order is placed, an order id is generated and order id and time is stored in [Redis].
  Eg: Redis - Order1 | 10:00 | CREATED
- Inventory service - Is responsible for blocking item for customer.  
- Payment service - It is responsible for payment. 
  Few scenarios: 
  1. If payment is successful, then delete from REdis and update Order taking DB and sends event into Kafka.
  2. If payment is fails, then delete from Redis --> update count + 1 in inventory & update order as failed in OTS DB.
  3. If redis record expires, then increase count in inventory and mark order as failed in order taking service DB.
  All the events are sent to Kafka for order processing.
- Order Processing service - It have details of all the order ie. where is status of order - Deleivered etc.
- Historical Order service - Through this, we can get details of historical orders. And it inserts data into Cassandra.
- Notification service - Used to notify user about the order etc.

## Database
- MongoDb [item] db - Unstructured db is required as diff product have diff parameters. Such as 
  for TV - size,LED etc || Tshirt - color, size, collar etc.
- MySQL[Cart/Wishlist] DB - Data related to wishlist and cart items are stored in structured DB.


### Reference
https://www.youtube.com/watch?v=EpASu_1dUdE&list=PLhgw50vUymyckXl3D1IlXoVl94wknJfUC&index=1