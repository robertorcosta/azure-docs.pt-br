---
title: Tutorial – Criar um cluster de VM do Azure com o Terraform usando o Registro do módulo
description: Saber como usar os módulos do Terraform para criar um cluster de máquina virtual do Windows no Azure
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: ba99f9cdc20448398b339041aeab41fb75495e5d
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969489"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>Tutorial: Criar um cluster de VM do Azure com o Terraform usando o Registro do módulo

Este artigo o orienta você a criar um pequeno cluster de VM com o [módulo de computação do Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2) do Terraform. Neste tutorial, você aprenderá a: 

> [!div class="checklist"]
> * Configurar a autenticação com o Azure
> * Criar o modelo do Terraform
> * Visualizar as alterações com o plano
> * Aplicar a configuração para criar o cluster de VM

Para obter mais informações sobre o Terraform, consulte a [documentação do Terraform](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Configurar a autenticação com o Azure

> [!TIP]
> Se você [usar variáveis de ambiente do Terraform](/azure/virtual-machines/linux/terraform-install-configure) ou executar este tutorial no [Azure Cloud Shell](/azure/cloud-shell/overview), ignore esta etapa.

 Examine [Install Terraform and configure access to Azure](/azure/virtual-machines/linux/terraform-install-configure) (Instalar o Terraform e configurar o acesso ao Azure) para criar uma entidade de serviço do Azure. Use essa entidade de serviço para popular um novo arquivo `azureProviderAndCreds.tf` em um diretório vazio com o código a seguir:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Criar o modelo

Crie um novo modelo do Terraform chamado `main.tf` com o código a seguir:

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = module.network.vnet_subnets[0]
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = module.mycompute.public_ip_dns_name
}

output "vm_public_ip" {
    value = module.mycompute.public_ip_address
}

output "vm_private_ips" {
    value = module.mycompute.network_interface_private_ip
}
```

Execute o `terraform init` em seu diretório de configuração. O uso de uma versão do Terraform superior à versão 0.10.6 mostra a saída a seguir:

![Terraform Init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Visualizar as alterações com o plano

Execute `terraform plan` para visualizar a infraestrutura de máquina virtual criada pelo modelo.

![Terraform Plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Criar as máquinas virtuais com apply

Execute `terraform apply` para provisionar as VMs no Azure.

![Terraform Apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Procurar a lista de módulos do Azure Terraform](https://registry.terraform.io/modules/Azure)