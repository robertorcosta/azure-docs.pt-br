---
title: Tutorial - Criar um conjunto de dimensionamento de máquinas virtuais do Azure usando o Terraform
description: Saiba como usar o Terraform para configurar e fazer a versão de um conjunto de dimensionamento de máquinas virtuais do Azure.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: ef2ce0a3ea8c50123cd51ab60a6b98894739d859
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159085"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-using-terraform"></a>Tutorial: Criar um conjunto de dimensionamento de máquinas virtuais do Azure usando o Terraform

[Os conjuntos de dimensionamento de máquinas virtuais do Azure](/azure/virtual-machine-scale-sets) permitem configurar VMs idênticas. O número de instâncias de VM pode ser ajustado com base na demanda ou em uma agenda. Para obter mais informações, consulte [Dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais no portal do Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal).

Neste tutorial, você aprenderá como usar o [Azure Cloud Shell](/azure/cloud-shell/overview) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Configurar uma implantação do Terraform
> * Usar variáveis e saídas para a implantação do Terraform
> * Criar e implantar a infraestrutura de rede
> * Criar e implantar um conjunto de dimensionamento de máquinas virtuais e anexá-lo à rede
> * Criar e implantar um jumpbox para se conectar às VMs via SSH

> [!NOTE]
> A versão mais recente dos arquivos de configuração do Terraform usados neste artigo estão no [Repositório Awesome Terraform no GitHub](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Instalar o Terraform**: Siga as instruções no artigo [Terraform e configurar o acesso ao Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Crie um par de chaves SSH**: Para obter mais informações, consulte [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Crie a estrutura de diretórios

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se você não selecionou um ambiente anteriormente, selecione **Bash** como o ambiente.

    ![Aviso do Cloud Shell](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Altere os diretórios para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `vmss`.

    ```bash
    mkdir vmss
    ```

1. Altere os diretórios para o novo diretório:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Crie o arquivo de definições de variáveis
Nesta seção, você define as variáveis que personalizam os recursos criados pelo Terraform.

No Azure Cloud Shell, execute as seguintes etapas:

1. Crie um arquivo chamado `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Cole o código a seguir no editor:

   ```hcl
   variable "location" {
    description = "The location where resources will be created"
   }

   variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
   }

   variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
   }
   ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="create-the-output-definitions-file"></a>Crie o arquivo de definições de saída
Nesta seção, você cria o arquivo que descreve a saída após a implantação.

No Azure Cloud Shell, execute as seguintes etapas:

1. Crie um arquivo chamado `output.tf`.

    ```bash
    code output.tf
    ```

1. Cole o código a seguir no editor para expor o FQDN (nome de domínio totalmente qualificado) para as máquinas virtuais.
   :

   ```hcl
    output "vmss_public_ip" {
        value = azurerm_public_ip.vmss.fqdn
    }
   ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="define-the-network-infrastructure-in-a-template"></a>Definir a infraestrutura de rede em um modelo
Nesta seção, você cria a infraestrutura de rede a seguir em um novo grupo de recursos do Azure:

  - Uma VNET (rede virtual) com o espaço de endereço de 10.0.0.0/16
  - Uma sub-rede com o espaço de endereço de 10.0.2.0/24
  - Dois endereços IP públicos. Um usado pelo balanceador de carga do conjunto de dimensionamento de máquinas virtuais, outro usado para conectar ao jumpbox SSH.

No Azure Cloud Shell, execute as seguintes etapas:

1. Crie um arquivo nomeado `vmss.tf` para descrever a infraestrutura do conjunto de dimensionamento de máquinas virtuais.

    ```bash
    code vmss.tf
    ```

1. Cole o código a seguir no final do arquivo para expor o FQDN (nome de domínio totalmente qualificado) para as máquinas virtuais.

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = var.resource_group_name
    location = var.location
    tags     = var.tags
   }

   resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
   }

   resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    tags                = var.tags
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
    allocation_method = "Static"
    domain_name_label            = random_string.fqdn.result
    tags                         = var.tags
   }
   ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

## <a name="provision-the-network-infrastructure"></a>Provisionar a infraestrutura de rede
Usando o Azure Cloud Shell no diretório em que você criou os arquivos de configuração (.tf), execute as seguintes etapas:

1. Inicialize o Terraform.

   ```bash
   terraform init
   ```

1. Execute o comando a seguir para implantar a infraestrutura definida no Azure.

   ```bash
   terraform apply
   ```

   O Terraform solicita um valor `location`, já que a variável `location` é definida em `variables.tf`, mas nunca estabelecido. É possível inserir qualquer local válido, como "Oeste dos EUA", selecionando Enter. (Use parênteses em torno de qualquer valor com espaços.)

1. O Terraform imprime a saída conforme definido no arquivo `output.tf`. Conforme mostrado na captura de tela a seguir, o FQDN assume o formato: `<ID>.<location>.cloudapp.azure.com`. A ID é um valor computado e a localização é o valor que você fornece ao executar o Terraform.

   ![Nome de domínio totalmente qualificado do conjunto de dimensionamento de máquinas virtuais para Endereço IP Público](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. No menu do portal do Azure, selecione **Grupos de recursos** do menu principal.

1. Na guia **Grupos de recursos**, selecione **myResourceGroup** para exibir os recursos que foram criados pelo Terraform.
   ![Recursos de rede do conjunto de dimensionamento de máquinas virtuais](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Adicionar um conjunto de dimensionamento de máquinas virtuais

Nesta seção, você aprende como adicionar os seguintes recursos ao modelo:

- Um Azure Load Balancer e regras para atender o aplicativo e anexá-lo ao endereço IP público configurado anteriormente neste artigo
- Um pool de endereços de back-end do Azure e atribuí-lo ao balanceador de carga
- Uma porta de investigação de integridade usada pelo aplicativo e configurada no balanceador de carga
- Um conjunto de dimensionamento de máquinas virtuais localizado por trás do balanceador de carga em execução na VNET implantada anteriormente neste artigo
- [Nginx](https://nginx.org/) nos nós do conjunto de dimensionamento de máquinas virtuais usando [cloud-init](https://cloudinit.readthedocs.io/en/latest/).

No Cloud Shell, execute as seguintes etapas:

1. Abra o arquivo de configuração `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Vá para o final do arquivo e entre no modo de acréscimo, selecionando a tecla A.

1. Cole o código a seguir no final do arquivo:

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = azurerm_public_ip.vmss.id
    }

    tags = var.tags
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
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
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
      admin_username       = var.admin_user
      admin_password       = var.admin_password
      custom_data          = file("web.conf")
    }

    os_profile_linux_config {
      disable_password_authentication = false
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

    tags = var.tags
   }
   ```

1. Salve o arquivo e saia do editor vi, inserindo o comando a seguir:

    ```bash
    :wq
    ```

1. Crie um arquivo nomeado `web.conf` para servir como a configuração cloud-init para as máquinas virtuais que fazem parte do conjunto de dimensionamento.

    ```bash
    code web.conf
    ```

1. Cole o código a seguir no editor:

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Salve o arquivo e saia do editor vi, inserindo o comando a seguir:

     ```bash
     :wq
     ```

1. Abra o arquivo de configuração `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Vá para o final do arquivo e entre no modo de acréscimo, selecionando a tecla A.

