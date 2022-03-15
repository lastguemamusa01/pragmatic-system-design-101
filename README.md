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







