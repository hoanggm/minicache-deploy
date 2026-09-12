1. Deploy

```bash 
cd docker/
```

```bash 
docker compose -p minicache-service up -d
```

2. Commands

```bash 
PING
```

```bash 
KEYS
```

```bash 
CLEAR
```

```bash 
EXIT
```

---

```bash 
GET <key>
```

```bash 
EXISTS <key>
```

```bash 
SET <key> <value> <not_exists: 0|1> <time_to_live>
```

```bash 
DEL <key>
```

---

```bash 
BF.INIT <key> <expected_keys_count> <false_positive_rate>
```

```bash 
BF.ADD <key> <value>
```

```bash 
BF.EXISTS <key> <value>
```

```bash 
BF.RM <key>
```

```bash 
BF.RS <key>
```

---

```bash 
Z.ADD <key> <score> <member> <value>
```

```bash 
Z.SCR <key> <member>
```

```bash 
Z.RANK <key> <member>
```

```bash 
Z.RANGE <key> <start> <stop>
```

```bash 
Z.RSCR <key> <minScore> <maxScore>
```

```bash 
Z.POS <key> <position>
```

```bash 
Z.INCR <key> <member> <increment>
```

```bash 
Z.TOP <key> <top>
```

```bash 
Z.RM <key> <member>
```

```bash 
Z.DEL <key>
```

---

```bash 
GEO.ADD <key> <member> <lat> <lon>
```

```bash 
GEO.SEARCH <key> <lat> <lon> <radius_meters> <limit>
```

```bash 
GEO.DIST <key> <member1> <member2>
```

```bash 
GEO.DEL <key>
```

```bash 
GEO.RM <key> <member>
```

```bash 
GEO.GET <key> <member>
```

```bash 
GEO.NB <key> <member>
```

```bash 
GEO.EXISTS <key> <member>
```

```bash 
GEO.ENCODE <key> <member>
```

---

```bash 
H.SET <key> <field> <value>
```

```bash 
H.GET <key> <field>
```

```bash 
H.ALL <key>
```

```bash 
H.RM <key> <field>
```

```bash 
H.DEL <key>
```

---

```bash 
FZ.ADD <key> "<word>" <frequency>
```

```bash 
FZ.SEARCH <key> "<query>" <top>
```

```bash 
FZ.SUGGEST <key> "<query>" <top> <maxEditDist>
```

```bash 
FZ.DEL <key>
```

```bash 
FZ.RM <key> "<word>"
```

```bash 
FZ.EXACT <key> "<word>"
```

```bash 
FZ.EXISTS <key> "<word>"
```

```bash 
FZ.INCR <key> "<word>" <increment>
```

```bash 
FZ.PHONETIC <key> "<input>" <limit>
```

```bash 
FZ.RANDOM <key> <count>
```

3. Integration

- Spring Boot Integration

```java
<dependency>
    <groupId>io.github.hoanggm</groupId>
    <artifactId>minicache-client</artifactId>
    <version>1.1.1</version>
</dependency>
```

```java
minicache.cluster=127.0.0.1:8000,127.0.0.1:8001,127.0.0.1:8002
minicache.connections.core-pool-size=200
minicache.connections.max-pool-size=300
minicache.strict-mode=false
minicache.queuing-time=3000
minicache.client.connect-timeout=1000
minicache.client.read-timeout=1500
minicache.client.buffer-size=2048
```

```java
@Configuration
public class MiniCacheConfig {
    @Value("${minicache.cluster}")
    private String cluster;

    @Value("${minicache.connections.core-pool-size}")
    private Integer corePoolSize;

    @Value("${minicache.connections.max-pool-size}")
    private Integer maxPoolSize;

    @Value("${minicache.strict-mode}")
    private Boolean isStrictMode;

    @Value("${minicache.queuing-time}")
    private Integer queuingTime;

    @Value("${minicache.client.connect-timeout}")
    private Integer clientConnectTimeout;

    @Value("${minicache.client.read-timeout}")
    private Integer clientReadTimeout;

    @Value("${minicache.client.buffer-size}")
    private Integer clientBufferSize;

    public MiniCacheConfig() {
    }

    @Bean
    public IntegrationService miniCacheService() {
        MiniCacheCluster miniCacheClusterClient = new MiniCacheCluster(
                this.cluster,
                this.corePoolSize,
                this.maxPoolSize,
                this.isStrictMode,
                this.queuingTime,
                this.clientConnectTimeout,
                this.clientReadTimeout,
                this.clientBufferSize
        );
        return new IntegrationService(miniCacheClusterClient, new SingleFlightCollapsingHandler());
    }
}
```

- Quarkus Integration

```java
<dependency>
    <groupId>io.github.hoanggm</groupId>
    <artifactId>minicache-client</artifactId>
    <version>1.1.1</version>
</dependency>
```

```java
minicache.cluster=127.0.0.1:8000,127.0.0.1:8001,127.0.0.1:8002
minicache.connections.core-pool-size=200
minicache.connections.max-pool-size=300
minicache.strict-mode=false
minicache.queuing-time=3000
minicache.client.connect-timeout=1000
minicache.client.read-timeout=1500
minicache.client.buffer-size=2048
```

```java
@ApplicationScoped
public class MiniCacheConfig {
    @ConfigProperty(name = "minicache.cluster")
    String cluster;

    @ConfigProperty(name = "minicache.connections.core-pool-size")
    Integer corePoolSize;

    @ConfigProperty(name = "minicache.connections.max-pool-size")
    Integer maxPoolSize;

    @ConfigProperty(name = "minicache.strict-mode")
    Boolean isStrictMode;

    @ConfigProperty(name = "minicache.queuing-time")
    Integer queuingTime;

    @ConfigProperty(name = "minicache.client.connect-timeout")
    Integer clientConnectTimeout;

    @ConfigProperty(name = "minicache.client.read-timeout")
    Integer clientReadTimeout;

    @ConfigProperty(name = "minicache.client.buffer-size")
    Integer clientBufferSize;

    public MiniCacheConfig() {
    }

    @Produces
    @ApplicationScoped
    public IntegrationService miniCacheService() {
        MiniCacheCluster miniCacheClusterClient = new MiniCacheCluster(
                this.cluster,
                this.corePoolSize,
                this.maxPoolSize,
                this.isStrictMode,
                this.queuingTime,
                this.clientConnectTimeout,
                this.clientReadTimeout,
                this.clientBufferSize
        );
        return new IntegrationService(miniCacheClusterClient, new SingleFlightCollapsingHandler());
    }
}
```
