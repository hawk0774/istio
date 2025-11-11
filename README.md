```mermaid
flowchart TD
    %% Internet
    subgraph Internet_Client
        A[Client (curl/browser)]
        B[DNS *.moscow.alfaintra.net -> 10.211.64.93]
    end

    %% MetalLB
    subgraph MetalLB
        C[MetalLB L2 IP: 10.211.64.93]
    end

    %% istio-system
    subgraph istio_system
        D[Service: istio-ingress LB -> ztunnel]
        E[ztunnel L4 mTLS/HBONE]
        F[Gateway: istio-ingress-test Port:8080]
    end

    %% Waypoint
    subgraph Waypoint_Proxy
        G[Waypoint L7 Envoy VirtualService]
    end

    %% dev
    subgraph dev
        VS1[vs-01-robots]
        VS2[vs-02-frontend /]
        VS3[vs-03-restapi rest.*]
        VS8[vs-08-graphql /graphql -> /]
        VS9[vs-09-restserver /rest -> /]
        VS10[vs-10-metrics /authmodule -> /]

        P1[unity-frontend:80]
        P2[unity-graphql:8880]
        P3[unity-restapi:8880]
        P4[unity-restserver:8880]
        P5[unity-authmodule:9090]
    end

    %% longhorn
    subgraph longhorn_system
        VS4[vs-04-longhorn]
        P6[longhorn-frontend:80]
    end

    %% minio
    subgraph minio_operator
        VS5[vs-05-minio]
        VS6[vs-06-minio-console]
        P7[minio:9000]
        P8[minio-console:9090]
    end

    %% monitoring
    subgraph monitoring
        VS7[vs-07-monitoring]
        P9[kps-kube-state-metrics:8080]
    end
