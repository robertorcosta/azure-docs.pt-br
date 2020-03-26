---
title: Início Rápido – Instalar e configurar o Terraform para provisionar recursos do Azure
description: Neste início rápido, você instalará e configurará o Terraform para criar recursos do Azure
keywords: azure devops terraform instalar configurar
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 82635f59ec8165add2046a230a040b06f89d9898
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78943512"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Início Rápido: Instalar e configurar o Terraform para provisionar recursos do Azure
 
O Terraform fornece uma maneira fácil de definir, visualizar e implantar a infraestrutura de nuvem usando uma [linguagem de modelagem simples](https://www.terraform.io/docs/configuration/syntax.html). Este artigo descreve as etapas necessárias para usar o Terraform para provisionar recursos no Azure.

Para saber mais sobre como usar Terraform com o Azure, visite o [Terraform Hub](/azure/terraform).
> [!NOTE]
> Para obter suporte específico do Terraform, entre em contato com o Terraform diretamente usando um dos canais da comunidade:
>
>    * A [seção Terraform](https://discuss.hashicorp.com/c/terraform-core) do portal da comunidade contém perguntas, casos de uso e padrões úteis.
>
>    * Para perguntas relacionadas ao provedor, acesse a seção [Provedores Terraform](https://discuss.hashicorp.com/c/terraform-providers) do portal da comunidade.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

O Terraform é instalado por padrão no [Cloud Shell](/azure/terraform/terraform-cloud-shell). Se você optar por instalar o Terraform localmente, conclua a próxima etapa, caso contrário, continue até [Configurar o acesso do Terraform ao Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Instalar o Terraform

Para instalar o Terraform, [baixe](https://www.terraform.io/downloads.html) o pacote apropriado para o seu sistema operacional em um diretório de instalação separado. O download contém um único arquivo executável, para o qual também deve definir um caminho global. Para obter instruções sobre como definir o caminho no Linux e no Mac, acesse [esta página da Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Para obter instruções sobre como definir o caminho no Windows, acesse [esta página da Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Verifique sua configuração de caminho com o comando `terraform`. Uma lista de opções do Terraform disponíveis é exibida, conforme a saída de exemplo a seguir:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Configurar o acesso do Terraform ao Azure

Para permitir que o Terraform provisione recursos para o Azure, crie uma [entidade de serviço do Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). A entidade de serviço concede seus scripts Terraform para provisionar recursos em sua assinatura do Azure.

Se você tiver várias assinaturas do Azure, primeiro consulte sua conta com [az account list](/cli/azure/account#az-account-list) para obter uma lista de valores de ID da assinatura e ID do locatário:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Para usar uma assinatura selecionada, defina a assinatura para esta sessão com [az account set](/cli/azure/account#az-account-set). Configure a variável de ambiente `SUBSCRIPTION_ID`para conter o valor do campo`id` retornado da assinatura que você deseja usar:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Agora você pode criar uma entidade de serviço para uso com o Terraform. Use [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) e defina o *escopo* para sua assinatura da seguinte maneira:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

`appId`, `password`, `sp_name` e `tenant` são retornados. Anote `appId` e `password`.

## <a name="configure-terraform-environment-variables"></a>Configurar as variáveis de ambiente do Terraform

Para configurar o Terraform para usar sua entidade de serviço do Azure AD, defina as seguintes variáveis de ambiente, que são usadas pelos [módulos do Azure Terraform](https://registry.terraform.io/modules/Azure). Você também pode definir o ambiente se estiver trabalhando com uma nuvem do Azure diferente do público do Azure.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Você pode usar o seguinte script de shell de amostra para definir essas variáveis:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Executar um script de exemplo

Crie um arquivo `test.tf` em um diretório vazio e cole o script a seguir.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Salve o arquivo e, em seguida, inicialize a implantação do Terraform. Esta etapa faz o download dos módulos do Azure necessários para criar um grupo de recursos do Azure.

```bash
terraform init
```

A saída deverá ser semelhante ao seguinte exemplo:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Você pode visualizar as ações a serem completadas pelo script Terraform com `terraform plan`. Quando estiver pronto para criar o grupo de recursos, aplique seu plano Terraform da seguinte maneira:

```bash
terraform apply
```

A saída deverá ser semelhante ao seguinte exemplo:

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você instalou o Terraform ou usou o Cloud Shell para configurar as credenciais do Azure e começar a criar recursos em sua assinatura do Azure. Para criar uma implantação mais completa do Terraform no Azure, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com o Terraform](terraform-create-complete-vm.md)