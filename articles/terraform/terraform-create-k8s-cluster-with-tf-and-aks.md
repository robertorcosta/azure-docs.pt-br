---
title: Tutorial – Criar um cluster do Kubernetes com o AKS (Serviço de Kubernetes do Azure) usando o Terraform
description: Neste tutorial, você criará um cluster do Kubernetes com o Serviço de Kubernetes do Azure e o Terraform
keywords: azure devops terraform aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 0a193c8da6441a04f742894797521fe92f26b2e1
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945302"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Tutorial: Criar um cluster do Kubernetes com o Serviço de Kubernetes do Azure usando o Terraform

O [AKS (Serviço de Kubernetes do Azure)](/azure/aks/) gerencia seu ambiente Kubernetes hospedado. O AKS permite a implantação e o gerenciamento de aplicativos em contêineres sem conhecimento sobre orquestração de contêineres. O AKS também permite que você execute muitas operações de manutenção comuns sem colocar seu aplicativo offline. Essas operações incluem provisionamento, atualização e dimensionamento de recursos sob demanda.

Neste tutorial, você aprenderá a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Use HCL (HashiCorp Language) para definir um cluster do Kubernetes
> * Use Terraform e AKS para criar um cluster Kubernetes
> * Use a ferramenta kubectl para testar a disponibilidade de um cluster do Kubernetes

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Configurar o Terraform**: Siga as instruções no artigo [Terraform e configurar o acesso ao Azure](terraform-install-configure.md)

