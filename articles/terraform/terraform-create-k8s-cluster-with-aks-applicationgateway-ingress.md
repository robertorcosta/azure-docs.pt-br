---
title: Criar um controlador de entrada do Gateway de Aplicativo no AKS (Serviço de Kubernetes do Azure)
description: Tutorial que ilustra como criar um cluster Kubernetes com o Serviço de Kubernetes do Azure usando o Gateway de Aplicativo como controlador de entrada
services: terraform
ms.service: azure
keywords: terraform, devops, máquina virtual, azure, kubernetes, entrada, gateway de aplicativo
author: tomarcher
manager: gwallace
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/09/2019
ms.openlocfilehash: b156169e7202319366e337cc7081e02f5de3acad
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244801"
---
# <a name="create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Criar um controlador de entrada do Gateway de Aplicativo no Serviço de Kubernetes do Azure

O [AKS (Serviço de Kubernetes do Azure)](/azure/aks/) gerencia seu ambiente Kubernetes hospedado. O AKS acelera e facilita a implantação e o gerenciamento de aplicativos em contêineres sem conhecimento de orquestração de contêineres. O AKS também elimina a carga de colocar aplicativos offline para executar tarefas operacionais e de manutenção. Usando o AKS, essas tarefas – incluindo o provisionamento, a atualização e o dimensionamento de recursos – podem ser executadas sob demanda.

Um controlador de entrada fornece diversos recursos para serviços do Kubernetes. Esses recursos incluem proxy reverso, roteamento de tráfego configurável e encerramento de TLS. Os recursos de entrada do Kubernetes são usados para configurar as regras de entrada para os serviços do Kubernetes individuais. Usando um controlador de entrada e regras de entrada, um único endereço IP pode rotear o tráfego para vários serviços em um cluster do Kubernetes. Todas essas funcionalidades são fornecidas pelo [Gateway de Aplicativo](/azure/Application-Gateway/) do Azure, fazendo dele o controlador de entrada ideal para o Kubernetes no Azure. 

Neste tutorial, você aprenderá a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar um cluster do [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) usando o AKS com o Gateway de Aplicativo como Controlador de Entrada.
> * Usar HCL (HashiCorp Language) para definir um cluster do Kubernetes.
> * Usar o Terraform para criar um recurso do Gateway de Aplicativo.
> * Usar o Terraform e o AKS para criar um cluster do Kubernetes.
> * Usar a ferramenta kubectl para testar a disponibilidade de um cluster do Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Configurar o Terraform**: Siga as instruções no artigo [Terraform e configurar o acesso ao Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Entidade de serviço do Azure**: siga as instruções na seção **Criar a entidade de serviço** do artigo [Criar uma entidade de serviço do Azure com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Anote os valores para de appId, displayName e senha.

- **Obter a ID de objeto da entidade de serviço**: execute o seguinte comando no Cloud Shell: `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>Crie a estrutura de diretórios

O primeiro passo é criar o diretório que contém os arquivos de configuração do Terraform para o exercício.

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Altere os diretórios para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Altere os diretórios para o novo diretório:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Declarar o provedor do Azure

Crie o arquivo de configuração Terraform que declara o provedor do Azure.

1. No Cloud Shell, crie um arquivo chamado `main.tf`.

    ```bash
    code main.tf
    ```

1. Cole o código a seguir no editor:

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="define-input-variables"></a>Definir variáveis de entrada

Crie o arquivo de configuração do Terraform que lista todas as variáveis necessárias para esta implantação.

1. No Cloud Shell, crie um arquivo chamado `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Cole o código a seguir no editor:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group already created."
    }

    variable "location" {
      description = "Location of the cluster."
    }

    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }

    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }

    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }

    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }

    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }

    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }

    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }

    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }

    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }

    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }

    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }

    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
      default     = 40
    }

    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }

    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }

    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }

    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }

    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }

    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }

    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }

    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }

    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }

    variable "tags" {
      type = "map"

      default = {
        source = "terraform"
      }
    }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="define-the-resources"></a>Definir os recursos 
