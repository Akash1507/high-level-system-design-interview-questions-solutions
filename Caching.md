# Definition

- Cache is a small memory, fast access local store where we store frequently accessed data.
- Caching is the technique of storing copies of frequently used application data in a layer of smaller, faster memory in order to improve data retrieval times, throughput, and compute costs.

## Why do we use Cache?

- Cache is based on the principle of locality. It means that frequently accessed data is kept close to the system.
- The two kinds of locality are:

  - **Temporal locality** where data that has been referenced recently is likely to be referenced again (i.e. time-based locality). It repeatedly refers to same data in short time span.

  - **Spatial locality** where data that is stored near recently referenced data is also likely to be referenced again(i.e. space-based locality). It only refers to data item which are closed together in memory. eg. Data stored together in an array

## How does a Cache work?

- When a request comes to a system, there can be two scenarios. If a copy of the data exists in cache it’s called a cache hit, and when the data has to be fetched from the primary data store it’s called a cache miss.
- The performance of a cache is measured by the number of cache hits out of the total number of requests.

## Types of Cache

### Application server cache

- Placing a cache directly on an application server enables the local storage of response data.
- Each time a request is made to the service, the server will return local cached data if it exists.
- If it is not in the cache, the requesting node will query the data from primary store. The cache could be located both in memory (which is very fast) and on the node’s local disk (faster than going to network storage).
- The problem with this type of caching comes in when you have a distributed system. It leads to a lot of cache misses because each instance has its own cache which does not contain information on other instances.

### Distributed cache

- Each node will have a part of the whole cache space, and then using the consistent hashing function each request can be routed to where the cache request could be found.
- The cache is divided up using consistent hashing and each request can be routed to the node which contains the response for it.

### Global Cache

- A global cache is a single cache memory in front of the server instances.
- It is common for all instances and fetches data from the primary store in case it’s not present in the cache itself.
- It has added latency but has the advantage of considerably better cache performance.

### CDN

- CDN(Content Distribution Network) is used where a large amount of static content is served. The response can be HTML file, CSS file, JavaScript file, pictures, videos, etc. First, request ask the CDN for data, if it exists then the data will be returned. If not, the CDN will query the backend servers and then cache it locally.

## Cache Writing Policies

- A Cache Policy is a set of rules which define how the data will be loaded (and evicted) from a cache memory.
- A cache is made of copies of data, and is thus transient storage, so when writing we need to decide when to write to the cache and when to write to the primary data store.
- The most common cache writing policies are as follows:

### Write-through caching

- Under this scheme, data is written into the cache and the corresponding database at the same time. The cache ensures fast retrieval, and since data is written simultaneously in the cache and primary data store, there is complete consistency.
- However, since every write operation has to be done twice it accounts for more latency.

### Write-around caching

- In write around caching, data is written directly to primary data store. The cache in turn checks with primary data store to keep itself in sync.
- There is a downside here that cache might be behind the primary storage at times, but the added latency is countered by the primary data store always being consistent.

### Write-back caching

- In write back caching data is written first into the cache, and then into the primary data store. - There can be two scenarios here, either the primary storage is updated directly after the cache, or that the cache memory is persisted after an entry removal (in case the cache memory was already full).
- In this scenario the entry is tagged with a dirty bit to mark that the data is out of sync.
- Write back caching is prone to data loss and should only be used in write-heavy operations where write speed is very important.

### Cache Eviction Policies

- Cache eviction policies define a set of rules that decide what data must be removed when the cache is full and a new entry is to be added.
- A good replacement policy will ensure that the cached data is as relevant as possible to the application, that is, it utilises the principle of locality to optimise for cache hits.
- Therefore a cache eviction policy can only be defined on the basis of what data is stored in the cache.
- Some of the popular cache eviction algorithms are:

  - **First In First Out (FIFO):** This cache evicts the first entry in the cache regardless of how many times it was called

  - **Least Recently Used (LRU):** Evicts the least recently used items first.

  - **Most Recently Used (MRU):** Evicts the most recently used items first.

  - **Least Frequently Used (LFU):** Counts how often an entry was read from cache. Those that are used least often are discarded first.

  - **Random Replacement (RR):** Randomly selects a candidate item and discards it to make space when necessary.
