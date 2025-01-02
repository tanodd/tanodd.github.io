# Docker Swarm

**Introduction:**

Docker Swarm là một công cụ (có phải công cụ không ta) được Docker đẻ ra (nhưng mà bây giờ bán để tập trung phát triển mảng khác rồi).
Nó cho phép bạn quản lí và scale các container của docker trên nhiều node. Hiện tại thì nó đã lỗi thời so với K8s,
nhưng nó giúp bạn hình dung được bức tranh tổng thể của microservice. Gét gô

**Prerequisites:**
Trước khi đến với Docker Swarm thì tui muốn chắc bạn đã:
- Có biết sơ sơ về Docker
- Có 2 máy (ưu tiên máy ảo) - 1 máy để làm Node manager và 1 để làm node worker. Cả 2 đều phải được cài docker


**Setting Up Docker Swarm:**

1. Setup docker Swarm trên Node đầu tiên, node này sẽ trở thành Node Manager nhé

```
docker swarm init --advertise-addr <MANAGER_NODE_IP>
```

<aside>
💡

To check token
docker swarm join-token manager

</aside>

2. Join node worker vào swarm của node manager vừa tạo

On each worker node, run the following command (replace `<MANAGER_NODE_IP>` with the IP address of the manager node):

```
docker swarm join - token <TOKEN> <MANAGER_NODE_IP>:2377
```

3. Verify the Swarm status:

On the manager node, run:

```
docker node ls
```

**Deploying and Managing Services in Docker Swarm:**

Deploying a service:

```
docker service create - name my-nginx - replicas 3 -p 80:80 nginx
```

Listing services:

```
docker service ls
```

Scaling a service:

```
docker service scale my-nginx=5
```

Updating a service:

```
docker service update - image nginx:latest my-nginx
```

Inspecting a service:

```
docker service inspect my-nginx
```

Removing a service:

```
docker service rm my-nginx
```

**Conclusion:**

Docker Swarm provides a simple yet powerful solution for orchestrating containers in a distributed environment. With the ability to deploy, scale, update, and manage services across multiple nodes, Docker Swarm empowers developers and system administrators to build resilient and scalable applications.

Remember to experiment and explore additional features such as service rolling updates, service rollback, service constraints, and more. Happy container orchestration with Docker Swarm!