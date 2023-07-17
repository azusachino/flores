---
title: Etcd
created: 2022-11-16 19:05:03
modified: 2022-12-05 14:05:28
tags: [CS, Consensus]
---

etcd is a distributed reliable key-value store for the most critical data of a distributed system

## 简单使用

etcd 的使用其实非常简单，它对外提供了 gRPC 接口，我们可以通过 Protobuf 和 gRPC 直接对 etcd 中存储的数据进行管理，也可以使用官方提供的 etcdctl 操作存储的数据。

```protobuf
service KV {
  rpc Range(RangeRequest) returns (RangeResponse) {
      option (google.api.http) = {
        post: "/v3beta/kv/range"
        body: "*"
    };
  }

  rpc Put(PutRequest) returns (PutResponse) {
      option (google.api.http) = {
        post: "/v3beta/kv/put"
        body: "*"
    };
  }
}
```

### List/Watch

```go
cli, err := clientv3.New(clientv3.Config{
    Endpoints:   []string{"127.0.0.1:2379"},
    Context:     ctx,
    DialTimeout: 3 * time.Second,
    DialOptions: []grpc.DialOption{},
})

// handle error
ch := cli.Watch(ctx, "/some/keyspace", clientv3.WithPrefix())
for resp := range ch {
    for _, event = range resp.Events {
        switch event.Type {
        case mvccpb.PUT:
            // process with put event
        case mvccpb.DELETE:
            // process with delete event
        }
    }
}
```

## Installation

## References

- [高可用分布式存储 etcd 的实现原理](https://draveness.me/etcd-introduction/)
- [etcd](https://etcd.io)
