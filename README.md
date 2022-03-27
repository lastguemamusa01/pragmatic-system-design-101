# pragmatic-system-design-101
resume of the udemy course pragmatic system design 101 course

For system design interviews. 
senior engineer -> make better architectural design decisions


no design is perfect.
always tradoffs
design is personal. 

collection of ideas of how to approach this topics.

## Building blocks 

Service -> box with name
cluster -> boxes of services
user -> user icon

user communicate by http with the service

database -> drum

synchronous communication like http call -> solid arrow -> format of url and rest request type

for asynchronous messaging -> usually use queues -> dotted line(queue)


## Making Estimates

system latency 
critical metrics

### latency

> means is faster
cpu cache > RAM > Disk > local network > global network

### Throughput

| component | reads per second | writes per second |
| ------------- | ------------- | ------------- |
| RDBMS  | 10,000  | 10,000 |
| Distributed cache | 100,000 | 100,000 | 
| Message Queue | 100,000 | 100,000 | 
| No sql | 20,000 - 50,000 | 10,000 - 25,000 |


no sql is huge database instance
RDBMS -> IS you need to read 100,000 reads -> 10 seconds -> use RDBMS

### Capacity

| component | maximum effective capacity | 
| ------------- | ------------- |
| RDBMS  | 3TB  |
| Distributed cache | 16 GB - 128 GB | 
| No sql | Depends | 

this is not scale vertically , this is for horizontal scaling. for the single instance capacity.

no sql -> capacity is unlimited -> using sharding and consistent hashing.

## Networking

### Load Balancing

load balancer -> is a machine(physical machine or Virtual machine) that runs a reverse proxy software. separate instance from your application.
load balacer usually set up in pairs, if one fail another one is working fine.

load balancer runs a software called a reverse proxy. 
the goal of software is to distrubte the requests between multiple servers that host the actual applciation.

example: 

desing udemy.com

1000 request per seconds 

we need multiple servers

