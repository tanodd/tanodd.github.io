## Một chút về Proxy
Hãy tưởng tượng Internet giống như xã hội của chúng ta vậy, bạn đến quán cà phê và yêu cầu một cốc Amely Canô thông qua bạn nhân viên phục vụ, rồi nhân viên phục vụ nhận ghi lại yêu cầu sau đó chuyển sang cho pha chế. Proxy cũng giống như vậy, nó đứng giữa bạn và pha chế, tiếp nhận yêu cầu, đưa yêu cầu đó vào bên trong để xử lí và trả lại kết quả cho bạn. 
**Coffee** ☕️
### Concepts
* Các khái niệm của Traefik dựa trên những thành phần bao gồm: EntryPoint, Routers, Middlewares và Services
* Các tính năng chính bao gồm các dynamic configuration (tự động cấu hình), automation service discovery và hỗ trợ đa giao thức và đa nền tảng 
    * *EntryPoints*: EntryPoints là điểm đầu vào của Traefik - được xem như cổng chính từ bên ngoài đi vào. Nó dựa vào port và phương thức (UDP hay TCP) mà các gói tin được gửi đến và quyết định có chấp nhận forward hay không.
    * *Routers*: Router đảm nhiệm vai trong kết nối các yêu cầu đi vào đến dịch vụ bên trong được yêu cầu
    * *Middlewares*: đi kèm với router, middleware có thể modify yêu cầu đi vào hoặc phản hồi đi ra trước khi gửi chúng đến service của bạn
    * *Services*: các service được trách nhiệm cấu hình cách các service thật sự (như các container của docker) handle những request
##### Edge Router
* Traefik là một Edge Router, nó là cánh cửa ngăn cách bên ngoài và định tuyến vào trong hạ tầng của bạn, có thể là máy ảo, microservice như Docker Container,…
* Dựa vào path, host, header của request mà chúng quyết định sẽ định tuyến như thế nào

![Concept](https://doc.traefik.io/traefik/assets/img/traefik-concepts-1.png)

##### Auto Service Discovery
* Thông thường, các proxy hay như ở trên là edge router cần file cấu hình để chúng có thể biết cách định tuyến như thế nào, có những service nào nằm bên trong nhưng Traefik thì khác, nó có thể tự động nhận biết các server để cập nhật bảng định tuyến
* Dựa vào các service của bạn và thông tin mà bạn cấu hình mà Traefik có thể hình dung được cách đặc điểm của yêu cầu mà service có thể handle

![Auto Discovery](https://doc.traefik.io/traefik/assets/img/traefik-concepts-2.png)

* Nó có nghĩa là khi một service được deploy, Traefik sẽ tự động phát hiện ngay lập tức rồi sau đó update routing table, real time luôn :) (*đá nginx ra chuồng gà thôi), tương tự khi service được remove ra thì nó cũng remove các thứ liên quan. Chúng ta không cần phải làm gì cả, trừ khi muốn can thiệp vào.

>Lưu ý, traefik có 2 loại cấu hình lại Fix cứng và Dynamic, fix cứng có nghĩa là đã start traefik lên rồi thì khó sửa (nếu muốn sửa thì sửa file YAML hoặc TOML), còn Dynamic thì sửa vô tư 

### Kiến trúc chi tiết
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
## Provider
- Chúng ta dùng để chỉ định internal service dùng cái gì để hoạt động, như Docker hay Swarm,... Mục đích của việc chỉ định Provider là để Traefik có thể nhận dạng được cấu hình Dynamic Routing khi ta tạo 1 container mới, xoá đi hay cập nhật - đây là một tính năng vượt trội đáng để sử dụng Traefik - Dynamic Routing
* Docker: `--providers.docker=true`
* Swarm: `"--providers.swarm=true"`
* Kubenetes IngressRoute, Ingress, Gateưay API: tui chưa tiếp xúc nên tạm để link đây hé [Kubenetes Provider](https://doc.traefik.io/traefik/routing/providers/kubernetes-crd/)

## Middleware 
* Chúng ta thường dùng Middle để xác thực, http redirect https, forwardAuth, Rate Limit 
* Có 2 loại Middleware là HTTP với TCP, nhưng ở bài viết này chúng ta chỉ hướng đến HTTP 

    |     Middleware    |                    Purpose                    |             Area            |
    |:-----------------:|:---------------------------------------------:|:---------------------------:|
    | AddPrefix         |               Adds a Path Prefix              |        Path Modifier        |
    | BasicAuth         |           Adds Basic Authentication           |   Security, Authentication  |
    | Buffering         |          Buffers the request/response         |      Request Lifecycle      |
    | Chain             |     Combines multiple pieces of middleware    |             Misc            |
    | CircuitBreaker    |      Prevents calling unhealthy services      |      Request Lifecycle      |
    | Compress          |            Compresses the response            |       Content Modifier      |
    | ContentType       |      Handles Content-Type auto-detection      |             Misc            |
    | DigestAuth        |           Adds Digest Authentication          |   Security, Authentication  |
    | Errors            |           Defines custom error pages          |      Request Lifecycle      |
    | ForwardAuth       |            Delegates Authentication           |   Security, Authentication  |
    | Headers           |             Adds / Updates headers            |           Security          |
    | IPAllowList       |         Limits the allowed client IPs         | Security, Request lifecycle |
    | InFlightReq       | Limits the number of simultaneous connections | Security, Request lifecycle |
    | PassTLSClientCert |      Adds Client Certificates in a Header     |           Security          |
    | RateLimit         |           Limits the call frequency           | Security, Request lifecycle |
    | RedirectScheme    |           Redirects based on scheme           |      Request lifecycle      |
    | RedirectRegex     |            Redirects based on regex           |      Request lifecycle      |
    | ReplacePath       |        Changes the path of the request        |        Path Modifier        |
    | ReplacePathRegex  |        Changes the path of the request        |        Path Modifier        |
    | Retry             |     Automatically retries in case of error    |      Request lifecycle      |
    | StripPrefix       |        Changes the path of the request        |        Path Modifier        |
    | StripPrefixRegex  |        Changes the path of the request        |        Path Modifier        |

**Thôi lí thuyết ngang ni thôi, dài quá rồi, uống ly cà phê và nghỉ khoẻ để cuối tuần demo thực hành** ☕️