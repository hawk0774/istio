# Схема работы Istio Ambient Ingress (1.24.1)

```mermaid
flowchart TD
    subgraph "Интернет / Клиент"
        A[Клиент<br/>curl / браузер]
        B[DNS<br/>*.moscow.alfaintra.net<br/>→ 10.211.64.93]
    end

    subgraph "MetalLB"
        C[MetalLB<br/>L2Advertisement<br/>IP: 10.211.64.93/32]
    end

    subgraph "istio-system"
        D[Service: istio-ingress<br/>LoadBalancer → ztunnel]
        E[ztunnel (L4)<br/>mTLS, HBONE]
        F[Gateway: istio-ingress-test<br/>Port: 8080, HTTP]
    end

    subgraph "Waypoint Proxy (L7 Envoy)"
        G[Waypoint Proxy<br/>Обрабатывает VirtualService]
    end

    subgraph "dev"
        H1[vs-01-robots-selective.yaml<br/>robots.txt → только публичные хосты]
        H2[vs-02-frontend.yaml<br/>/ → unity-frontend]
        H3[vs-03-restapi.yaml<br/>rest.* → unity-restapi]
        H8[vs-08-graphql.yaml<br/>/graphql → / (rewrite)]
        H9[vs-09-restserver.yaml<br/>/rest → / (rewrite)]
        H10[vs-10-metrics-services.yaml<br/>/authmodule, /platform → / (rewrite)]
        P1[unity-frontend<br/>Port: 80]
        P2[unity-graphql<br/>Port: 8880]
        P3[unity-restapi<br/>Port: 8880]
        P4[unity-restserver<br/>Port: 8880]
        P5[unity-authmodule<br/>Port: 9090]
    end

    subgraph "longhorn-system"
        H4[vs-04-longhorn.yaml<br/>ibunikube4 → longhorn-frontend]
        P6[long: longhorn-frontend<br/>Port: 80]
    end

    subgraph "minio-operator"
        H5[vs-05-minio.yaml<br/>files.* → minio]
        H6[vs-06-minio-console.yaml<br/>unitytest-mc → minio-console]
        P7[minio<br/>Port: 9000]
        P8[minio-console<br/>Port: 9090]
    end

    subgraph "monitoring"
        H7[vs-07-monitoring.yaml<br/>/kube-state-metrics → kps]
        P9[kps-kube-state-metrics<br/>Port: 8080]
    end

    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G

    G --> H1
    G --> H2
    G --> H3
    G --> H4
    G --> H5
    G --> H6
    G --> H7
    G --> H8
    G --> H9
    G --> H10

    H1 --> P1
    H2 --> P1
    H3 --> P3
    H4 --> P6
    H5 --> P7
    H6 --> P8
    H7 --> P9
    H8 --> P2
    H9 --> P4
    H10 --> P5

    style A fill:#e1f5fe
    style C fill:#fff3e0
    style E fill:#f3e5f5
    style G fill:#e8f5e8
    style H1 fill:#fff9c4
    style H10 fill:#fff9c4
