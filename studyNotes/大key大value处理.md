
1. 什么是大key
大key通常指的是key的value占用内存过大，也就是大value以及key过多的问题。
对于单个String类型来说，超过1MB的value。
对于hash，set，zSet list，存储过多元素，通常超过1w个。
一个集群中，存储了上亿的key。
2. 大key的危害
大key在读取时，可能会阻塞redis的输出缓冲区，最终导致服务超时。
大key可能导致节点之间的数据倾斜问题，有的服务数据存储压力大，有的节点较空闲。
大key如果是热点key，还会导致网卡流量不足。
3. 如何处理
对于String类型：
	每次都需要整存整取，可以将key拆分多个key，在使用multiGet命令，将操作压力分担给多个redis实例，降低单个redis实例的io，cpu压力。
	对于部分存取：将key拆分为多个key，存放到hash中，使用hget，hmget来获取部分值，使用hset，hmset来设置部分值。
对于集合类型：
	类似String的解决办法，或者是固定桶的数量，对hash取模放入桶中。
对于布隆过滤器：
	布隆过滤器通常数据量在512MB绝对是大key，需要将拆分后bitmap作为独立的bitmap，否则可能导致我们key需要多次hash访问。通过自定义hash算法将key固定到不同的bitmap。
	布隆过滤器的误判率是哈希函数个数k，集合元素个数n，以及Bitmap大小m所决定的，其约等于
	![](Pasted%20image%2020240816163639.png)
	因此如果我们在第一步，也就是在分配key给不同Bitmap时，能够尽可能均匀的拆分，那么n／m的值几乎是一样的，误判率也就不会改变。
key过多：
	危害：key本身占用大量内存，集群会有slot2Key的映射关系，key越多保存的指针越多。
	key本身有很强的相关性：
		举例 user.id = xxx,user-id-name = xxx,user-id-catergory = xxx
		可以改变数据类型，使用hash来保存user的所有属性。
	key本身没有很强的相关性（非常多的user)：
		预估总数，预估固定桶的数量，例如2e的key，一个hash保存100个，可以减少为200w个桶。注意对负数的处理，分桶时尽量对hash的值数量不超过512，尽量100
		