---
title: "缓存穿透、缓存击穿、缓存雪崩"
date: 2020-09-20T20:11:33+08:00
draft: false
---

## 缓存穿透

如果缓存和数据库都没有查询的数据，那么每次都会去数据库查找，这种查询不存在的数据的现象称为缓存穿透

### 缓存穿透带来的问题

如果每次都拿一个不存在的id去查询数据库，可能会导致数据库压力增大，数据库有可能会挂

### 解决办法

- 缓存空值
    - 之所以会发生穿透，是因为缓存中没有存储这些数据的key，从而每次都查询数据库
    - 我们可以为这些key在缓存中设置对应的值为null，后面查询这个key的时候就不用查询数据库了
    - 当然为了健壮性，还要对这些key设置过期时间，以防止数据库中真的有数据

---

## 缓存击穿

在高并发下，大量的请求同时查询同一个key时，此时这个key刚好失效，就会导致同一时间内，这些请求都会去查询数据库

### 击穿带来的问题

会造成某一时刻数据库的请求量很大

### 解决办法

采用锁，只有拿到锁的第一个线程去请求数据库，然后插入缓存，当然每次拿到锁的时候都要去查询一下有没有缓存

---

## 缓存雪崩

当某一时刻发生大规模的缓存失效的情况，比如说缓存服务器宕机了

### 解决办法

- 采用集群，减少宕机的几率
- 限流和降级

---

## 解决热点数据集中失效的问题

我们在设置缓存的时候，一般会给缓存设置一个失效时间，过了这个时间，缓存就失效了

对于一些热点数据而言，当缓存失效以后会有大量的请求过来，然后打到数据库中，从而导致数据库崩溃的情况

### 解决办法

- 设置不同的失效时间
- 采用加锁机制
- 永不失效，就是采用定时任务对快要失效的缓存进行更新缓存和失效时间