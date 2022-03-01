# Definition

A load balancer is a device that acts as a reverse proxy and distributes network or application traffic across a number of servers. It helps scale horizontally across an ever-increasing number of servers.

# How does the load balancer work?

- **Define IP or DNS name for LB**: Administrators define one IP address and/or DNS name for a given application, task, or website, to which all requests will come. This IP address or DNS name is the load balancing server.
- **Add backend pool for LB**: The administrator will then enter into the load balancing server the IP addresses of all the actual servers that will be sharing the workload for a given application or task. This pool of available servers is only accessible internally, via the load balancer.
- **Deploy LB**: Finally, your load balancer needs to be deployed — either as a proxy, which sits between your app servers and your users worldwide and accepts all traffic, or as a gateway, which assigns a user to a server once and leaves the interaction alone thereafter.
- **Redirect requests**: Once the load balancing system is in place, all requests to the application come to the load balancer and are redirected according to the administrator’s preferred algorithm.

# Types of LBs

## Layer 4 load balancers

- It acts upon data found in network and transport layer protocols (IP, TCP, FTP, UDP).
- They are mostly the network address translators (NATs) which share the load to the different servers getting translated to by these load balancers.
- Session persistence can be achieved at the IP address level
- No termination for TCP connections

### Two modes of L4 LB:

1. _Direct Server Return (DSR)_: The TCP connection is established directly between the client and the backend. The load-balancer sees only the requests and just changes the destination MAC address of the packets. The backends answer directly to the client using the service IP (VIP) configured on a loopback interface (this is why the src is VIP for response). Note that the requests pass through the load balancer while the responses not. LB won’t be the bandwidth bottleneck.
2. _NAT Mode_: The clients get connected to the service VIP. The load balancer chooses a server in the pool then forwards packets to it by changing the destination IP address (DNAT), the LB becomes the default gateway for the real servers, and the source IP is the client’s IP. All traffic will pass through the load balancer, and output bandwidth is limited by load balancer output capacity. There is only one connection established.

## Layer 7 load balancers

- It distributes requests based upon data found in application layer protocols such as HTTP. They can further distribute requests based on application-specific data such as HTTP headers, cookies, or data within the application message itself, such as the value of a specific parameter.
- For the client, the destination IP will be the IP of the load balancer, for the backend server, the source IP will be the IP of the load balancer.
- The cookie can be used to achieve a sticky session.
- IP of the client will be kept with the X-Forwarded-For header.
- To get the HTTP information, the connection is - terminated at the load balancer, thus, there will be 2 TCP connections: **Client-LB**, **LB-Backend**.
- Example: Azure Application Gateway, Nginx as HTTP load balancer

## LB Locations

- Between user and web servers (User => Web Servers)
- Between web servers and an internal platform layer (application servers, cache servers) (Webservers => App or Cache servers)
- Between internal platform layer and database (App or Cache servers => Database servers)

## Algorithms

- _Round Robin_ — When load balancer receives a request, it assigns the request to the first server in the list and then moves that server to the bottom of the list.
- _Weighted Round Robin Method_ — The weighted round-robin scheduling is designed to handle servers with different processing capacities. Each server is assigned a weight (an integer value that indicates the processing capacity). Servers with higher weights receive new connections before those with less weights and servers with higher weights get more connections than those with less weights.
- _Least Connections_ — A new request is sent to the servers with fewest current connections to the clients. The relative computing capacity for each server is factored into determining which one has the least connections.
- _Least Response Time_ — Directs the traffic to the server with fewest active connections and lowest average response time.
- _Least Bandwidth Method_ — Server that is currently serving the least amount of traffic, measured in megabits per second (Mbps)
- _IP Hash_ — The IP of the clients is used to determine which server receives the request.

## Load balancer features:

- If a single server goes down, the load balancer removes that server from server group and redirects traffic to the remaining online servers
  When a new server is added to the server group, the load balancer automatically starts to send requests to it.
- Distributes clients request or network load efficiently across multiple servers.
- Ensure high availability and reliability by sending request only to the servers that are online.
- Provides the flexibility to add or subtract servers as demand dictates.
- Can be added at multiple layers ( application server, databases, caches etc) in the application stack
- Can dynamically add or remove servers from the group without interrupting existing connections.
  W- hile routing requests to multiple backend servers load balancer choose the server based on two factors. First, ensure that the server they choose is responding and then choose one server based on a pre-configured algorithm from the set of healthy servers.

**Health Checks**: To make sure load balancer forward traffic to only healthy servers, “health checks” regularly attempt to connect to backend servers to ensure that servers are listening. If a server fails a health check, it is automatically removed from the pool, and traffic will not be forwarded to until it responds to the health checks again.

**Session persistence or Sticky Session**: Session persistence or sticky session is a feature of some of the commercial load balancer where they route all the requests for a particular session to the same physical machine that serviced the first request for that session to improve performance.

