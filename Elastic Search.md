# Elastic Search

## RestHighLevelClient

pom.xml依赖：

导入elasticsearch和rest-high-level-client

```xml
<dependency>
    <groupId>org.elasticsearch</groupId>
    <artifactId>elasticsearch</artifactId>
    <version>6.6.1</version>
    <exclusions>
        <exclusion>
            <artifactId>jackson-core</artifactId>
            <groupId>com.fasterxml.jackson.core</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>6.6.1</version>
    <exclusions>
        <exclusion>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



配置yaml：

```yaml
server:
  port: 8080
spring:
  elasticsearch:
    rest:
      uris: http://localhost:9200
```

或者

```yaml
# Elasticsearch相关的配置
elasticsearch:
  clientIps: 127.0.0.1 # ES网关地址，实际地址在ES实例概览中查看，注意至少配置两个网关地址
  httpPort: 8990  # ES网关端口
  username: test
  password: 1234
esParam:
  IndexName: testName # 索引名
  indexType: "type" # 索引type，有可能是type或者doc
  bulkSize: 500 # 一次bulk请求文档个数
  docNum: 200000000 # 希望导入的数据总量
```