1. Personalize a implantação, colando o código a seguir no final do arquivo:

    ```hcl
    variable "application_port" {
       description = "The port that you want to expose to the external load balancer"
       default     = 80
    }

    variable "admin_user" {
       description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
       default     = "azureuser"
    }

    variable "admin_password" {
       description = "Default password for admin account"
    }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

1. Crie um plano do Terraform para visualizar a implantação do conjunto de dimensionamento de máquinas virtuais. (Você precisa especificar uma senha de sua escolha, bem como a localização dos recursos.)

    ```bash
    terraform plan
    ```

    A saída do comando deve ser semelhante à captura de tela a seguir:

    ![Saída da criação do conjunto de dimensionamento de máquinas virtuais](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Implante os novos recursos no Azure.

    ```bash
    terraform apply
    ```

    A saída do comando deve ser semelhante à captura de tela a seguir:

    ![Terraform grupo de recursos do conjunto de dimensionamento de máquinas virtuais](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Abra um navegador e conecte-se ao FQDN que foi retornado pelo comando.

    ![Resultados da navegação para FQDN](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Adicionar um jumpbox do SSH
Um *jumpbox* do SSH é um servidor único pelo qual ocorre "jump" para acessar outros servidores na rede. Nesta etapa, você configurará os seguintes recursos:

- Um adaptador de rede (ou jumpbox) conectado à mesma sub-rede que o conjunto de dimensionamento de máquinas virtuais.

- Uma máquina virtual conectada a este adaptador de rede. Este “jumpbox” pode ser acessado remotamente. Uma vez conectado, é possível usar o SSH em qualquer máquina virtual no conjunto de dimensionamento.

1. Abra o arquivo de configuração `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Vá para o final do arquivo e entre no modo de acréscimo, selecionando a tecla A.

1. Cole o código a seguir no final do arquivo:

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = var.tags}
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

    tags = var.tags
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
      admin_username = var.admin_user
      admin_password = var.admin_password
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = var.tags
   }
   ```

1. Abra o arquivo de configuração `output.tf`.

   ```bash
   code output.tf
   ```

1. Vá para o final do arquivo e entre no modo de acréscimo, selecionando a tecla A.

1. Cole o código a seguir no final do arquivo para exibir o nome do host do jumpbox quando a implantação for concluída:

   ```hcl
   output "jumpbox_public_ip" {
      value = azurerm_public_ip.jumpbox.fqdn
   }
   ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

1. Implante o jumpbox.

   ```bash
   terraform apply
   ```

Depois que a implantação for concluída, o conteúdo do grupo de recursos será semelhante ao mostrado na captura de tela a seguir:

![Terraform grupo de recursos do conjunto de dimensionamento de máquinas virtuais](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> A capacidade de fazer logon com uma senha é desabilitada no jumpbox e no conjunto de dimensionamento de máquinas virtuais que você implantou. Faça logon com SSH para acessar a(s) máquina(s) virtual(is).

## <a name="environment-cleanup"></a>Limpeza do ambiente

Para excluir os recursos do Terraform que foram criados neste tutorial, digite o comando a seguir no Cloud Shell:

```bash
terraform destroy
```

O processo de destruição pode demorar vários minutos para ser concluído.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)