# discovery.etcd.io

This code powers the public service at https://discovery.etcd.io. The API is
documented in the etcd clustering documentation:

https://github.com/coreos/etcd/blob/master/Documentation/clustering.md#public-etcd-discovery-service

## Docker Container

You may run the service in a docker container.

### 1. Run etcd service (optional)

Etcd service is required by discovery service. If you already has etcd service runing, please skip this step.

```
# set host ip for etcd, replace your own host IP before run it.
export HostIP="x.x.x.x"

# run ectcd daemon in docker
docker pull quay.io/coreos/etcd:v2.1.3 
docker run -d -v /usr/share/ca-certificates/:/etc/ssl/certs -p 4001:4001 -p 2380:2380 -p 2379:2379 \
 --name etcd quay.io/coreos/etcd:v2.1.3 \
 -name etcd0 \
 -advertise-client-urls http://${HostIP}:2379,http://${HostIP}:4001 \
 -listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
 -initial-advertise-peer-urls http://${HostIP}:2380 \
 -listen-peer-urls http://0.0.0.0:2380 \
 -initial-cluster-token etcd-cluster-1 \
 -initial-cluster etcd0=http://${HostIP}:2380 \
 -initial-cluster-state new

```

### 2. Run discovery.etcd.io service

```
docker pull quay.io/coreos/discovery.etcd.io
docker run --name discovery -d --net=host quay.io/coreos/discovery.etcd.io

```

### 3. Test

```
curl --verbose -X PUT localhost:8087/new

```

## Development

discovery.etcd.io uses devweb for easy development. It is simple to get started:

```
./devweb
curl --verbose -X PUT localhost:8087/new
```
