---
title: ProtoBuffer
created: 2022-09-21 07:19:29
modified: 2022-12-05 14:05:27
aliases: [protobuf]
tags: [CS, GRPC]
---

[[GRPC]] 的基石组件。

## Sample

```protobuf
message Person {
    optional string name = 1;
    optional int32 id = 2;
    optional string email = 3;
}
```

```java
Person john = Person.newBuilder()
                .setId(1234)    
                .setName("John Doe")    
                .setEmail("jdoe@example.com")    
                .build();
output = new FileOutputStream(args[0]);  
john.writeTo(output);
```

```java
Person john;
fstream input(argv[1],ios::in | ios::binary);  
john.ParseFromIstream(&input);
id = john.id();
name = john.name();
email = john.email();
```

## References

- [Google Doc](https://developers.google.com/protocol-buffers/)