![image](https://user-images.githubusercontent.com/25869911/158472209-cc2081f2-bced-42bc-a393-24944df20063.png)


### Type of load balancers

* layer 4
  * transport layer
  * has access to: TCP or UDP, IP, PORT
* layer 7
  * application layer
  * has access to everithing layer 4 has
  * In addition, has access to HTTP headers, coockies, payload

open sources load balancer : NGINX, Haproxy, traefik


### pros of using load balancer

load balancers make our system more resilient. if one server is down, load balancer knows and redirect the traffic to another server.

makes your system horizontally scalable -> horizontal scalling -> just add more servers


### Summary

* Load balancer is a machine that acts as a reverse proxy
* For each request it picks one of the servers based on its strategy
* Most common strategy is Round robin, but there are others
* Load balancers make your system more scalable and resilient

## CDN(Content Devliery Network)
 

user want to open udemy.com page in his browser.

that farther the signal has to travel, the more time will take

usa user with usa server get faster load thant user from south korea with usa server.

one solution would be to replicate your site content in different physical locations.
this will be very expensive -> server on japan , india

CDN - your keep you servers where they are, like usa. CDN will have their own servers that are spread across the globe.
you put your static content on those servers and your users will fetch it from the server cloasest to them automatically.

CDN is basically a cache for your static assets. 

example is udemy -> 
server: cloudflare

udemy is using cloudflare as their CDN

![image](https://user-images.githubusercontent.com/25869911/158474824-0769edef-3471-4760-908e-30e317b1b08c.png)

static content -> images, css, html and javascript

Type of CDN -> push or pull

pull cdn -> when user request content will be trigger the changes. ideally when you have a lot of static content or resources.
push cdn -> when the dev update image this will be pushed to the CDN -> this is only if there are few  resources. if there are a lot of resources this will be very expensive.

### Summary

* CDNs allow placing your static assets close to the users
* Reduces costs
* Decreases latency
* Increses complexity of your system

## Caching

over CPUs have cache, browser has a cache, databases have caches. caches are everywhere.

the goal of cache is to speed up getting some data from a slower storage. cache is in memory.
but your browser stores data on disk as cache. so it won't need to access to network.
you can use cache with database, if founded in cache it not need to hit to the database.


### pros and cons

pros : 

* improve read performance(aka latency)
* reduce the load(aka throughput)


cons:

* Increses complexity
* consumes resources (memory of servers, or additional servers ifit is distrubuted caches)
* introduces some incosistencies


### caching strategies

* cache aside
  * most common - it checks the key in cache, and if it si not there, hte apllication fetches it from the storage and then update the cache.
  * common pattern for using external cache such as Redis.
  * pros : 
    * cache only what is needed(data)
  * cons :
    * cache misses are expensive
    * data staleness
    * implementation complexity (work with both the cache and the storage using two seprate APIs and two separate libraries)
    
![image](https://user-images.githubusercontent.com/25869911/158477223-a09a4fed-0712-4bc0-9b19-8a06278f8bad.png)


* read through
  * where application does not have direct access to the storage, but instead always interacts with the cache API.
  * In case of cache miss, this API layer will fetch the results from storage, update them i cache and then return them to the application
  * this pattern is quite common in ORM(Object relational mapping) frameworks, the data would be usually stored in memory
  * pros : 
    * cache only what is needed
    * work with a single API
    * transparent (you are not even aware that there is a cache)
  * cons :
    * cache misses are expensive
    * data staleness
    * reliability - the probability that a product, system, or service will perform its intended function adequately for a specified period of time, or will operate in a defined environment without failure

![image](https://user-images.githubusercontent.com/25869911/158479383-50f308ad-838f-4a03-8071-0a55e27303e4.png)


* write through
  * somewhat similar to read through, application interacts with the API, that for each update also stores data in cache.
  * pros :
    * data in cache is never stale(obsolet). up-to-date data. 
  * cons : 
    * writes are more expensive.  
    * redundant data (there is a chance that we will write data to cache that no one ever reads) - Data redundancy occurs when the same piece of data exists in multiple places, whereas data inconsistency is when the same data exists in different formats in multiple tables.

![image](https://user-images.githubusercontent.com/25869911/158480349-99c26b47-f3f3-4ccc-b4c6-9761b4f4aa29.png)


* write behind
  * very similar to write through. the only difference is that the data is not written immediately. instead, the cache will wait for more events, or for a timeout and only then flush everything to storage.
  * cache act as a buffer
  * pros : 
    * no write penalty - write seems very fast. because we don't write to the slow storage every time
    * reduced load on storage - update it in bulk.
  * cons :
    * reliability - if the cache crashes, we lose some of the updates.
    * lack of consistency - if we don't flush the data from buffer often enough, it may create inconsistencies in our data.

![image](https://user-images.githubusercontent.com/25869911/158481234-593a2cdb-19fe-4675-9f45-12b56c60ecfa.png)

### Eviction Policies

Eventually, our cache becomes too large(many keys or to much memory).
policies help us decide which of the elements we should remove.

LRU - Least recentrly used - 

* based on a linked list,  where they had points to the next item to be removed.
* when there is a cache hit, the item moves to the tail of the list
* most recentrly used become the tail of the list
* if it's a cache miss, the new lement is added to the tail of the list, and pushes one element out.
* very efficient algorithm (doensn't require much memory or CPU resources, but it suffers from something called false cache eviction)
* False Cache Eviction - If a lot of new keys are requested at once, it may evict some popular keys.

![image](https://user-images.githubusercontent.com/25869911/158483867-d6c0cb77-8109-4e92-86de-dc686243047e.png)

![image](https://user-images.githubusercontent.com/25869911/158483940-aec989f0-72ed-4ca4-9981-f35b34a4f960.png)

![image](https://user-images.githubusercontent.com/25869911/158484114-580806da-9a73-4725-9ad7-809e404538b7.png)


LFU - Least frequently used

* LFU solve false cache eviction
* more complicated than LRU
* every key will have a counter which is increment every once in a while.
* when there is a cache hit, the counter of the key is reset.
* if we look for joker, we reset the counter on that entry. but other entries keep increasing
* when there is a cache miss, algorithm will take the key with the largest counter value and evict it.
* elements that are accessed often stay in the cache forever.
* but it has an overhead of keeping track of the counters for each entry.

![image](https://user-images.githubusercontent.com/25869911/158484528-fc9fcc0e-91f2-4286-b136-bb53f7fb65ca.png)

![image](https://user-images.githubusercontent.com/25869911/158484566-18a53673-cabf-4f9d-8326-cd7cd23d38d3.png)

![image](https://user-images.githubusercontent.com/25869911/158484778-fa361cde-d6b9-4c1e-8941-c2d14ec17a07.png)

... etc

https://medium.datadriveninvestor.com/all-things-caching-use-cases-benefits-strategies-choosing-a-caching-technology-exploring-fa6c1f2e93aa
https://en.wikipedia.org/wiki/Cache_replacement_policies

### Summary

* most caches use LRU as the default
* LRU is faster and cheaper in terms on memory than LFU
* LFU suffers less from false cache eviction

### Redis

Using redis as distributed cache. 


Redis

* In-memory, key-value store, limited by ram(Biggest servers are around 500 gb ram at the moment)
* supports 100,000 requests per second on a single node

key-value means acts similiar to a hash map or a dictionary.

![image](https://user-images.githubusercontent.com/25869911/158485538-ca90585e-12cb-4046-9b2f-728b567806ff.png)

key is string
value can be string, list, set, hash and some others

data structures only can contains string.

you cannot operate directly on nested structures in Redis, like you could read MongoDB.

Redis keys also support TTL(Time to Live) - when you put a key in your Redis, you also tell it for how long it should be stored, in seconds.

![image](https://user-images.githubusercontent.com/25869911/158485907-ab488393-a1a0-478b-b816-89a77586cb9d.png)


TTL is help we avoid that the redis is going to quickly run out of memory


Redis supports persistence, it can save data to the disk. this is important , because when redis instance crashes if everything was stored in memory, it will task some time to repopulate itself. and during that time our application will be slow. we can avoid this delay recovering our cache from the disk.

![image](https://user-images.githubusercontent.com/25869911/158486460-9138eafb-4177-4012-a8f1-76d7456aca01.png)


If redis is both fast and can persist data, why use it only as cache ?
in order to be performant, it persists data only every second.
so if it crashes, last second of data may be lost. example thousands of data is lost.

![image](https://user-images.githubusercontent.com/25869911/158486678-1158046d-a8ba-4563-843e-84347ad45cbb.png)

some system can afford that(lost of thousands of data lost), it is always a trandoff.

### summary

* Redis is the most popular cache solution at the moment (key-value sotres)
* Extremely fast(high throughput)
* Limited by Ram
* Stores data to disk, but can loose recent data.
* if you need to store hundreds of gigabytes in cache, you will need more than one instance of redis.


The tradeoffs of using Redis is that it is usually a remote service, so there is some network latency and also added complexity.

you need to think about what should you do if you cannot connect the Redis ?
Does your application crashes ? 
can you work the database or another service directly in the meantime ?

## Queue

Queue or Message Queue

![image](https://user-images.githubusercontent.com/25869911/160303660-a13a2c18-ae08-4bfa-8d29-24f6276c5bb7.png)

synch app problem -> if one process is having a lot of request and the service down, the entire process need to wait to continue.

asynchronous messaging

queus are used to develiver messages asynchronously.
this decouple the services
when queue is used, the service there is 2 role. producer and consumer

the services can be written in different languages but can connected.
producer and consumer are independent, just concerning there roles.

queue is acted as buffer. 

when consumer crashes, still data is not lost. data is remaining in the queue.

synchronous - you know always what happens to the payment service, like insufficient fond message
asyncchronous - you don't know what happens. this add complexities.

![image](https://user-images.githubusercontent.com/25869911/160304007-a15ebc62-972f-48e9-97e5-3c88ee3e0590.png)

examples : 
* apache kafka
* rabbitMQ
* amazon SQS
* apache activeMQ

### Summary

* Pros
  * Buffering
  * Request spikes smoothing
  * Message durability
* Cons
  * Increase system complexity
  * Increases latency

### Messaging Paradigms

Messaging models :

* Message queue
* Publisher/Subscriber( aka Pub/Sub)

* Message Queueing
  * if we want for a message to arrive to only a single instance of a service, we would use a message queue.
  * The more consumers we add, the more messages we are able to process simultaneously
  * even if one instance of consumer(service) is down, will chose another instance. 

![image](https://user-images.githubusercontent.com/25869911/160304334-c73881ab-1999-4432-ab6c-035b215b0b5e.png)

![image](https://user-images.githubusercontent.com/25869911/160304336-f82d6b30-7c2b-49b3-9782-32674f622bd9.png)


* Pub/Sub
  * is used in cases when you want to notify multiple other services about an event that happened.
  * paytment services need to notify payment successful to receipts and billing services
  * since there is no retry mechanism in that mode messages arrive to the consumer in the same order they are published 

![image](https://user-images.githubusercontent.com/25869911/160304862-8476626c-3335-4b84-8335-4dc029ce7e1f.png)


### Summary

* Message Queueing
  * Action
  * Exactly once delivery
  * Messages can arrive out of order(but due to retry mechanism, some older messagers may arrive out of order)
* Cons
  * Notification
  * At least once delivery
  * Messages are always in order(there are not retries)

### RabbitMQ

rabbitMq - message brokers

* based on AMQP protocol(ALSO ActiveMQ from apache use this protocol)
* (default) messages are store until consumer retrieves them(message read it and deleted) (this behavior can be changed)
* is often used to schedule some heavy tasks in the background. (Offloads heavy tasks)
  * examples: payment processing, resizing of images, running reports, and so on.
* Distrubtes tasks(used to distrbute load between multiple servers where each gets some work to do)

* Routing keys - every rabbitMq message has a key and it represents the address of the queue it needs to go to.
  * you can use semicolons to add more details like order, amount of payment, country

![image](https://user-images.githubusercontent.com/25869911/160304870-327a3a0b-d7c4-46c1-bd0a-1fd146efaef7.png)


