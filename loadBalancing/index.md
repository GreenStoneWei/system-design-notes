# Load Balancing

## 什麼是 Load Balance
一種將工作量分散導流至不同資源（resource instances）的裝置，通常用來提高可靠度及可用性（reliability and availability）。讓服務可以承載更多流量（take as a site gets more traffic）
- TLS termination
- Observability
- Security and DoS mitigation



#### Assumption
- service is stateless
- if not so -> sticky sessions (fragile)

### Load Balance 的種類
#### 1. Layer 7 load balancing (HTTP, HTTPS, WebSocket)
  - Nginx/HAProxy
  - 通常會在這裡解密（terminate SSL）
  - Usually smarter (但需要看 packet 裡的內容)
#### 2. Layer 4 load balancing (TCP, UDP)
  - ip based
  - HAProxy/IPVS
#### 3. Layer 3 load balancing
  - ECMP (equal cost multi path routing).
  - Usually done in hardware with top-of-rack switches
#### 4. DNS load balancing
  - nslookup
    `nslookup [-type=any] [-server={NAME_SERVER_IP}] yahoo.com`
  - GeoDNS, round-robin
#### 5. Manually load balancing with multiple subdomains
  - 考慮 sharded data 還沒有複製到其他 data center
    - 必須導流至有資源的地方
    - 每個 data center 最好有subdomains
#### 6. Anycast
  - 一般的 Internet 是 Unicast，（每個 instance 有獨一無二的 IP address）
  - Anycast 是讓多個 instance 有同樣的 IP

#### Others
  - Embedded client library
  - Sidecar proxy (service mesh)

1 - 3 可以達到提高負載量和可用性，但仍有單點失效（single point of failure）的問題

比喻：
Layer 7: 機場海關櫃檯，一個變多個
Layer 4: 分流本國人與外國人，本國人及外國人皆有多個海關人員

### Health checking
#### Active
  - e.g., an HTTP request to a /healthcheck endpoint
#### Passive
  - e.g., if there have been three HTTP 503 response codes in a row.

---

### Top Five Scalability Patterns
#### 1. Global Server Load Balancing (GSLB)
  - GSLB is a top-level availability assurance pattern. Site availability.
  - if one data center (cloud or traditional) isn’t responding, you can find another. 
  - steps:
    1. Ask the global load balancer for the appropriate IP address.
    2. Make the request to the IP address of the site returned by the GSLB inquiry.
#### 2. Plain Old Load Balancing (POLB)
  - Connection-based Availability
  - standard, TCP-based load balancing
  - nothing more than algorithms involved in the choice of how to direct requests.
#### 3. Persistence
  - Sticky Sessions & SSL
  - most popular being cookie-based
#### 4. Host-Based Load Balancing
  - This is more efficient and can reduce the number of servers you need to scale an application.
  - Ingress controllers
#### 5. Route and Return 
  - Layer 7 (HTTP) Load Balancing
  - API versioning

---
### Miscellaneous
- Direct server return
  - 一種讓 server 回來的 response 不通過各 layer 直接回給 client。

#### Commands
  - nslookup
  - traceroute



Ref: 

https://blog.envoyproxy.io/introduction-to-modern-network-load-balancing-and-proxying-a57f6ff80236

https://www.f5.com/company/blog/top-five-scalability-patterns

https://blog.vivekpanyam.com/scaling-a-web-service-load-balancing/

https://www.youtube.com/watch?v=MKgJeqF1DHw