For example, when upstream servers stores information requested by a user in its cache to boost performance, switching server would cause that information to be fetched for the second time creating performance inefficiency which can be removed by using Session persistence or Sticky Session.

## Implementations

### Smart clients

- Developers lean towards smart clients because they are developers, and so they are used to writing software to solve their problems, and smart clients are software.
  -The smart client is a client that takes a pool of service hosts and balances load across them, detects downed hosts, and avoids sending requests their way (they also have to detect recovered hosts, deal with adding new hosts, etc, making them fun to get working decently and a terror to setup).

### Hardware load balancers

- Hardware load balancers are specialized hardware deployed in-between the server and the client. It can be a switching/routing hardware or even a dedicated system running a load balancing software with specialized capabilities.
- Difficult to scale as we need to add more hardware as load increases
- Very expensive because of the high cost of purchase and maintenance of physical load balancer. Also, need a specialized consultant for maintenance and installation.
- Increased security due to physical hardware
- The hardware load balancers are implemented on Layer4 and Layer7 of the OSI model so prominent among this hardware are L4-L7 routers.
  Hardware Load Balancer.

### Software load balancers

- Software load balancers generally implement a combination of one or more scheduling algorithms. They are often installed on the servers and consume the processor and memory of the servers.
- Less expensive as runs on commodity hardware
- Easy to scale by simply running additional instances of the load balancer
- Flexibility to adjust based on changing needs
- Can run anywhere
- Some smart software-based load balancer provides predictive analytics that determines traffic bottlenecks.
- The following are a few examples of software load balancers:
  1. HAProxy — A TCP load balancer.
  2. NGINX — An HTTP load balancer with SSL termination support.

### Redundant Load Balancers

- The load balancer can itself become a single point of failure; to overcome this, a second load balancer can be connected to the first to form a cluster.
- Each LB monitors the health of the other and, since both of them are equally capable of serving traffic and failure detection, in the event the main load balancer fails, the second load balancer takes over.
- Load balancers distribute incoming client requests to computing resources such as application servers and databases. In each case, the load balancer returns the response from the computing resource to the appropriate client.
- Load balancers can be implemented with hardware (expensive) or with software such as HAProxy.
- To protect against failures, it’s common to set up multiple load balancers, either in active-passive or active-active mode.

## Load Balancer Cons:

- The load balancer can become a performance bottleneck if it does not have enough resources or if it is not configured properly.
- Introducing a load balancer to help eliminate a single point of failure results in increased complexity.
- A single load balancer is a single point of failure, configuring multiple load balancers further increases complexity.

### Horizontal scaling

- Load balancers can also help with horizontal scaling, improving performance and availability.
- Scaling out using commodity machines is more cost efficient and results in higher availability than scaling up a single server on more expensive hardware, called Vertical Scaling.
- It is also easier to hire for talent working on commodity hardware than it is for specialized enterprise systems.
- Another useful function provided by some load balancers is session persistence, which means sending all requests from a particular client to the same server.
- Even though HTTP is stateless in theory, many applications must store state information just to provide their core functionality — think of the shopping basket on an e-commerce site. Such applications under-perform or can even fail in a load-balanced environment, if the load balancer distributes requests in a user session to different servers instead of directing them all to the server that responded to the initial request.

### Disadvantages of Horizontal Scaling:

- Scaling horizontally introduces complexity and involves cloning servers
- Servers should be stateless: they should not contain any user-related data like sessions or profile pictures
- Sessions can be stored in a centralized data store such as a database (SQL, NoSQL) or a persistent cache (Redis, Memcached)
- Downstream servers such as caches and databases need to handle more simultaneous connections as upstream servers scale out

## Reverse proxy (web server)

- A reverse proxy is a web server that centralizes internal services and provides unified interfaces to the public. Requests from clients are forwarded to a server that can fulfill it before the reverse proxy returns the server’s response to the client.

### Pros:

- Increased security — Hide information about backend servers, blacklist IPs, limit number of connections per client
- Increased scalability and flexibility — Clients only see the reverse proxy’s IP, allowing you to scale servers or change their configuration
- SSL termination — Decrypt incoming requests and encrypt server responses so backend servers do not have to perform these potentially expensive operations
- Removes the need to install X.509 certificates on each server
- Compression — Compress server responses
- Caching — Return the response for cached requests
- Static content — Serve static content directly

### Cons:

- Introducing a reverse proxy results in increased complexity.
- A single reverse proxy is a single point of failure, configuring multiple reverse proxies (ie a failover) further increases complexity.

## Load Balancer vs Reverse Proxy

- Deploying a load balancer is useful when you have multiple servers. Often, load balancers route traffic to a set of servers serving the same function.
- Reverse proxies can be useful even with just one web server or application server, opening up the benefits described in the previous section.
- Solutions such as NGINX and HAProxy can support both layer 7 reverse proxying and load balancing.
