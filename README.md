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
  * this pattern is quite common in ORM(Object relation mapping)


* write through

* write behind














