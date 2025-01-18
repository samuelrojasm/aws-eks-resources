# Guía de Configuración de EKS (Amazon Elastic Kubernetes Service)
Esta guía proporciona los pasos necesarios para configurar un clúster de Amazon EKS. Se cubrirán los siguientes aspectos:
- Requisitos previos
- Configuración de EKS usando la consola de AWS
- Configuración de EKS usando AWS CLI
- Configuración de EKS usando Terraform
- Acceso al clúster con kubectl
- Verificación del clúster y primeros pasos
- Consideraciones de costos y buenas prácticas

# Requisios previos
## Opciones para crear el cluser de AWS EKS
- eksctl
- AWS Management Console
- AWS CLI

# Creación del cluser con eksctl
- Este ejemplo muestra la creación de clústeres mediante **eksctl**, una herramienta CLI para crear clústeres en Amazon EKS mediante AWS CloudFormation. 
- Weaveworks ha desarrollado eksctl como una sencilla utilidad de línea de comandos para crear y administrar clústeres de Kubernetes en Amazon EKS.

## ¿Qué hace eksctl (por default)?
eksctl automatiza muchos de los pasos necesarios para la creación de clústeres y nodos de trabajo (worker node). A continuación se muestra un resumen de las tareas realizadas por eksctl cuando se ejecuta con la opción predeterminada:
1. Creates IAM roles for the cluster and worker nodes.
2. Creates a dedicated VPC with Classless Inter-Domain Routing (CIDR) 192.168.0.0/16.
3. Creates a cluster and a nodegroup.
4. Configures access to API endpoints.
5. Installs CoreDNS.
6. Writes a kubeconfig file for the cluster.
## Proceso
1. Install eksctl
- Instalación en MacOs
``` 
brew tap weaveworks/tap
brew install weaveworks/tap/eksctl
eksctl version
```

## Referencias
- [The official CLI for Amazon EKS](https://eksctl.io)
- [Getting started with Amazon EKS – eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)
