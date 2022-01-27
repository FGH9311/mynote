# 无法访问redis.clients.jedis.JedisPoolConfig 找不到redis.clients.jedis.JedisPoolConfig的类文件





```java
 @Bean
    @Primary
    public JedisConnectionFactory jedisConnectionFactory(JedisPoolConfig jedisPoolConfig) {
        JedisClientConfiguration jedisClientConfiguration = JedisClientConfiguration.builder().usePooling()
                .poolConfig(jedisPoolConfig).and().readTimeout(Duration.ofMillis(timeout)).build();
        RedisStandaloneConfiguration redisStandaloneConfiguration = new RedisStandaloneConfiguration();
        redisStandaloneConfiguration.setHostName(host);
        redisStandaloneConfiguration.setPort(port);
        return new JedisConnectionFactory(redisStandaloneConfiguration, jedisClientConfiguration);
    }
```



出现 ：

```java
java: 无法访问redis.clients.jedis.JedisPoolConfig   找不到redis.clients.jedis.JedisPoolConfig的类文件
```





需要添加：

```java
 <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <type>jar</type>
        </dependency>

        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-redis</artifactId>
        </dependency>
```



之前已经添加springboot redis , 而且 包含 spring-data-redis (不起作用？)

```
         <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```