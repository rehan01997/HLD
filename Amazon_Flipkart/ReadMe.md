
## Amazon and Flipkart design - High Level Design

Func Requirement:
- Cart/Wishlist
- Checkout
- Search
- View order

Non func requirement
- Low latency
- High availability
- High consumption


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
- User service - It is used to provide user info to their clients. For eg: search service uses customer info params
  through which it calls serviceability service.
- Wishlist/Cart service - It is responsible to store details of items in DB.[Repository]
- Spark streaming - it responsible for generating reports such as what was the most bought product in 30 mins.  
- Hadoop cluster - Spark streaming service tells which user bought/search/like which product etc.
- Spark cluster - It has ML model which can be used to recommend items on the basis of [user and category].
  Eg - for UserA and product electronics - XYZ is recommended items. The recommend service pulls data from spark cluster for the recommended product.


- MongoDb [item] db - Unstructured db is required as diff product have diff parameters. Such as 
  for TV - size,LED etc || Tshirt - color, size, collar etc.
- MySQL[Cart/Wishlist] DB - Data related to wishlist and cart items are stored in structured DB.
- 
