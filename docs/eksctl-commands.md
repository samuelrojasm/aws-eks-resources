# Comandos de eksctl
Este archivo contiene una lista organizada de comandos útiles de ekscl para gestionar un clúster AWS EKS en diferentes etapas del ciclo de vida del clúster. Los comandos están categorizados para facilitar su uso.

## Comandos Básicos
### Creación del clúster
- `eksctl create cluster` *(Creación del clúster)*
## Comandos de Administración y Mantenimiento
### Eliminar recursos
- `eksctl delete nodegroup --cluster=${clusterName}  --name=${nodegroupName}`  *(Borra un nodegroup)*
## Gestión de Recursos de Kubernetes
### Gestionar fargate
- `eksctl get fargateprofile --cluster ${clusterName}` *(Get the Fargate profile of the cluster)*