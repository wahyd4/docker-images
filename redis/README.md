This images is aim to help you create a 6 - node Redis cluster in minutes

## Build the Docker image by yourself
```bash
cd redis
docker build -t cluster-redis:latest .
```

## Create a Redis cluster on Kubernetes

1. Create a Redis Statefulset and a Service
```bash
kubectl create -f redis/redis.yml
```

 Now Take a look at the redis pods
```bash
kubectl get pods -o wide | grep redis
```

```bash
redis-0                            1/1       Running   0          9m        172.17.0.7    minikube
redis-1                            1/1       Running   0          9m        172.17.0.9    minikube
redis-2                            1/1       Running   0          9m        172.17.0.10   minikube
redis-3                            1/1       Running   0          9m        172.17.0.11   minikube
redis-4                            1/1       Running   0          9m        172.17.0.12   minikube
redis-5                            1/1       Running   0          9m        172.17.0.15   minikube
```

2. Access a Redis pod, pick anyone you like.
```bash
kubectl exec -it redis-0 bash
```

3. Go to Redis source code folder, and create the Redis cluster (You should type all the IPs of six pods)
```bash
cd /usr/src/redis/src/
./redis-trib.rb create --replicas 1 172.17.0.11:6379 172.17.0.12:6379 172.17.0.13:6379 \
172.17.0.14:6379 172.17.0.15:6379 172.17.0.16:6379
```
4. Type "yes" when you saw this

```bash
>>> Creating cluster
>>> Performing hash slots allocation on 6 nodes...
Using 3 masters:
10.244.1.131:6379
10.244.8.165:6379
10.244.8.163:6379
Adding replica 10.244.8.164:6379 to 10.244.1.131:6379
Adding replica 10.244.1.129:6379 to 10.244.8.165:6379
Adding replica 10.244.1.130:6379 to 10.244.8.163:6379
M: 0282dc6d88992d33bc4423f4a36436b4e465b42d 10.244.1.131:6379
   slots:0-5460 (5461 slots) master
M: e1786c92ae16c92bd122fb614b80920a88bddc94 10.244.8.165:6379
   slots:5461-10922 (5462 slots) master
M: 9268d2a235026a4acfaaf9d3e969ec7a559d6e9b 10.244.8.163:6379
   slots:10923-16383 (5461 slots) master
S: dd26e888f3010850f1f2499b20fef6bce0e89122 10.244.8.164:6379
   replicates 0282dc6d88992d33bc4423f4a36436b4e465b42d
S: 865583db4ee9a68acea1c2d81f89456a6c8bfb68 10.244.1.129:6379
   replicates e1786c92ae16c92bd122fb614b80920a88bddc94
S: a76cae8dcde6e55f70e5f96f589ea1520d2c3f4b 10.244.1.130:6379
   replicates 9268d2a235026a4acfaaf9d3e969ec7a559d6e9b
Can I set the above configuration? (type 'yes' to accept): yes
```

When you see something like the following, then all done!

```bash
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```
You can verify the cluster by
```bash
redis-cli -c

127.0.0.1:6379> cluster nodes
```

```bash
127.0.0.1:6379> cluster nodes
9268d2a235026a4acfaaf9d3e969ec7a559d6e9b 10.244.8.163:6379 master - 1498813397795 1498813395292 3 connected 10923-16383
865583db4ee9a68acea1c2d81f89456a6c8bfb68 10.244.1.129:6379 slave e1786c92ae16c92bd122fb614b80920a88bddc94 1498813399298 1498813397194 5 connected
e1786c92ae16c92bd122fb614b80920a88bddc94 10.244.8.165:6379 master - 1498813397295 1498813394790 2 connected 5461-10922
a76cae8dcde6e55f70e5f96f589ea1520d2c3f4b 10.244.1.130:6379 slave 9268d2a235026a4acfaaf9d3e969ec7a559d6e9b 1498813398296 1498813396292 6 connected
dd26e888f3010850f1f2499b20fef6bce0e89122 10.244.8.164:6379 master - 1498813396793 1498813394290 7 connected 0-5460
0282dc6d88992d33bc4423f4a36436b4e465b42d 10.244.1.131:6379 myself,slave dd26e888f3010850f1f2499b20fef6bce0e89122 0 0 1 connected
(0.60s)
```



## Others

  I followed the Redis official cluster tutorial to build this custom redis image(upon redis-3.2.9) and set up the cluster. Please check at <https://redis.io/topics/cluster-tutorial>


