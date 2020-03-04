---
title: Tutorial – Criar um conjunto de dimensionamento de máquinas virtuais do Azure com base em uma imagem personalizada do Packer usando o Terraform
description: Use o Terraform para configurar e controlar a versão de um conjunto de dimensionamento de máquinas virtuais do Azure com base em uma imagem personalizada gerada pelo Packer (completo com uma rede virtual e discos gerenciados anexados).
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 92a8221d625f8b6b73343f74b85fdfcf5e578b23
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472190"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>Tutorial: Criar um conjunto de dimensionamento de máquinas virtuais do Azure com base em uma imagem personalizada do Packer usando o Terraform

Neste tutorial, você usará o [Terraform](https://www.terraform.io/) para criar e implantar um [conjunto de dimensionamento de máquinas virtuais do Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) criado com uma imagem personalizada produzida com auxílio do [Packer](https://www.packer.io/intro/index.html) com discos gerenciados que usam a [HCL](https://www.terraform.io/docs/configuration/syntax.html) (Linguagem de Configuração da HashiCorp). 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar sua implantação do Terraform.
> * Usar variáveis e saídas para a implantação do Terraform.
> * Criar e implantar uma infraestrutura de rede.
> * Criar uma imagem de máquina virtual personalizada usando o Packer.
> * Criar e implantar um conjunto de dimensionamento de máquinas virtuais usando a imagem personalizada.
> * Criar e implantar um jumpbox.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Prerequisites

- **Terraform**: [Instalar o Terraform e configurar o acesso ao Azure](terraform-install-configure.md).
- **Par de chaves SSH**: [Criar um par de chaves SSH](/azure/virtual-machines/linux/mac-create-ssh-keys).
- **Packer**:  [Instalar o Packer](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>Criar a estrutura do arquivo

Crie três novos arquivos em um diretório vazio com os seguintes nomes:

- `variables.tf`: Esse arquivo contém os valores das variáveis usadas no modelo.
- `output.tf`: Esse arquivo descreve as configurações que são exibidas após a implantação.
- `vmss.tf`: Esse arquivo contém o código da infraestrutura que você está implantando.

##  <a name="create-the-variables"></a>Criar as variáveis 

Nesta etapa, você definirá as variáveis que personalizam os recursos criados pelo Terraform.

Edite o arquivo `variables.tf`, copie o código a seguir e salve as alterações.

```hcl
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> A definição do valor padrão da variável resource_group_name foi removida. Defina seu próprio valor.

Salve o arquivo.

Ao implantar o modelo do Terraform, obtenha o nome de domínio totalmente qualificado que é usado para acessar o aplicativo. Use o tipo de recurso `output` do Terraform e obtenha a propriedade `fqdn` do recurso. 

Edite o arquivo `output.tf` e copie o código a seguir para expor o nome de domínio totalmente qualificado para as máquinas virtuais. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definir a infraestrutura de rede em um modelo 

Nesta etapa, você criará a seguinte infraestrutura de rede em um novo grupo de recursos do Azure: 
  - Uma rede virtual com o espaço de endereço igual a 10.0.0.0/16.
  - Uma sub-rede com o espaço de endereço igual a 10.0.2.0/24.
  - Dois endereços IP públicos. Um é usado pelo balanceador de carga do conjunto de dimensionamento de máquinas virtuais. O outro é usado para se conectar ao jumpbox do SSH.

Você também precisa de um grupo de recursos no qual todos os recursos são criados. 

Edite e copie o seguinte código no arquivo `vmss.tf`: 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = var.resource_group_name
  location = var.location

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = azurerm_resource_group.vmss.name
  virtual_network_name = azurerm_virtual_network.vmss.name
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Marque os recursos que estão sendo implantados no Azure para facilitar sua identificação no futuro.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede

Inicialize o ambiente de Terraform executando o seguinte comando no diretório em que você criou os arquivos `.tf`:

```bash
terraform init 
```
 
Os plug-ins do provedor são baixados do Terraform Registry para a pasta `.terraform` no diretório em que você executou o comando.

Execute o comando a seguir para implantar a infraestrutura no Azure.

```bash
terraform apply
```

Verifique se o nome de domínio totalmente qualificado do endereço IP público corresponde à sua configuração.

![Nome de domínio totalmente qualificado do conjunto de dimensionamento de máquinas virtuais do Terraform para o endereço IP público](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

O grupo de recursos contém os seguintes recursos:

![Terraform recursos de rede do conjunto de dimensionamento de máquinas virtuais](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Criar uma imagem do Azure usando o Packer
Criar uma imagem Linux personalizada seguindo as etapas do tutorial [Como usar o Packer para criar imagens de máquina virtual Linux no Azure](/azure/virtual-machines/linux/build-image-with-packer).
 
Siga o tutorial para criar uma imagem Ubuntu desprovisionada com o Nginx instalado.

![Após criar a imagem do Packer, você terá uma imagem](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Para os fins deste tutorial, na imagem do Packer, um comando é executado para instalar o Nginx. Também é possível executar seu próprio script durante a criação.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Editar a infraestrutura para adicionar o conjunto de dimensionamento de máquinas virtuais

Nesta etapa, você criará os seguintes recursos na rede implantada anteriormente:
- Um balanceador de carga do Azure para atender ao aplicativo. Anexe-o ao endereço IP público que foi implantado anteriormente.
- Um balanceador de carga do Azure e regras para atender ao aplicativo. Anexe-o ao endereço IP público que foi configurado anteriormente.
- Um pool de endereços de back-end do Azure. Atribua-o ao balanceador de carga.
- Uma porta de investigação de integridade usada pelo aplicativo e configurada no balanceador de carga.
- Um conjunto de dimensionamento de máquinas virtuais que fica atrás do balanceador de carga e é executado na rede virtual que foi implantada anteriormente.
- [Nginx](https://nginx.org/) nos nós de dimensionamento da máquina virtual, instalado usando uma imagem personalizada.


Adicione o seguinte código ao final do arquivo `vmss.tf`.

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.vmss.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "ssh-running-probe"
  port                = var.application_port
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = azurerm_resource_group.vmss.name
  loadbalancer_id                = azurerm_lb.vmss.id
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = var.application_port
  backend_port                   = var.application_port
  backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = azurerm_lb_probe.vmss.id
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = data.azurerm_resource_group.image.name
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id=data.azurerm_image.image.id
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = azurerm_subnet.vmss.id
      load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
      primary = true
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Personalize a implantação adicionando o seguinte código ao `variables.tf`:

```hcl
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Implantar o conjunto de dimensionamento de máquinas virtuais no Azure

Execute o seguinte comando para visualizar a implantação do conjunto de dimensionamento de máquinas virtuais:

```bash
terraform plan
```

A saída do comando deve ser semelhante à imagem mostrada a seguir:

![Terraform adicionar plano do conjunto de dimensionamento de máquinas virtuais](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Implante os recursos adicionais no Azure: 

```bash
terraform apply 
```

O conteúdo do grupo de recursos é semelhante à imagem a seguir:

![Terraform grupo de recursos do conjunto de dimensionamento de máquinas virtuais](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Abra um navegador e conecte-se ao nome de domínio totalmente qualificado que foi retornado pelo comando. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Adicionar um jumpbox à rede existente 

Esta etapa opcional permite o acesso a SSH para as instâncias do conjunto de dimensionamento de máquinas virtuais por meio de um jumpbox.

Adicione os seguintes recursos à sua implantação existente:
- Um adaptador de rede conectado à mesma sub-rede que o conjunto de dimensionamento de máquinas virtuais
- Uma máquina virtual conectada a este adaptador de rede

Adicione o seguinte código ao final do arquivo `vmss.tf`:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = azurerm_subnet.vmss.id
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = azurerm_public_ip.jumpbox.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = var.location
  resource_group_name   = azurerm_resource_group.vmss.name
  network_interface_ids = [azurerm_network_interface.jumpbox.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Edite `outputs.tf` para adicionar o código a seguir que exibirá o nome do host do jumpbox quando a implantação for encerrada:

```
output "jumpbox_public_ip" {
    value = azurerm_public_ip.jumpbox.fqdn
}
```

## <a name="deploy-the-jumpbox"></a>Implantar o jumpbox

Implante o jumpbox.

```bash
terraform apply 
```

Depois que a implantação for concluída, o conteúdo do grupo de recursos será semelhante à seguinte imagem:

![Terraform grupo de recursos do conjunto de dimensionamento de máquinas virtuais](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> A entrada com senha está desabilitada no jumpbox e no conjunto de dimensionamento de máquinas virtuais que você implantou. Entre com o SSH para acessar as VMs.

## <a name="clean-up-the-environment"></a>Limpar o ambiente

Os comandos a seguir excluem todos os recursos criados neste tutorial:

```bash
terraform destroy
```

Insira *sim* quando lhe for perguntado se deseja confirmar a exclusão dos recursos. O processo de destruição leva alguns minutos para ser concluído.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)
