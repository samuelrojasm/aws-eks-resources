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
- Este ejemplo muestra la creación de clústeres mediante eksctl, una herramienta CLI para crear clústeres en Amazon EKS mediante AWS CloudFormation. 
- Weaveworks ha desarrollado eksctl como una sencilla utilidad de línea de comandos para crear y administrar clústeres de Kubernetes en Amazon EKS.
[The official CLI for Amazon EKS](https://eksctl.io)
