### Tổng quan
* Hãy cùng qua sát về kiến trúc của Traefik ở hình bên dưới

 ![Architech](https://doc.traefik.io/traefik/assets/img/architecture-overview.png)

  * Đầu tiên, chúng ta sẽ định nghĩa các *entrypoint* (entrypoint có thể hình dung như cái cổng đi vào, port mấy mấy như 80 hoặc 433 bla bla)
 Các *routers* phân tích các request đi vào và đối chiếu với các rule được định nghĩa trước, nó sẽ hỏi xem "Request này dùng host nào, path là gì và header ra sao.
 Và chuyển chúng đến các service yêu cầu. Lưu ý, nếu Middlewares xuất hiện, nó sẽ nằm giữa và can thiệp vào giữa routers và service, chẳng hạn như bắt chúng ta nhập mật khẩu/ user trước khi được thông qua

#### Nhiệm vụ cụ thể của các thành phần (Có thể trùng với concepts như đọc lại cho nhớ hé)
1. Providers: định nghĩa mạng mẽo, hạ tầng chúng ta đang dùng là gì: Docker? Swarm? K8s,..
2. EntryPoints: lắng nghe traffic đi vào (port nào ?)
3. Router: phân tích request (host, path, headers, SSL,...?)
4. Services: forward yêu cầu tới service (loadbalacing hay server nào đó ?)
5. Middleware: update request hoặc đưa ra quyết định dựa trên request. Dễ hiểu hơn thì nó nằm giữa service và router: chặn lưu lượng lại và hỏi: mày là thèn nào? đứa CCCD ra đây, header đâu ? láo nháo là tau limit rate lại nhé, kiểu vậy =)))

## EntryPoints
![EntryPoint](https://doc.traefik.io/traefik/assets/img/entrypoints.png)

* Nó là cửa ngõ hứng lưu lượng từ ngoài đi vào, lắng nghe lưu lượng TCP/UDP, port bao nhiêu
#### Cấu hình mẫu
```
## Static configuration
--entryPoints.web.address=:80
--entryPoints.websecure.address=:443
```
 * Ở trên 2 entrypoint được định nghĩa: web và websecure
>Còn nhiều định nghĩa lắm mà ít dùng, mọi người muốn tìm hiểu thì vào docs chính chủ nhe [Traefik Document - EntryPoint](https://doc.traefik.io/traefik/routing/entrypoints/)

## Routers
![Routers](https://doc.traefik.io/traefik/assets/img/routers.png)
* Một router chịu trách nhiệm kết nối request đi vào thông qua EntryPoint tới service và gọi middleware trung gian (nếu có) mà chúng có thể handle phụ thuộc vào rule (host, path, ...) 
#### Cấu hình mẫu

```
## Static configuration
- "traefik.http.routers.traefik.rule=Rule_nào_đó('Điều kiện')
## - traefik.http.routers.traefik.rule=Host(`tanodd.hihi`) - router này nhận host tanodd.hihi và forward tiếp 
```

| Rules                         | Description                                                                  |
|-------------------------------|------------------------------------------------------------------------------|
| Header(`key`, `value`)        | Matches requests containing a header named key set to value.                 |
| HeaderRegexp(`key`, `regexp`) | Matches requests containing a header named key matching regexp               |
| Host(`domain`)                | Matches requests host set to domain.                                         |
| HostRegexp(`regexp`)          | Matches requests host matching regexp.                                       |
| Method(`method`)              | Matches requests method set to method.                                       |
| Path(`path`)                  | Matches requests path set to path.                                           |
| PathPrefix(`prefix`)          | Matches requests path prefix set to prefix.                                  |
| PathRegexp(`regexp`)          | Matches request path using regexp.                                           |
| Query(`key`, `value`)         | Matches requests query parameters named key set to value.                    |
| QueryRegexp(`key`, `regexp`)  | Matches requests query parameters named key matching regexp.                 |
| ClientIP(`ip`)                | Matches requests client IP using ip. It accepts IPv4, IPv6 and CIDR formats. |

## Services
![Services](https://doc.traefik.io/traefik/assets/img/services.png)
#### Cấu hình mẫu
```
 - "traefik.http.services.traefik.loadbalancer.server.port=8080"
```
> Service này sẽ forward lưu lượng đến port 8000 trong internal
* Ngoài ra, ta còn có thể trỏ cụ thể ip, port,... với weight để ưu tiên cân bằng tải ... như ví dụ dưới
```
## Dynamic configuration
http:
  services:
    my-service:
      loadBalancer:
        servers:
          - url: "http://private-ip-server-1/"
            weight: 2
          - url: "http://private-ip-server-2/"
            weight: 1
```
