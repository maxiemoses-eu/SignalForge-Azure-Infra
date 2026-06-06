# SignalForge-Azure-Infra

### Infrastructure Topology (Azure VNet)

```mermaid
graph TD
    %% Define Styles and Themes
    classDef internet fill:#eceff1,stroke:#37474f,stroke-width:2px,color:#000;
    classDef subnet fill:#f5f5f5,stroke:#9e9e9e,stroke-width:2px,stroke-dasharray: 5 5,color:#333;
    classDef component fill:#e1f5fe,stroke:#0288d1,stroke-width:2px,color:#01579b;
    classDef secure fill:#efebe9,stroke:#5d4037,stroke-width:2px,color:#3e2723;

    %% Elements outside the VNet
    Users(["🌐 Internet Users"]):::internet

    %% Virtual Network Boundary
    subgraph VNet ["☁️ Azure Virtual Network (VNet)"]
        
        subgraph IngressSubnet ["📂 Ingress Subnet"]
            AGW["🛡️ Azure Application Gateway<br>(AGIC)"]:::component
        end

        subgraph AKSSubnet ["📂 AKS Nodes Subnet"]
            SNP["⚙️ System Node Pool<br>(Core Services)"]:::component
            UNP["💰 User Node Pool<br>(Spot Instances)"]:::component
        end

        subgraph PrivateSubnet ["📂 Private Endpoints Subnet"]
            PE["🔒 Private Endpoint<br>(Key Vault / DB)"]:::secure
        end

    end

    %% External Registry
    ACR["📦 Azure Container Registry<br>(ACR)"]:::component

    %% Traffic and Data Flows
    Users -->|Public HTTPS| AGW
    AGW -->|Route Traffic| SNP
    AGW -->|Route Traffic| UNP
    
    %% AKS Internal Networking & Outbound
    SNP & UNP -->|eBPF Cilium Policies| ACR
    SNP & UNP ==>|Secure Link| PE
    ACR -.->|AcrPull Access| PE

    %% Apply cluster styles
    style VNet fill:#ffffff,stroke:#0078d4,stroke-width:3px,color:#0078d4;