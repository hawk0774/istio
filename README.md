# Схема работы Istio Ambient Ingress (1.24.1)

```mermaid
flowchart TD
    %% ── Интернет ────────────────────────────────────────
    subgraph Интернет_Клиент
        A[Клиент\n(curl / браузер)]
        B[DNS\n*.moscow.alfaintra.net\n→ 10.211.64.93]
    end

    %% ── MetalLB ────────────────────────────────────────
    subgraph MetalLB
        C[MetalLB\nL2Advertisement\nIP: 10.211.64.93/32]
    end

    %% ── istio-system ───────────────────────────────────
    subgraph istio_system
        D[Service: istio‑ingress\nLoadBalancer → ztunnel]
        E[ztunnel (L4)\nmTLS, HBONE]
        F[Gateway: istio‑ingress‑test\nPort: 8080, HTTP]
    end

    %% ── Waypoint (L7) ──────────────────────────────────
    subgraph Waypoint_Proxy
        G[Waypoint Proxy\n(L7 Envoy)\nVirtualService, rewrite, robots.txt]
    end

    %% ── dev ───────────────────────────────────────────
    subgraph dev
        VS1[vs‑01‑robots‑selective.yaml\nrobots.txt (только публичные)]
        VS2[vs‑02‑frontend.yaml\n/ → unity‑frontend]
        VS3[vs‑03‑restapi.yaml\nrest.* → unity‑restapi]
        VS8[vs‑08‑graphql.yaml\n/graphql → / (rewrite)]
        VS9[vs‑09‑restserver.yaml\n/rest → / (rewrite)]
        VS10[vs‑10‑metrics‑services.yaml\n/authmodule, /platform … → / (rewrite)]

        POD1[unity‑frontend\n:80]
        POD2[unity‑graphql\n:8880]
        POD3[unity‑restapi\n:8880]
        POD4[unity‑restserver\n:8880]
        POD5[unity‑authmodule\n:9090]
    end

    %% ── longhorn‑system ───────────────────────────────
    subgraph longhorn_system
        VS4[vs‑04‑longhorn.yaml\nibunikube4 → longhorn‑frontend]
        POD6[longhorn‑frontend\n:80]
    end
