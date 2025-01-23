# Guía de Configuración de EKS (Amazon Elastic Kubernetes Service)
Esta guía proporciona los pasos necesarios para configurar un clúster de Amazon EKS. Se cubrirán los siguientes aspectos:
- Requisitos previos
- Configuración de EKS usando la consola de AWS
- Configuración de EKS usando AWS CLI
- Configuración de EKS usando Terraform
- Acceso al clúster con kubectl
- Verificación del clúster y primeros pasos
- Consideraciones de costos y buenas prácticas

## Opciones para crear el cluser de AWS EKS
1. eksctl
2. AWS Management Console
3. AWS Command Line Interface (AWS CLI)
4. Infraestructura como Código (IaC)

## 1. Creación del cluser con eksctl
- Este ejemplo muestra la creación de clústeres mediante **eksctl**, una herramienta CLI para crear clústeres en Amazon EKS mediante AWS CloudFormation. 
- Weaveworks ha desarrollado eksctl como una sencilla utilidad de línea de comandos para crear y administrar clústeres de Kubernetes en Amazon EKS.

### ¿Qué hace eksctl (por default)?
eksctl automatiza muchos de los pasos necesarios para la creación de clústeres y nodos de trabajo (worker node). A continuación se muestra un resumen de las tareas realizadas por eksctl cuando se ejecuta con la opción predeterminada:
1. Creates IAM roles for the cluster and worker nodes.
2. Creates a dedicated VPC with Classless Inter-Domain Routing (CIDR) 192.168.0.0/16.
3. Creates a cluster and a nodegroup.
4. Configures access to API endpoints.
5. Installs CoreDNS.
6. Writes a kubeconfig file for the cluster.
### Proceso creación de cluster con eksctl
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
### Modos Declarativo e Imperativo
1. You can create a configuration file for use with eksctl (modo Declarativo)
``` 
eksctl create cluster -f cluster.yaml
``` 
2. You can customize the command by adding arguments (modo Imperativo)
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
- Este archivo define de forma declarativa cómo debe ser el clúster de EKS:
```
# archivo: cluster-config-with-subnet-ids.yaml

apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
  name: mi-cluster-eks
  region: us-west-2
vpc:
  id: ""  # Deja vacío para crear una nueva VPC
  subnets:
    private:
      us-west-2a:
        id: subnet-xxxxxxx  # ID de la subred privada en la zona us-west-2a
      us-west-2b:
        id: subnet-yyyyyyy  # ID de la subred privada en la zona us-west-2b
    public:
      us-west-2a:
        id: subnet-aaaaaaa  # ID de la subred pública en la zona us-west-2a
      us-west-2b:
        id: subnet-bbbbbbb  # ID de la subred pública en la zona us-west-2b
nodeGroups:
  - name: grupo-nodos-1
    instanceType: t3.medium
    desiredCapacity: 3
    minSize: 2
    maxSize: 5
    privateNetworking: true  # Los nodos estarán en las subredes privadas
```
## 2. Creación del cluser con AWS Management Console
La consola de AWS proporciona un flujo interactivo para crear un clúster EKS.
### Paso 1: Crear un clúster EKS
1. Inicia sesión en la Consola de AWS.
2. Ir a EKS (Elastic Kubernetes Service) en el panel de servicios de AWS.
3. Clic en Create Cluster.
4. Completar detalles del clúster:
    - **Cluster name:** Asigna un nombre a tu clúster.
    - **Role ARN:** Crea o selecciona un rol IAM con permisos necesarios para EKS.
    - **VPC:** Selecciona la VPC para el clúster.
    - **Subnets:** Desplegar el clúster en subredes públicas o privadas dependiendo de las necesidades.
5. Clic en Create.
### Paso 2: Crear el grupo de nodos EKS
1. Una vez creado el clúster, navegar a la pestaña Node Groups.
2. Clic en **Add Node Group**.
3. Completar los detalles del grupo de nodos, incluyendo:
    - **Node group name:** Un nombre para el grupo de nodos.
    - **Node IAM role:** Crear o seleccionar un rol de IAM con permisos necesarios para el nodo EKS (comúnmente un rol con permisos AmazonEKSWorkerNodePolicy).
    - **Instance types:** Elige tipos de instancias EC2 para tus nodos (por ejemplo, t3.micro para pruebas).
4. Configurar las subnet y scaling options.
5. Clic en Create para crear el grupo de nodos. 

## 3. Creación del cluser usando AWS CLI
### Paso 1: Crear el clúster
```
aws eks create-cluster \
  --name <cluster-name> \
  --role-arn arn:aws:iam::<account-id>:role/<role-name> \
  --resources-vpc-config subnetIds=<subnet-ids>,securityGroupIds=<security-group-ids>
```
- **cluster-name:** El nombre del clúster EKS.
- **role-arn:** El ARN del rol IAM que permite a EKS gestionar los recursos de AWS (debe tener permisos como *AmazonEKSClusterPolicy*).
- **subnetIds:** Los IDs de las subredes donde se desplegará el clúster.
- **securityGroupIds:** Los grupos de seguridad que permitirán la comunicación del clúster.
### Paso 2: Configurar los nodos
```
aws eks create-nodegroup \
  --cluster-name <cluster-name> \
  --nodegroup-name <nodegroup-name> \
  --node-role arn:aws:iam::<account-id>:role/<role-name> \
  --subnets <subnet-ids> \
  --instance-types <instance-type> \
  --scaling-config minSize=1,maxSize=3,desiredSize=2
```

## Creación del cluster con (IaC)
Es posible usar **Terraform** para gestionar infraestructura, a continuación un ejemplo básico de configuración para crear un clúster EKS:
### Paso 1: Crear un archivo main.tf
Este archivo crea el clúster EKS y el grupo de nodos usando Terraform.
```
provider "aws" {
  region = "us-west-2"
}

resource "aws_eks_cluster" "eks-cluster" {
  name     = "test-cluster"
  role_arn = aws_iam_role.eks_role.arn

  vpc_config {
    subnet_ids = [aws_subnet.subnet.id]
  }
}

resource "aws_iam_role" "eks_role" {
  name = "eks-role"
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Action = "sts:AssumeRole"
      Effect = "Allow"
      Principal = {
        Service = "eks.amazonaws.com"
      }
    }]
  })
}

resource "aws_eks_node_group" "eks-node-group" {
  cluster_name    = aws_eks_cluster.eks-cluster.name
  node_group_name = "eks-node-group"
  node_role_arn   = aws_iam_role.node_role.arn
  subnet_ids      = [aws_subnet.subnet.id]

  scaling_config {
    desired_size = 2
    max_size     = 3
    min_size     = 1
  }

  instance_types = ["t3.micro"]
}
```
### Paso 2: Ejecutar los comandos de Terraform
Esto creará el clúster EKS en AWS.
```
terraform init
terraform apply
```


## Referencias
- [The official CLI for Amazon EKS](https://eksctl.io)
- [Getting started with Amazon EKS – eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)
