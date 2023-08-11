## `CacheManager`란?

### 0. Cache란?
들어오는 값을 기억해두었다가 추후에 같은 값이 들어오게 되면 메소드를 돌지 않고 바로 실행해준다.    

  
`CacheManager`는 다양한 캐시의 솔루션들을 코드에 최소의 영향으로다 다양한 캐싱 솔루션을 도입할 수 있게 해준다.  
   
```java
    public interface CacheManager {

    /**
     * Get the cache associated with the given name.
     * <p>Note that the cache may be lazily created at runtime if the
     * native provider supports it.
     * @param name the cache identifier (must not be {@code null})
     * @return the associated cache, or {@code null} if such a cache
     * does not exist or could be not created
     */
    @Nullable
    Cache getCache(String name);

    /**
     * Get a collection of the cache names known by this manager.
     * @return the names of all caches known by the cache manager
     */
    Collection<String> getCacheNames();

}
```
`CacheManager`를 통해 원하는 캐시를 가져올 수 있다. 
   
<br/>

잠깐) `Caffeine cache`는?  
로컬 캐시로 해당 기기에서만 사용되는 캐시이다. 값을 넣으면 연산을 거치지 않고 바로 꺼내와서 쓰는 방식과 유사하다.  
말 그대로 로컬에서 돌아가기 때문에 네트워크를 탈 필요가 없어 속도가 빠르지만, 분산 시스템에서 데이터 정합성이 깨질 수 있다.  

<br/>.

### 1. 사전 설정
```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-cache</artifactId>
        <version>2.4.0</version>
    </dependency>
```

<br/>

### 2. 구현 방법

**CacheConfig**
```java
import java.util.ArrayList;

@Configuration
@EnableCaching
public class CacheConfig {
    private static final int DEFAULT_MAX_SIZE = 3000;
    private static final int DEFAULT_TTL = 00; // Time To Live

    @Bean
    public CacheManager cacheManager() {
        SimpleCacheManager manager = new SimpleCacheManager();
        ArrayList<CaffeineCache> caches = new ArrayList<CaffeineCache>();
        
        for(Cache c : Cache.values()){
            caches.add(new CaffeineCahe(...));
        }
        manager.setCaches(caches);
        return manager;
    }
    
    public enum Cache{
        private String name = "";
        private int maxSize =DEFAULT_MAX_SIZE;
        private int ttl = DEFAULT_TTL;
    }
}
```
  
**Service**

```java
    @Service
    public class CacheService{
        
        @Cacheable(cacheNames = ["name"])
        public void find~(){
        ...
        }
    
}
```
