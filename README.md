# springboot-mybatis-encache
springboot与mybatis ehcahce集成
* springboot提供统一的框架
* mybatis提供ORM框架
* ehcache提供单机版本缓存

## encache配置
```
@Configuration
@EnableCaching
public class EnCacheConfig {
	@Bean
	public CacheManager cacheManager() {
		return new EhCacheCacheManager(ehCacheCacheManager().getObject());
	}

	@Bean
	public EhCacheManagerFactoryBean ehCacheCacheManager() {
		EhCacheManagerFactoryBean cmfb = new EhCacheManagerFactoryBean();
		cmfb.setConfigLocation(new ClassPathResource("conf/ehcache.xml"));
		cmfb.setShared(true);
		return cmfb;
	}
	
}
```

## 配置文件
```
<?xml version="1.0" encoding="gbk"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="ehcache.xsd">
    <diskStore path="java.io.tmpdir"/>
 
    <defaultCache maxElementsInMemory="10000" eternal="false" timeToIdleSeconds="30" timeToLiveSeconds="30" overflowToDisk="false"/>
    <!-- 
        配置自定义缓存
        maxElementsInMemory：缓存中允许创建的最大对象数
        eternal：缓存中对象是否为永久的，如果是，超时设置将被忽略，对象从不过期。
        timeToIdleSeconds：缓存数据的钝化时间，也就是在一个元素消亡之前，
                    两次访问时间的最大时间间隔值，这只能在元素不是永久驻留时有效，
                    如果该值是 0 就意味着元素可以停顿无穷长的时间。
        timeToLiveSeconds：缓存数据的生存时间，也就是一个元素从构建到消亡的最大时间间隔值，
                    这只能在元素不是永久驻留时有效，如果该值是0就意味着元素可以停顿无穷长的时间。
        overflowToDisk：内存不足时，是否启用磁盘缓存。
        memoryStoreEvictionPolicy：缓存满了之后的淘汰算法。
    -->
    <cache name="SimplePageCachingFilter" 
        maxElementsInMemory="10000" 
        eternal="false"
        overflowToDisk="false" 
        timeToIdleSeconds="900" 
        timeToLiveSeconds="1800"
        memoryStoreEvictionPolicy="LFU" />
        
      <cache name="userEhCache" 
        maxElementsInMemory="10000" 
        eternal="false"
        overflowToDisk="false" 
        timeToIdleSeconds="900" 
        timeToLiveSeconds="1800"
        memoryStoreEvictionPolicy="LFU" />
 
</ehcache>
```

## 测试
```
ab -c 10 -n 10000 192.168.1.236:8080/user/981
```