- **Entidade de serviço do Azure**: siga as instruções na seção **Criar a entidade de serviço** do artigo [Criar uma entidade de serviço do Azure com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Anote os valores para o appId, displayName, senha e inquilino.

## <a name="create-the-directory-structure"></a>Crie a estrutura de diretórios

O primeiro passo é criar o diretório que contém os arquivos de configuração do Terraform para o exercício.

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se você não selecionou um ambiente anteriormente, selecione **Bash** como o ambiente.

    ![Aviso do Cloud Shell](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Altere os diretórios para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Altere os diretórios para o novo diretório:

    ```bash
    cd terraform-aks-k8s
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
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="define-a-kubernetes-cluster"></a>Definir um cluster do Kubernetes

Crie o arquivo de configuração do Terraform que declara os recursos para o cluster do Kubernetes.

1. No Cloud Shell, crie um arquivo chamado `k8s.tf`.

    ```bash
    code k8s.tf
    ```

1. Cole o código a seguir no editor:

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_DS1_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    O código anterior define o nome do cluster, a localização e o nome do grupo de recursos. O prefixo do FQDN (nome de domínio totalmente qualificado) também é definido. O FQDN é usado para acessar o cluster.

    O registro `linux_profile` permite que você defina as configurações que habilitam a conexão aos nós de trabalho usando SSH.

    Com o AKS, você paga apenas pelos nós do trabalhador. O registro `default_node_pool` configura os detalhes para esses nós de trabalho. O `default_node_pool record` inclui o número de nós de trabalho a serem criados e o tipo desses nós. Se você precisar aumentar ou reduzir verticalmente o cluster no futuro, modifique o valor de `count` neste registro.

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="declare-the-variables"></a>Declarar as variáveis

1. No Cloud Shell, crie um arquivo chamado `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Cole o código a seguir no editor:

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="create-a-terraform-output-file"></a>Crie um modelo do Terraform

[ As saídas do Terraform ](https://www.terraform.io/docs/configuration/outputs.html) permitem definir valores que serão destacados para o usuário quando o Terraform aplica um plano, e podem ser consultados usando o comando `terraform output`. Nesta seção, você cria um arquivo de saída que permite acesso ao cluster com [ kubectl ](https://kubernetes.io/docs/reference/kubectl/overview/).

1. No Cloud Shell, crie um arquivo chamado `output.tf`.

    ```bash
    code output.tf
    ```

1. Cole o código a seguir no editor:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Configurar o armazenamento do Azure para armazenar o estado Terraform

Terraform rastreia o estado localmente através do arquivo `terraform.tfstate`. Esse padrão funciona bem em um ambiente de uma única pessoa. Em um ambiente de várias pessoas, o [Armazenamento do Azure](/azure/storage/) é usado para controlar o estado.

Nesta seção, você verá como executar as seguintes tarefas:
- Recuperar dados da conta de armazenamento (nome e chave de conta)
- Criar um contêiner de armazenamento no qual as informações de estado do Terraform serão armazenadas.

1. No portal do Azure, selecione **Todos os serviços** no menu à esquerda.

1. Selecione **Contas de armazenamento**.

1. Na guia **Contas de armazenamento**, selecione o nome da conta de armazenamento na qual o Terraform deve armazenar o estado. Por exemplo, você pode usar a conta de armazenamento criada quando você abriu o Cloud Shell pela primeira vez.  O nome da conta de armazenamento criada pelo Cloud Shell geralmente começa com `cs` seguido por uma sequência aleatória de números e letras. Anote a conta de armazenamento selecionada. Esse valor será necessário mais tarde.

1. Na guia da conta de armazenamento, selecione **Chaves de acesso**.

    ![Menu da conta de armazenamento](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Anote o valor da **chave1** **chave**. (Selecionar o ícone à direita da chave copia o valor para a área de transferência.)

    ![Chaves de acesso da conta de armazenamento](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. No Cloud Shell, crie um contêiner em sua conta de armazenamento do Azure. Substitua os espaços reservados pelos valores adequados do seu ambiente.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Crie o cluster do Kubernetes

Nesta seção, você verá como usar o comando `terraform init` para criar os recursos definidos nos arquivos de configuração criados nas seções anteriores.

1. No Cloud Shell, inicialize o Terraform. Substitua os espaços reservados pelos valores adequados do seu ambiente.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    O comando `terraform init` exibe o êxito da inicialização do plug-in de back-end e de provedor:

    ![Exemplo de resultados de "terraform init"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exporte as credenciais da entidade de serviço. Substitua os espaços reservados pelos valores apropriados na entidade de serviço.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Execute o comando `terraform plan` para criar o plano Terraform que define os elementos da infraestrutura. 

    ```bash
    terraform plan -out out.plan
    ```

    O comando `terraform plan` exibe os recursos que serão criados quando você executar o comando `terraform apply`:

    ![Exemplo de resultados do "plano de terraform"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Execute o comando `terraform apply` para aplicar o plano para criar o cluster do Kubernetes. O processo para criar um cluster do Kubernetes pode levar vários minutos, resultando no tempo limite da sessão do Cloud Shell. Se a sessão do Cloud Shell expirar, siga as etapas da seção "Recuperação de um tempo limite do Cloud Shell" para permitir que você conclua o tutorial.

    ```bash
    terraform apply out.plan
    ```

    O comando `terraform apply` exibe os resultados da criação dos recursos definidos em seus arquivos de configuração:

    ![Exemplo de resultados de "terraform apply"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. No portal do Azure, selecione **Todos os recursos** no menu à esquerda para ver os recursos criados para o novo cluster do Kubernetes.

    ![Todos os recursos no portal do Azure](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Recuperar de um limite de tempo do Cloud Shell

Se a sessão do Cloud Shell atingir o tempo limite, execute as seguintes etapas para recuperá-la:

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

    ![A ferramenta kubectl permite verificar a integridade do seu cluster do Kubernetes](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Monitorar integridade e logs

Quando o cluster do AKS foi criado, o monitoramento foi habilitado para capturar métricas de integridade para os nós de cluster e os pods. Essas métricas de integridade estão disponíveis no portal do Azure. Para saber mais sobre o monitoramento de integridade do contêiner, confira [Monitoramento da Integridade do Serviço de Kubernetes do Azure](/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)