Crie um arquivo de configuração do Terraform que cria todos os recursos. 

1. No Cloud Shell, crie um arquivo chamado `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Cole o seguinte bloco de códigos para criar um bloco de locais para ser reutilizado pelas variáveis computadas:

    ```hcl
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```

1. Cole o seguinte bloco de códigos para criar uma fonte de dados para o grupo de recursos, com uma nova identidade do Usuário:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }

    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"

      name = "identity1"

      tags = "${var.tags}"
    }
    ```

1. Cole o seguinte bloco de códigos para criar os recursos de rede de base:

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]

      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }

      tags = "${var.tags}"
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"

      tags = "${var.tags}"
    }
    ```

1. Cole o seguinte bloco de códigos para criar o recurso do Gateway de Aplicativo:

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"

      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }

      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }

      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }

      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }

      tags = "${var.tags}"

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. Cole o seguinte bloco de códigos para criar as atribuições de função:

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Cole o seguinte bloco de códigos para criar o cluster do Kubernetes:

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"

      resource_group_name = "${data.azurerm_resource_group.rg.name}"

      linux_profile {
        admin_username = "${var.vm_user_name}"

        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }

      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
    }

    ```

1. Salve o arquivo e saia do editor.

O código apresentado nesta seção define o nome do cluster, a localização e o resource_group_name. O valor `dns_prefix`, que faz parte do FQDN (nome de domínio totalmente qualificado) usado para acessar o cluster, é definido.

O registro `linux_profile` permite que você defina as configurações que habilitam a conexão aos nós de trabalho usando SSH.

Com o AKS, você paga apenas pelos nós do trabalhador. O registro `agent_pool_profile` configura os detalhes para esses nós de trabalho. O `agent_pool_profile record` inclui o número de nós de trabalho a serem criados e o tipo desses nós. Se você precisar aumentar ou reduzir verticalmente o cluster no futuro, modifique o valor de `count` neste registro.

## <a name="create-a-terraform-output-file"></a>Crie um modelo do Terraform

