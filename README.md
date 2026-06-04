# SignalForge-Azure-Infra

# Infrastructure Architecture

+---------------------------------------------------------------------------------+
|                                 AZURE VIRTUAL NETWORK (VNet)                    |
|                                                                                 |
|  +--------------------+   +-----------------------+   +----------------------+  |
|  |   Ingress Subnet   |   |   AKS Nodes Subnet    |   | Private Endpoints    |  |
|  |                    |   |                       |   | Subnet               |  |
|  | [Azure Application |-->| [System Node Pool]    |-->| [Private Endpoint]   |  |
|  |  Gateway / AGIC]   |   | [User Node Pool (Spot]|   |  to Key Vault / DB   |  |
|  +--------------------+   +-----------------------+   +----------------------+  |
|            |                          |                          ^              |
|            | (Public HTTPS)           | (eBPF Cilium Policies)   | Secure Link  |
|            v                          v                          |              |
|     [Internet Users]          [ACR (AcrPull Access)] ------------+              |
+---------------------------------------------------------------------------------+
