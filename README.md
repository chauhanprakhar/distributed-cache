# distributed-cache

### Distributed cache
The simplest cache is the key-value pair cache stored in memory. Speaking of key-value pairs, it is easy to think of the dictionary (dict) type, which is called map in the Go language. 

### What should I do if I run out of memory?
Then just randomly delete a few pieces of data. How about deleting them randomly? Or is it better to go in chronological order? Or are there other better elimination strategies? The access frequency of different data is different. Is it better to delete data with low access frequency first? The access frequency of data may change over time, so it may be a better choice to prioritize deleting the least recently accessed data. We need to implement a reasonable elimination strategy.

### 1.2 What should I do if there is a concurrent write conflict?
Access to the cache is generally impossible to be serial. Map does not have concurrency protection. To deal with concurrency scenarios, modification operations (including addition, update and deletion) need to be locked.

### 1.3 What should I do if the stand-alone performance is not enough?
The resources of a single computer are limited, including computing, storage, etc. As business volume and access increase, a single machine can easily encounter bottlenecks. If the resources of multiple computers are utilized, parallel processing to improve performance requires that the cache application be able to support distribution, which is called scale horizontally. Corresponding to horizontal expansion is vertical expansion (scale vertically), which improves system performance by increasing the computing, storage, bandwidth, etc. of a single node. The cost and performance of hardware are not linearly related. In most cases, distributed system is a better choice.

## 2. FIFO/LFU/LRU

### 2.1 FIFO (First In First Out)
First in, first out, that is, the oldest (earliest added) record in the cache is eliminated. FIFO believes that the earliest added record is more likely to be no longer used than the record that was just added. The implementation of this algorithm is also very simple. Create a queue, add new records to the end of the queue, and eliminate the head of the queue every time the memory is insufficient. However, in many scenarios, some records are added earliest but are also accessed most often, and have to be eliminated because they stay too long. This type of data will be frequently added to the cache and then eliminated, resulting in a lower cache hit rate.

### 2.2 LFU (Least Frequently Used)
Least used, that is, eliminating the records with the lowest access frequency in the cache. LFU believes that if data has been accessed many times in the past, it will be accessed more frequently in the future. The implementation of LFU needs to maintain a queue sorted by the number of accesses. For each access, the number of accesses is increased by 1, and the queue is reordered. When eliminated, the one with the least number of accesses can be selected. The hit rate of the LFU algorithm is relatively high, but the shortcomings are also very obvious. Maintaining the number of accesses for each record consumes a lot of memory. In addition, if the access pattern of the data changes, LFU takes a long time to Adaptation, that is to say, the LFU algorithm is greatly affected by historical data. For example, a piece of data has an extremely high number of accesses in history, but is almost no longer accessed after a certain point in time. However, it cannot be eliminated because the number of historical accesses is too high.

### 2.3 LRU (Least Recently Used)
Least Recently Used, compared to FIFO, which only considers time factors, and LFU, which only considers access frequency, the LRU algorithm can be considered a relatively balanced elimination algorithm. LRU believes that if the data has been accessed recently, the probability of being accessed in the future will be higher. The implementation of the LRU algorithm is very simple. It maintains a queue. If a record is accessed, it is moved to the end of the queue. Then the head of the queue is the least recently accessed data, and the record is eliminated.