As [saídas do Terraform](https://www.terraform.io/docs/configuration/outputs.html) permitem definir valores que são destacados para o usuário quando o Terraform aplica um plano e podem ser consultadas usando o comando `terraform output`. Nesta seção, você cria um arquivo de saída que permite acesso ao cluster com [ kubectl ](https://kubernetes.io/docs/reference/kubectl/overview/).

1. No Cloud Shell, crie um arquivo chamado `output.tf`.

    ```bash
    code output.tf
    ```

1. Cole o código a seguir no editor:

    ```hcl
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }

    output "identity_resource_id" {
        value = "${azurerm_user_assigned_identity.testIdentity.id}"
    }

    output "identity_client_id" {
        value = "${azurerm_user_assigned_identity.testIdentity.client_id}"
    }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Configurar o armazenamento do Azure para armazenar o estado do Terraform

Terraform rastreia o estado localmente através do arquivo `terraform.tfstate`. Esse padrão funciona bem em um ambiente de uma única pessoa. No entanto, em um ambiente mais prático com várias pessoas, você precisa rastrear o estado no servidor utilizando o [armazenamento do Azure](/azure/storage/). Nesta seção, você aprende a recuperar as informações de conta de armazenamento necessárias e a criar um contêiner de armazenamento. As informações de estado do Terraform são armazenadas nesse contêiner.

1. No portal do Azure, selecione **Todos os serviços** no menu à esquerda.

1. Selecione **Contas de armazenamento**.

1. Na guia **Contas de armazenamento**, selecione o nome da conta de armazenamento na qual o Terraform deve armazenar o estado. Por exemplo, você pode usar a conta de armazenamento criada quando você abriu o Cloud Shell pela primeira vez.  O nome da conta de armazenamento criada pelo Cloud Shell geralmente começa com `cs` seguido por uma sequência aleatória de números e letras. 

    Tome nota da conta de armazenamento selecionada, pois ela será necessária mais tarde.

1. Na guia da conta de armazenamento, selecione **Chaves de acesso**.

    ![Menu da conta de armazenamento](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Anote o valor da **key1** **key**. (Selecionar o ícone à direita da chave copia o valor para a área de transferência.)

    ![Chaves de acesso da conta de armazenamento](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. No Cloud Shell, crie um contêiner em sua conta de armazenamento do Azure (substitua os espaços reservados &lt; YourAzureStorageAccountName> e &lt; YourAzureStorageAccountAccessKey> pelos valores apropriados para sua conta de armazenamento do Azure).

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Crie o cluster do Kubernetes
Nesta seção, você verá como usar o comando `terraform init` para criar os recursos definidos nos arquivos de configuração criados nas seções anteriores.

1. No Cloud Shell, inicialize o Terraform (substitua os espaços reservados &lt; YourAzureStorageAccountName> e &lt; YourAzureStorageAccountAccessKey> pelos valores apropriados para sua conta de armazenamento do Azure).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    O comando `terraform init` exibe o sucesso da inicialização do plug-in de backend e provedor:

    ![Exemplo de resultados de "terraform init"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. No Cloud Shell, crie um arquivo chamado `main.tf`:

    ```bash
    code terraform.tfvars
    ```

1. Cole as seguintes variáveis criadas anteriormente no editor:

    ```hcl
    resource_group_name = <Name of the Resource Group already created>

    location = <Location of the Resource Group>
      
    aks_service_principal_app_id = <Service Principal AppId>
      
    aks_service_principal_client_secret = <Service Principal Client Secret>
      
    aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

1. Execute o comando `terraform plan` para criar o plano Terraform que define os elementos da infraestrutura. 

    ```bash
    terraform plan -out out.plan
    ```

    O comando `terraform plan` exibe os recursos que são criados quando você executa o comando `terraform apply`:

    ![Exemplo de resultados do "plano de terraform"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Execute o comando `terraform apply` para aplicar o plano para criar o cluster do Kubernetes. O processo para criar um cluster do Kubernetes pode levar vários minutos, resultando no tempo limite da sessão do Cloud Shell. Se a sessão do Cloud Shell expirar, siga as etapas da seção "Recuperação de um tempo limite do Cloud Shell" para permitir que você conclua o tutorial.

    ```bash
    terraform apply out.plan
    ```

    O comando `terraform apply` exibe os resultados da criação dos recursos definidos em seus arquivos de configuração:

    ![Exemplo de resultados de "terraform apply"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. No portal do Azure, selecione **Grupos de Recursos** no menu à esquerda para ver os recursos criados para o novo cluster do Kubernetes no grupo de recursos selecionado.

    ![Aviso do Cloud Shell](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Recuperar de um limite de tempo do Cloud Shell

Se a sessão do Cloud Shell expirar, você poderá executar as seguintes etapas para recuperar:

1. Inicie uma sessão do Cloud Shell.

1. Mude para o diretório que contém os arquivos de configuração do Terraform.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Execute o comando a seguir:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
  
## <a name="test-the-kubernetes-cluster"></a>Teste o cluster do Kubernetes
As ferramentas do Kubernetes podem ser usadas para verificar o cluster recém-criado.

1. Obtenha a configuração do Kubernetes do estado Terraform e armazene-a em um arquivo que o kubectl possa ler.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Obtenha a configuração do Kubernetes do estado Terraform e armazene-a em um arquivo que o kubectl possa ler.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Verifique a integridade do cluster.

    ```bash
    kubectl get nodes
    ```

    Você deve ver os detalhes de seus nós de trabalho e todos devem ter um status **Pronto**, conforme mostrado na imagem a seguir:

    ![A ferramenta kubectl permite verificar a integridade do seu cluster do Kubernetes](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)

## <a name="install-azure-ad-pod-identity"></a>Instalar a identidade de Pod do Azure AD

A Identidade de Pod do Azure Active Directory fornece acesso baseado em token ao [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

A [Identidade de Pod do Azure AD](https://github.com/Azure/aad-pod-identity) adiciona os seguintes componentes ao seu cluster do Kubernetes:

  - [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) do Kubernetes: `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
  - Componente do [MIC (Controlador de Identidades Gerenciadas)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - Componente de [NMI (Identidade Gerenciada do Nó)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Se o RBAC estiver **habilitado**, execute o seguinte comando para instalar a Identidade de Pod do Azure AD em seu cluster:

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
    ```

Se o RBAC estiver **desabilitado**, execute o seguinte comando para instalar a Identidade de Pod do Azure AD em seu cluster:

    ```bash
    kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
    ```

## <a name="install-helm"></a>Instalar o Helm

O código nesta seção usa o [Helm](/azure/aks/kubernetes-helm), o gerenciador de pacotes do Kubernetes, para instalar o pacote `application-gateway-kubernetes-ingress`:

1. Se o RBAC estiver **habilitado**, execute o seguinte conjunto de comandos para instalar e configurar o Helm:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Se o RBAC estiver **desabilitado**, execute o seguinte comando para instalar e configurar o Helm:

    ```bash
    helm init
    ```

1. Adicione o repositório AGIC do Helm:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Instalar o Gráfico do Controlador de Entrada do Helm

1. Baixe `helm-config.yaml` para configurar o AGIC:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Edite o `helm-config.yaml` e insira os valores apropriados para as seções `appgw` e `armAuth`.

    ```bash
    nano helm-config.yaml
    ```

    Os valores são descritos da seguinte maneira:

    - `verbosityLevel`: define o nível de detalhamento da infraestrutura de registro em log do AGIC. Consulte [Níveis de registro em log](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) para obter os valores possíveis.
    - `appgw.subscriptionId`: a ID de assinatura do Azure para o Gateway de Aplicativo. Exemplo: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: nome do Grupo de Recursos do Azure no qual o Gateway de Aplicativo foi criado. 
    - `appgw.name`: nome do Gateway de Aplicativo. Exemplo: `applicationgateway1`.
    - `appgw.shared`: esse sinalizador booliano deve ter `false` como valor padrão. Defina como `true` caso precise de um [Gateway de Aplicativo Compartilhado](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
    - `kubernetes.watchNamespace`: especifique o namespace que o AGIC deve inspecionar. O namespace pode ser um único valor de cadeia de caracteres ou uma lista de namespaces separados por vírgula.
    - `armAuth.type`: um valor de `aadPodIdentity` ou `servicePrincipal`.
    - `armAuth.identityResourceID`: ID de recurso da identidade gerenciada.
    - `armAuth.identityClientId`: a ID do cliente da identidade.
    - `armAuth.secretJSON`: necessário somente quando o tipo de Segredo da Entidade de Serviço é escolhido (quando `armAuth.type` foi definido como `servicePrincipal`).

    Observações importantes:
    - O valor de `identityResourceID` é criado no script do Terraform e pode ser encontrado executando: `echo "$(terraform output identity_client_id)"`.
    - O valor de `identityClientID` é criado no script do Terraform e pode ser encontrado executando: `echo "$(terraform output identity_resource_id)"`.
    - O valor de `<resource-group>` é o grupo de recursos de seu Gateway de Aplicativo.
    - O valor de `<identity-name>` é o nome da identidade criada.
    - Todas as identidades de uma determinada assinatura podem ser listadas usando: `az identity list`.

1. Instale o pacote do controlador de entrada do Gateway de Aplicativo:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Instalar um aplicativo de exemplo

Quando tiver o Gateway de Aplicativo, o AKS e o AGIC instalados, você poderá instalar um aplicativo de exemplo usando o [Azure Cloud Shell](https://shell.azure.com/):

1. Use o comando de ondulação para baixar o arquivo YAML:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Aplique o arquivo YAML:

    ```bash
    kubectl apply -f apsnetapp.yaml
    ```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o Terraform e o AKS para criar um cluster do Kubernetes. A seguir são apresentados alguns recursos adicionais para ajudá-lo a saber mais sobre o Terraform no Azure.
 
 > [!div class="nextstepaction"] 
  > [Controlador de Entrada do Gateway de Aplicativo](https://azure.github.io/application-gateway-kubernetes-ingress/)