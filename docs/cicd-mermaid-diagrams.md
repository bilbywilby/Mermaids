# CI/CD & Infrastructure — Mermaid Diagram Reference

Companion diagrams to [`cicd-deep-dive-reference.md`](./cicd-deep-dive-reference.md). Paste any section into a Mermaid renderer (mermaid.live, GitHub/GitLab markdown, docs site) to view.

## Table of Contents
1. [Complete System Architecture](#1-complete-system-architecture)
2. [CI/CD Pipeline Deep Dive](#2-cicd-pipeline-deep-dive)
3. [Kubernetes Deployment Strategies](#3-kubernetes-deployment-strategies)
4. [Security Architecture & Zero Trust](#4-security-architecture--zero-trust)
5. [Service Mesh Traffic Flow](#5-service-mesh-traffic-flow)
6. [Edge Computing Architecture](#6-edge-computing-architecture)
7. [Post-Quantum Migration Roadmap](#7-post-quantum-migration-roadmap)
8. [Machine Learning Operations Pipeline](#8-machine-learning-operations-pipeline)
9. [Security Forensics Pipeline](#9-security-forensics-pipeline)

---

## 1. Complete System Architecture

```mermaid
flowchart TD
    subgraph Dev["Developer Experience"]
        A[VS Code/JetBrains<br/>AI Assistants]
        B[Terminal Tools<br/>aicode, tmux, zsh]
        C[Local LLMs<br/>Ollama, Codellama]
    end
    
    subgraph CI["CI/CD Pipeline"]
        D[Github Actions<br/>Self-Hosted Runners]
        E[Pre-commit Hooks<br/>Linting, Testing]
        F[Docker Builds<br/>Multi-stage]
        G[SonarQube/Snyk<br/>Security Scans]
    end
    
    subgraph Registry["Artifact Management"]
        H[Container Registry<br/>GHCR, ECR]
        I[Package Registry<br/>PyPI, npm]
        J[Binary Artifacts<br/>OSSRH]
    end
    
    subgraph Deploy["Deployment Layers"]
        K[Kubernetes Clusters<br/>Helm, Kustomize]
        L[Service Mesh<br/>Istio, Linkerd]
        M[Multi-Cloud<br/>AWS/GCP/Azure]
        N[Edge Locations<br/>K3s, KubeEdge]
    end
    
    subgraph Monitoring["Observability"]
        O[Prometheus/Grafana<br/>Metrics]
        P[Loki/Tempo<br/>Logs & Traces]
        Q[Alert Manager<br/>PagerDuty, Slack]
        R[Synthetic Monitoring<br/>Blackbox Exporter]
    end
    
    subgraph Security["Security Layer"]
        S[HashiCorp Vault<br/>Secrets Mgmt]
        T[TLS/Certificates<br/>Let's Encrypt]
        U[WAF/Firewall<br/>CloudFlare]
        V[ZK Proofs<br/>Privacy]
    end
    
    subgraph Data["Data & State"]
        W[PostgreSQL/MySQL<br/>Primary DB]
        X[Redis/Memcached<br/>Caching]
        Y[Kafka/RabbitMQ<br/>Messaging]
        Z[S3/Object Storage<br/>Backups]
    end
    
    A --> D
    B --> D
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    H --> K
    I --> K
    J --> L
    K --> M
    L --> N
    M --> O
    N --> O
    O --> Q
    P --> Q
    Q --> R
    K --> S
    S --> T
    M --> U
    W --> K
    X --> K
    Y --> K
    Z --> K
    
    classDef dev fill:#e1f5fe,stroke:#0288d1;
    classDef ci fill:#f3e5f5,stroke:#7b1fa2;
    classDef registry fill:#fff3e0,stroke:#f57c00;
    classDef deploy fill:#e8f5e9,stroke:#388e3c;
    classDef monitoring fill:#ffebee,stroke:#d32f2f;
    classDef security fill:#fafafa,stroke:#424242;
    classDef data fill:#fce4ec,stroke:#c2185b;
    
    class A,B,C dev;
    class D,E,F,G ci;
    class H,I,J registry;
    class K,L,M,N deploy;
    class O,P,Q,R monitoring;
    class S,T,U,V security;
    class W,X,Y,Z data;

EOF
mermaid flowchart TD subgraph Dev["Developer Experience"] A[VS Code/JetBrains AI Assistants] B[Terminal Tools aicode tmux zsh] C[Local LLMs Ollama Codellama] end

subgraph CI["CI/CD Pipeline"]
    D[Github Actions Self-Hosted Runners]
    E[Pre-commit Hooks Linting Testing]
    F[Docker Builds Multi-stage]
    G[SonarQube Snyk Security Scans]
end

subgraph Registry["Artifact Management"]
    H[Container Registry GHCR ECR]
    I[Package Registry PyPI npm]
    J[Binary Artifacts OSSRH]
end

subgraph Deploy["Deployment Layers"]
    K[Kubernetes Clusters Helm Kustomize]
    L[Service Mesh Istio Linkerd]
    M[Multi-Cloud AWS GCP Azure]
    N[Edge Locations K3s KubeEdge]
end

subgraph Monitoring["Observability"]
    O[Prometheus Grafana Metrics]
    P[Loki Tempo Logs Traces]
    Q[Alert Manager PagerDuty Slack]
    R[Synthetic Monitoring Blackbox]
end

subgraph Security["Security Layer"]
    S[HashiCorp Vault Secrets]
    T[TLS Certificates]
    U[WAF Firewall]
    V[ZK Proofs Privacy]
end

subgraph Data["Data & State"]
    W[PostgreSQL MySQL Primary DB]
    X[Redis Memcached Caching]
    Y[Kafka RabbitMQ Messaging]
    Z[S3 Object Storage Backups]
end

A --> D
B --> D
C --> D
D --> E
E --> F
F --> G
G --> H
H --> K
I --> K
J --> L
K --> M
L --> N
M --> O
N --> O
O --> Q
P --> Q
Q --> R
K --> S
S --> T
M --> U
W --> K
X --> K
Y --> K
Z --> K

classDef dev fill:#e1f5fe,stroke:#0288d1;
classDef ci fill:#f3e5f5,stroke:#7b1fa2;
classDef registry fill:#fff3e0,stroke:#f57c00;
classDef deploy fill:#e8f5e9,stroke:#388e3c;
classDef monitoring fill:#ffebee,stroke:#d32f2f;
classDef security fill:#fafafa,stroke:#424242;
classDef data fill:#fce4ec,stroke:#c2185b;

class A,B,C dev;
class D,E,F,G ci;
class H,I,J registry;
class K,L,M,N deploy;
class O,P,Q,R monitoring;
class S,T,U,V security;
class W,X,Y,Z data;
2. CI/CD Pipeline Deep Dive
flowchart LR
    subgraph Trigger["Trigger Sources"]
        T1[Git Push]
        T2[Pull Request]
        T3[Scheduled Cron]
        T4[Webhook]
        T5[Manual Dispatch]
    end
    
    subgraph Build["Build Stage"]
        B1[Checkout Code]
        B2[Cache Dependencies]
        B3[Install Tooling]
        B4[Compile Build]
        B5[Docker Build]
        B6[Push Registry]
    end
    
    subgraph Test["Test Stage"]
        T1A[Unit Tests pytest jest]
        T1B[Integration Tests testcontainers]
        T1C[E2E Tests Playwright Cypress]
        T1D[Performance Tests k6 Locust]
        T1E[Security Tests Bandit Trivy]
        T1F[Coverage Reports Codecov]
    end
    
    subgraph QA["Quality Assurance"]
        QA1[Static Analysis SonarQube]
        QA2[Code Formatting black prettier]
        QA3[License Compliance FOSSA]
        QA4[Dependency Audit pip-audit]
    end
    
    subgraph DeployStaging["Deploy to Staging"]
        DS1[Helm Upgrade namespace staging]
        DS2[Smoke Tests health ready]
        DS3[Load Test baseline perf]
        DS4[Approval Gate manual auto]
    end
    
    subgraph DeployProd["Deploy to Production"]
        DP1[Blue-Green Swap]
        DP2[Canary 10 to 100 percent]
        DP3[Feature Flags LaunchDarkly]
        DP4[Monitoring Gates error rate less 1 percent]
    end
    
    subgraph PostDeploy["Post-Deployment"]
        PD1[Metrics Collection Prometheus]
        PD2[Log Aggregation Loki]
        PD3[Trace Analysis Tempo]
        PD4[Alert Configuration Alert Manager]
    end
    
    subgraph Cleanup["Cleanup"]
        C1[Prune Old Images]
        C2[Archive Artifacts]
        C3[Generate Changelog]
        C4[Notify Teams Slack Email]
    end
    
    T1 --> B1
    T2 --> B1
    T3 --> B1
    T4 --> B1
    T5 --> B1
    
    B1 --> B2 --> B3 --> B4 --> B5 --> B6
    B6 --> QA1 --> QA2 --> QA3 --> QA4
    QA4 --> T1A --> T1B --> T1C --> T1D --> T1E --> T1F
    T1F --> DS1 --> DS2 --> DS3 --> DS4
    DS4 --> DP1 --> DP2 --> DP3 --> DP4
    DP4 --> PD1 --> PD2 --> PD3 --> PD4
    PD4 --> C1 --> C2 --> C3 --> C4
    PD4 -.->|rollback if fail|B6
    
    style Trigger fill:#fff9c4,stroke:#fbc02d
    style Build fill:#e3f2fd,stroke:#1976d2
    style Test fill:#e8f5e9,stroke:#388e3c
    style QA fill:#f3e5f5,stroke:#7b1fa2
    style DeployStaging fill:#fff3e0,stroke:#f57c00
    style DeployProd fill:#fce4ec,stroke:#c2185b
    style PostDeploy fill:#e0f2f1,stroke:#00796b
    style Cleanup fill:#f5f5f5,stroke:#616161

3. Kubernetes Deployment Strategies
stateDiagram-v2
    [*] --> Idle
    Idle --> PreCheck: trigger_deploy
    PreCheck --> HealthGate: run_smoke_tests
    HealthGate --> PreDeploy: all_checks_passed
    HealthGate --> Idle: checks_failed
    
    state PreDeploy {
        [*] --> BackupDB
        BackupDB --> SnapshotState
        SnapshotState --> GenerateManifest
        GenerateManifest --> [*]
    }
    
    PreDeploy --> RolloutStrategy
    state RolloutStrategy <<choice>>
    
    RolloutStrategy --> BlueGreen: blue_green=true
    RolloutStrategy --> Canary: canary=true
    RolloutStrategy --> Rolling: default
    
    state BlueGreen {
        [*] --> DeployNewSlot
        DeployNewSlot --> WaitReady
        WaitReady --> VerifyHealth
        VerifyHealth --> SwitchTraffic
        SwitchTraffic --> ShutdownOldSlot
        ShutdownOldSlot --> [*]
    }
    
    state Canary {
        [*] --> DeployCanary
        DeployCanary --> MonitorMetrics
        MonitorMetrics --> EvaluateMetrics <<choice>>
        EvaluateMetrics --> IncreaseWeight: error_rate<threshold
        EvaluateMetrics --> Rollback: error_rate>=threshold
        IncreaseWeight --> GradualIncrease: weight+=25percent
        GradualIncrease --> MonitorMetrics
        Rollback --> RestorePrevious: revert_manifest
        RestorePrevious --> [*]
    }
    
    state Rolling {
        [*] --> ScaleDownPods
        ScaleDownPods --> CreateNewPods
        CreateNewPods --> WaitReadiness
        WaitReadiness --> HealthCheck
        HealthCheck --> ContinueOrStop <<choice>>
        ContinueOrStop --> ScaleDownPods: more_pods_needed
        ContinueOrStop --> Complete: all_pods_ready
    }
    
    BlueGreen --> Monitoring
    Canary --> Monitoring
    Rolling --> Monitoring
    
    state Monitoring {
        [*] --> CollectMetrics
        CollectMetrics --> AnalyzeSLO
        AnalyzeSLO --> AlertTeam: slo_breach
        AnalyzeSLO --> [*]: slos_met
    }
    
    Monitoring --> PostDeploy
    PostDeploy --> [*]
    
    note right of DeployCanary
      Traffic split: 10% to 25% to 50% to 100%
      Each stage: 5-10 min monitoring
    end note
    
    note left of BlueGreen
      Zero downtime
      Instant rollback possible
      Double resource temporarily
    end note
    
    note right of Rolling
      maxSurge: 25%
      maxUnavailable: 0
      Standard for stateless apps
    end note

4. Security Architecture & Zero Trust
flowchart TB
    subgraph Identity["Identity & Access"]
        IAM1[OIDC Auth0 Okta Keycloak]
        IAM2[Service Accounts]
        IAM3[MFA TOTP WebAuthn]
    end
    
    subgraph Network["Network Security"]
        NS1[VPC Isolation]
        NS2[Network Policies Calico Cilium]
        NS3[mTLS Istio Linkerd]
    end
    
    subgraph Secrets["Secrets Management"]
        SM1[HashiCorp Vault]
        SM2[AWS Secrets Manager]
        SM3[Sealed Secrets K8s]
    end
    
    subgraph SupplyChain["Supply Chain"]
        SC1[Sigstore Cosign]
        SC2[SBOM Syft CycloneDX]
        SC3[Policy OPA Kyverno]
    end
    
    subgraph Runtime["Runtime Protection"]
        RT1[gVisor Kata]
        RT2[Falco Security]
        RT3[eBPF Monitoring]
    end
    
    subgraph DataProtection["Data Protection"]
        DP1[Encryption AES-256]
        DP2[TLS 1.3]
        DP3[Tokenization PII]
    end
    
    subgraph Detection["Threat Detection"]
        DET1[SIEM Splunk Datadog]
        DET2[SOAR Playbooks]
    end
    
    Identity --> Network
    Network --> Runtime
    Runtime --> DataProtection
    Secrets --> Identity
    SupplyChain --> Runtime
    DataProtection --> Detection
    Runtime --> Detection
    
    classDef identity fill:#f3e5f5,stroke:#7b1fa2
    classDef network fill:#e3f2bd,stroke:#1976d2
    classDef secrets fill:#fff3e0,stroke:#f57c00
    classDef supply fill:#e8f5e9,stroke:#388e3c
    classDef runtime fill:#ffebee,stroke:#d32f2f
    classDef data fill:#fce4ec,stroke:#c2185b
    classDef detection fill:#e0f2f1,stroke:#00796b
    
    class IAM1,IAM2,IAM3 identity
    class NS1,NS2,NS3 network
    class SM1,SM2,SM3 secrets
    class SC1,SC2,SC3 supply
    class RT1,RT2,RT3 runtime
    class DP1,DP2,DP3 data
    class DET1,DET2 detection

5. Service Mesh Traffic Flow
flowchart LR
    subgraph Clients["Clients"]
        C1[Web Browser]
        C2[Mobile App]
        C3[API Partners]
    end
    
    subgraph Gateway["Gateway Layer"]
        G1[Ingress nginx traefik]
        G2[API Gateway Kong]
        G3[Rate Limiting]
    end
    
    subgraph Mesh["Service Mesh"]
        M1[Istio Sidecar]
        M2[Envoy Proxy]
        M3[Traffic Routing]
        M4[Circuit Breakers]
    end
    
    subgraph Backends["Backends"]
        B1[API Service]
        B2[Worker Service]
        B3[Cache Redis]
    end
    
    Clients --> Gateway
    Gateway --> Mesh
    Mesh --> Backends
    
    style Clients fill:#fff9c4
    style Gateway fill:#f3e5f5
    style Mesh fill:#e8f5e9
    style Backends fill:#e3f2fd

6. Edge Computing Architecture
flowchart TB
    subgraph Cloud["Central Cloud"]
        C1[K8s Control Plane]
        C2[Central Registry]
        C3[CI CD Pipeline]
    end
    
    subgraph Regional["Regional Hubs"]
        RH1[US-East]
        RH2[EU-West]
        RH3[AP-South]
    end
    
    subgraph EdgeNodes["Edge Locations"]
        EN1[Edge Node 1 K3s]
        EN2[Edge Node 2 K3s]
        EN3[IoT Gateway MQTT]
    end
    
    subgraph Workloads["Workloads"]
        W1[Inference TF Lite]
        W2[Real-time Flink]
        W3[Local Caching Redis]
    end
    
    C1 --> RH1
    C1 --> RH2
    C1 --> RH3
    RH1 --> EN1
    RH2 --> EN2
    RH3 --> EN3
    EN1 --> W1
    EN2 --> W2
    EN3 --> W3
    
    %% Note: Hundreds of edge sites with intermittent connectivity
    %% Note: Offline-first design with eventual consistency
    
    style Cloud fill:#e1f5fe
    style Regional fill:#f3e5f5
    style EdgeNodes fill:#e8f5e9
    style Workloads fill:#fff3e0

7. Post-Quantum Migration Roadmap
gantt
    title Post-Quantum Cryptography Migration
    dateFormat  YYYY-MM-DD
    section Phase 1 Assessment
    Inventory       :p1, 2024-01-01, 30d
    Classification  :after p1, 20d
    section Phase 2 Hybrid
    Library Install :p2, 2024-04-01, 45d
    Test Env Setup  :after p2, 30d
    section Phase 3 Production
    Certificate Rot :p3, 2025-01-01, 90d
    API Migration   :after p3, 60d
    section Phase 4 Final
    PQ-Only Mode    :p4, 2025-07-01, 90d
    Compliance Audit:after p4, 60d

8. Machine Learning Operations Pipeline
flowchart TD
    subgraph Data["Data Layer"]
        DL1[Data Lake S3]
        DL2[Feature Store Feast]
        DL3[Data Validation]
    end
    
    subgraph Training["Training"]
        T1[MLflow Tracking]
        T2[Hyperparameter Tune]
        T3[Model Registry]
    end
    
    subgraph Serving["Serving"]
        S1[TF Serving]
        S2[Batch Spark]
        S3[Edge TF Lite]
    end
    
    subgraph Monitor["Monitoring"]
        M1[Prediction Drift]
        M2[Data Quality]
        M3[Alerting]
    end
    
    subgraph Retrain["Retrain Trigger"]
        R1[Schedule]
        R2[Performance Decay]
        R3[Data Shift]
    end
    
    DL1 --> DL2 --> DL3
    DL3 --> T1 --> T2 --> T3
    T3 --> S1 --> S2 --> S3
    S1 --> M1 --> M2 --> M3
    M2 -.->|trigger|R2
    M1 -.->|trigger|R3
    
    R1 --> T1
    R2 --> T1
    R3 --> T1
    
    style Data fill:#e1f5fe
    style Training fill:#f3e5f5
    style Serving fill:#e8f5e9
    style Monitor fill:#fff3e0
    style Retrain fill:#ffebee

9. Security Forensics Pipeline
flowchart TB
    subgraph Detection["Detection"]
        D1[SIEM Alerts]
        D2[YARA Matches]
        D3[Sigma Rules]
    end
    
    subgraph Triage["Triage"]
        T1[Assessment]
        T2[Impact Analysis]
        T3[Preservation]
    end
    
    subgraph Collection["Collection"]
        C1[Memory Dump]
        C2[Disk Imaging]
        C3[Log Aggregation]
    end
    
    subgraph Analysis["Analysis"]
        A1[Reverse Eng]
        A2[Timeline Recon]
        A3[IOCs Extract]
    end
    
    subgraph Response["Response"]
        R1[Isolate Hosts]
        R2[Block IOCs]
        R3[System Rebuild]
    end
    
    D1 --> Triage
    D2 --> Triage
    D3 --> Triage
    T1 --> T2 --> T3
    T3 --> C1 --> C2 --> C3
    C1 --> A1 --> A2 --> A3
    A3 --> R1 --> R2 --> R3
    
    %% Note: Golden rule preserve evidence first
    %% Note: Response phases contain eradicate recover
    
    style Detection fill:#ffebee
    style Triage fill:#fff3e0
    style Collection fill:#f3e5f5
    style Analysis fill:#e8f5e9
    style Response fill:#e1f5fe
