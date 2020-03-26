---
title: Tutorial – Criar um cluster de VM do Azure com o Terraform e HCL
description: Neste tutorial, você usará o Terraform e HCL para criar um cluster de máquina virtual do Linux com um balanceador de carga no Azure
keywords: azure devops terraform vm máquina virtual cluster
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ae1b8eac15309ff27297d9472e70d32e68acaaac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945266"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Tutorial: Criar um cluster de VM do Azure com o Terraform e HCL

Neste tutorial, você vê como criar um pequeno cluster de computação usando [HCL](https://www.terraform.io/docs/configuration/syntax.html). 

Você aprenderá a realizar as seguintes tarefas:

> [!div class="checklist"]
> * Configurar a autenticação do Azure.
> * Criar um arquivo de configuração Terraform.
> * Usar um arquivo de configuração Terraform para criar um balanceador de carga.
> * Usar um arquivo de configuração do Terraform para implantar duas VMs do Linux em um conjunto de disponibilidade.
> * Inicialize o Terraform.
> * Crie um plano de execução Terraform.
> * Aplique o plano de execução Terraform para criar os recursos do Azure.

## <a name="1-set-up-azure-authentication"></a>1. Configurar a autenticação do Azure

> [!NOTE]
> Se você [usar variáveis de ambiente do Terraform](terraform-install-configure.md) ou executar este tutorial no [Azure Cloud Shell](terraform-cloud-shell.md), ignore esta seção.

Nesta seção, você pode gerar uma entidade de serviço do Azure e dois arquivos de configuração de Terraform que contém as credenciais da entidade de segurança.

1. [Configure uma entidade de serviço do Azure AD](terraform-install-configure.md#set-up-terraform-access-to-azure) para permitir que o Terraform provisione recursos no Azure. Ao criar a entidade de segurança, anote os valores para o ID de assinatura, o locatário, a appId e a senha.

2. Abra um prompt de comando.

3. Crie um diretório vazio no qual armazenar os arquivos Terraform.

4. Crie um novo arquivo que contém suas declarações de variáveis. Você pode nomear o arquivo como com uma extensão `.tf`.

5. Copie o seguinte código ao seu arquivo de declaração de variável:

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      version = "~>1.40"
     
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Crie um novo arquivo que contém os valores para as variáveis de Terraform. É comum nomear o arquivo de variável Terraform `terraform.tfvars` como Terraform carrega automaticamente qualquer arquivo chamado `terraform.tfvars` (ou um padrão de `*.auto.tfvars`) se estiver presente no diretório atual. 

7. Copie o seguinte código ao seu arquivo de declaração de variável. Substitua os espaços reservados da seguinte maneira: para `subscription_id`, use a ID de assinatura do Azure especificada durante a execução de `az account set`. Para `tenant_id`, use o `tenant` valor retornado de `az ad sp create-for-rbac`. Para `client_id`, use o `appId` valor retornado de `az ad sp create-for-rbac`. Para `client_secret`, use o `password` valor retornado de `az ad sp create-for-rbac`.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Criar um arquivo de configuração Terraform

Nesta seção, você deve criar um arquivo que contém definições de recursos para sua infraestrutura.

1. Crie um arquivo chamado `main.tf`. 

2. Copiar definições de recursos de exemplo a seguir em um arquivo recém-criado `main.tf`: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. Inicializar Terraform 

O [comando terraform init](https://www.terraform.io/docs/commands/init.html) é usado para inicializar um diretório que contém os arquivos de configuração Terraform - os arquivos criados com as seções anteriores. É uma boa prática para sempre executar o comando `terraform init` depois de gravar uma nova configuração do Terraform. 

> [!TIP]
> O comando `terraform init` é idempotente, que significa que ele pode ser chamado várias vezes ao mesmo tempo em que produz o mesmo resultado. Portanto, se você estiver trabalhando em um ambiente de colaboração e você achar que os arquivos de configuração podem ter sido alterados, é sempre uma boa ideia para chamar o comando `terraform init` antes de executar ou aplicar um plano.

Para inicializar Terraform, execute o seguinte comando:

  ```bash
  terraform init
  ```

  ![Inicializando Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Criar um plano de execução Terraform

O [comando de plano do terraform](https://www.terraform.io/docs/commands/plan.html) é usado para criar um plano de execução. Para gerar um plano de execução, Terraform agrega todos os arquivos `.tf` no diretório atual. 

O parâmetro [-out](https://www.terraform.io/docs/commands/plan.html#out-path) salva o plano de execução em um arquivo de saída. Esse recurso aborda problemas de simultaneidade comuns em ambientes de vários desenvolvimentos. Um desses problemas resolvido pelo arquivo de saída é o seguinte cenário:

1. Dev 1 cria o arquivo de configuração.
1. Dev 2 modifica o arquivo de configuração.
1. Dev 1 aplica (executa) o arquivo de configuração.
1. O Dev 1 obtém resultados inesperados pois não sabe que o Dev 2 modificou a configuração.

O Dev 1 especificando um arquivo de saída impede que o Dev 2 afete o Dev 1. 

Se você não precisa salvar o plano de execução, execute o seguinte comando:

  ```bash
  terraform plan
  ```

Se você precisa salvar o plano de execução, execute o seguinte comando: Substitua os espaços reservados pelos valores adequados do seu ambiente.

  ```bash
  terraform plan -out=<path>
  ```

Outro parâmetro útil é [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Por padrão, o Terraform tentou encontrar seu arquivo de variáveis da seguinte maneira:
- Arquivo nomeado `terraform.tfvars`
- Arquivo nomeado usando o seguinte padrão: `*.auto.tfvars`

No entanto, o arquivo de variáveis não precisa seguir nenhuma das duas convenções anteriores. Nesse caso, especifique o nome do arquivo de variáveis com o parâmetro `-var-file` em que o nome do arquivo de variáveis não carrega uma extensão. O exemplo a seguir ilustra esse ponto:

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform determina as ações necessárias para atingir o estado especificado no arquivo de configuração.

![Criar um plano de execução Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Aplicar o plano de execução Terraform

A etapa final deste tutorial é usar o [comando de aplicar terraform](https://www.terraform.io/docs/commands/apply.html) para aplicar o conjunto de ações geradas pelo comando `terraform plan`.

Se você deseja aplicar o plano de execução mais recente, execute o seguinte comando:

  ```bash
  terraform apply
  ```

Se você deseja aplicar o plano de execução salvo anteriormente, execute o seguinte comando. Substitua os espaços reservados pelos valores adequados do seu ambiente:

  ```bash
  terraform apply <path>
  ```

![Aplicar um plano de execução Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Criar um conjunto de dimensionamento de máquinas virtuais do Azure usando o Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)