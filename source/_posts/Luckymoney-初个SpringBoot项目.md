---
title: Luckymoney--初个SpringBoot项目
date: 2019-08-01 21:55:45
tags: SpringBoot Web项目
---

# 课程

[《2小时快速上手SpringBoot》](https://coding.imooc.com/class/328.html) 

# 项目

该项目只涉及对数据库的一些基本操作，用于入门`SpringBoot`。

## 1. 创建项目

我是用的是使用 IDEA 的 自带的 `Spring Initializr`，也可以使用线上版本的 [Spring Initializr](https://start.spring.io/)。

## 2. Maven 配置

主要是添加 `Jpa`和`MySQL` 的依赖。

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
```



## 3. 配置 `application.yml`

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/luckymoney
    username: root
    password: 123456
  jpa:
    show-sql: true
    hibernate:
      ddl-auto: update

```

主要是配置数据库相关的内容

## 4. 创建实体类`Luckymoney`

用于创建数据库，以及数据库的表中所包含的字段。

以及提供一些对数据的基本操作。

### 字段

```java
		// 主键 ID
    private Integer Id;
    // 红包金额
    private Double money;
    // 红包发送者
    private String producer;
    // 红包接受者
    private String consumer;
```





```java
package com.sybapp.luckymoney;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
public class Luckymoney {
    @Id
    @GeneratedValue
    // 主键 ID
    private Integer Id;
    // 红包金额
    private Double money;
    // 红包发送者
    private String producer;
    // 红包接受者
    private String consumer;

    public Integer getId() {
        return Id;
    }

    public void setId(Integer id) {
        Id = id;
    }

    public Double getMoney() {
        return money;
    }

    public void setMoney(Double money) {
        this.money = money;
    }

    public String getProducer() {
        return producer;
    }

    public void setProducer(String producer) {
        this.producer = producer;
    }

    public String getConsumer() {
        return consumer;
    }

    public void setConsumer(String consumer) {
        this.consumer = consumer;
    }
}

```

## 5. 创建`LuckmoneyController`

`Controller` 主要是处理跟用户交互的，一般每个业务逻辑都会有一个 Controller，提供给用户 http 请求接口，用户请求接口进行数据访问。

既然要和用户交互，那么免不了要使用到数据库。

这里使用`Jpa`进行数据库的访问。

`LuckymoneyRepository.java`

```java
package com.sybapp.luckymoney;

import org.springframework.data.jpa.repository.JpaRepository;

public interface LuckymoneyRepository extends JpaRepository<Luckymoney, Integer> {
}

```

要使用`jpa`只需要继承一个`JpaRepository`类，并指明实体类和ID的类型即可。



```java
package com.sybapp.luckymoney;

import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Optional;

@RestController
public class LuckymoneyController {

    private final LuckymoneyRepository repository;

    public LuckymoneyController(LuckymoneyRepository repository) {
        this.repository = repository;
    }


    /**
     * 获取红包列表
     *
     * @return List
     */
    @GetMapping("/luckymoneys")
    public List<Luckymoney> getLuckymoneyInfo() {
        return repository.findAll();
    }

    /**
     * 创建红包
     *
     * @param producer
     * @param money
     * @return repository.save
     */
    @PostMapping("/createLuckymoney")
    public Luckymoney setLuckymoneyInfo(@RequestParam("producer") String producer,
                                        @RequestParam("money") Double money) {
        Luckymoney luckymoney = new Luckymoney();
        luckymoney.setProducer(producer);
        luckymoney.setMoney(money);
        return repository.save(luckymoney);
    }

    /**
     * 通过ID查询红包
     *
     * @param id
     * @return Luckymoney
     */
    @GetMapping("/luckymoney/{id}")
    public Luckymoney getLuckmoneyInfoById(@PathVariable("id") Integer id) {
        return repository.findById(id).orElse(null);
    }

    /**
     * 更新红包数据
     * @param id
     * @param consumer
     * @return
     */
    @GetMapping("/luckymoneys/{id}")
    public Luckymoney updateLuckmoneyInfo(@PathVariable("id") Integer id,
                                          @RequestParam("consumer") String consumer) {
//        Luckymoney luckymoney = getLuckmoneyInfoById(id);
//        if (luckymoney == null) {
//            return null;
//        }
//        luckymoney.setConsumer(consumer);
//        return repository.save(luckymoney);

        Optional<Luckymoney> optionalLuckymoney = repository.findById(id);
        if (optionalLuckymoney.isPresent()) {
            Luckymoney luckymoney = optionalLuckymoney.get();
            luckymoney.setConsumer(consumer);
            return repository.save(luckymoney);
        }

        return null;
    }
}

```



## 6. 主类

其实主类已经使用`Spring Initializr `生成了

```java
package com.sybapp.luckymoney;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class LuckymoneyApplication {

    public static void main(String[] args) {
        SpringApplication.run(LuckymoneyApplication.class, args);
    }

}

```

## 7. 运行测试

