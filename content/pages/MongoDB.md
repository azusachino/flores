---
title: MongoDB
created: 2022-12-13 18:17:42
modified: 2023-05-30 11:19:47
tags: [CS, DB]
---

## General Commands

### Find

```s
# 关键字 + 操作符 匹配
db.movies.find({"title": {"$in": ["a", "b"]}})

# 字段匹配
db.c.find({"y": null})

# 正则表达式
db.users.find({"name": {"$regex": /joe/i }})

# 多个元素匹配数组
db.food.find({"fruit": {"$all": ["apple", "banana"]}})

# 指定偏移量和返回元素数量 (startIndex, pageSize)
db.blog.posts.findOne(criteria, {"comments" : {"$slice" : [23, 10]}})
```

## Scripts

```yaml
version: "3.6"

services:
  mongodb:
    image: mongo:latest
    container_name: mongodb
    restart: always
    ports:
      - "27017:27017"
    volumes:
      - ./data:/data/db
```

## References

- [MongoDB 权威指南](https://weread.qq.com/web/reader/75432b50727d8270754e35ck6ea321b021d6ea9ab1ba605)
