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
2. Ejemplo de creación del cluster
``` 
eksctl create cluster -f ./prod-cluster-config.yaml
``` 
3. Verificar el estado los nodos
``` 
kubectl get nodes
``` 
### Declarative or Imperative
1. You can create a configuration file for use with eksctl (modo Declarativo)
``` 
eksctl create cluster -f cluster.yaml
``` 
2. You can customize the command by adding arguments
```
eksctl create cluster \
  --name <nombre_del_clúster> \
  --version <version> \
  --region <región> \
  --nodegroup-name <nombre_del_grupo_de_nodos> \
  --node-type <tipo_de_instancia> \
  --nodes <número_de_nodos> \
  --nodes-min <número_mínimo_de_nodos> \
  --nodes-max <número_máximo_de_nodos> \
  --managed
```
Ejemplo de modo imperativo para crear un clúster de Amazon EKS:
```
eksctl create cluster \
  --name mi-cluster-eks \
  --version 1.19 \
  --region us-west-2 \
  --nodegroup-name grupo-nodos-1 \
  --node-type t3.medium \
  --nodes 3 \
  --nodes-min 2 \
  --nodes-max 5 \
  --managed
```
#### Customize eksctl with a configuration file
- Como se ha revisado anteriormente, es posible crear un clúster utilizando un archivo de configuración en formato YAML en lugar de banderas, lo que da más control sobre las opciones seleccionadas. 
- El uso de un archivo de configuración también hace que el proceso de creación de un clúster sea **declarativo** en lugar de **imperativo**. 
- **eksctl** traduce las instrucciones del archivo de configuración a plantillas equivalentes de CloudFormation. Es posible poner los archivos de configuración en control de código fuente igual que como se hace con otros activos como plantillas de CloudFormation o archivos de automatización de Terraform.


## Referencias
- [The official CLI for Amazon EKS](https://eksctl.io)
- [Getting started with Amazon EKS – eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)
