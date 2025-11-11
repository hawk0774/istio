## User Info
- **Current time**: November 11, 2025 06:11 PM CET  
- **Country**: SE (Sweden)

---


```mermaid
flowchart TD
    %% ── Клиент ───────────────────────────────────────
    subgraph Client
        A[Client (curl / browser)]
        B[DNS *.moscow.alfaintra.net → 10.211.64.93]
    end

    %% ── MetalLB ───────────────────────────────────────
    subgraph MetalLB
        C[MetalLB L2 IP: 10.211.64.93]
    end

    %% ── istio-system ───────────────────────────────────
    subgraph istio_system
        D[Service: istio-ingress LB → ztunnel]
        E[ztunnel L4 mTLS/HBONE]
        F[Gateway: istio-ingress-test Port:8080]
    end

    %% ── Waypoint (L7) ──────────────────────────────────
    subgraph Waypoint
        G[Waypoint L7 Envoy VirtualService]
    end

    %% ── dev ───────────────────────────────────────────
    subgraph dev
        VS1[vs-01-robots]
        VS2[vs-02-frontend /]
        VS3[vs-03-restapi rest.*]
        VS8[vs-08-graphql /graphql → /]
        VS9[vs-09-restserver /rest → /]
        VS10[vs-10-metrics /authmodule → /]

        P1[unity-frontend:80]
        P2[unity-graphql:8880]
        P3[unity-restapi:8880]
        P4[unity-restserver:8880]
        P5[unity-authmodule:9090]
    end

    %% ── longhorn-system ───────────────────────────────
    subgraph longhorn_system
        VS4[vs-04-longhorn]
        P6[longhorn-frontend:80]
    end

    %% ── minio-operator ───────────────────────────────
    subgraph minio_operator
        VS5[vs-05-minio]
        VS6[vs-06-minio-console]
        P7[minio:9000]
        P8[minio-console:9090]
    end

    %% ── monitoring ───────────────────────────────────
    subgraph monitoring
        VS7[vs-07-monitoring]
        P9[kps-kube-state-metrics:8080]
    end

    %% ── Поток ────────────────────────────────────────
    A --> B --> C --> D --> E --> F --> G
    G --> VS1 --> P1
    G --> VS2 --> P1
    G --> VS3 --> P3
    G --> VS4 --> P6
    G --> VS5 --> P7
    G --> VS6 --> P8
    G --> VS7 --> P9
    G --> VS8 --> P2
    G --> VS9 --> P4
    G --> VS10 --> P5

    %% ── Стили ────────────────────────────────────────
    classDef client fill:#e1f5fe,stroke:#333
    classDef infra fill:#fff3e0,stroke:#333
    classDef istio fill:#f3e5f5,stroke:#333
    classDef waypoint fill:#e8f5e8,stroke:#333
    classDef vs fill:#fff9c4,stroke:#333

    class A client
    class C infra
    class E,F istio
    class G waypoint
    class VS1,VS2,VS3,VS4,VS5,VS6,VS7,VS8,VS9,VS10 vs
