---
title: Início Rápido – Usar o Terraform para criar uma VM do Linux completa no Azure
description: Neste início rápido, você usará o Terraform para criar e gerenciar um ambiente completo de máquina virtual do Linux no Azure
keywords: azure devops terraform linux vm máquina virtual
ms.topic: quickstart
ms.date: 03/15/2020
ms.openlocfilehash: f262734cc16d97e4d73af371410403a4cbb8815e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79415454"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Início Rápido: Criar uma infraestrutura completa de máquina virtual do Linux no Azure usando o Terraform

O Terraform permite definir e criar implantações de infraestrutura completa no Azure. Você cria modelos do Terraform em um formato legível que criar e configurar os recursos do Azure de maneira consistente e reproduzível. Este artigo mostra como criar um ambiente Linux completo e os recursos de apoio com o Terraform. Você também pode aprender a [instalar e configurar o Terraform](terraform-install-configure.md).

> [!NOTE]
> Para obter suporte específico do Terraform, entre em contato com o Terraform diretamente usando um dos canais da comunidade:
>
>    * A [seção Terraform](https://discuss.hashicorp.com/c/terraform-core) do portal da comunidade contém perguntas, casos de uso e padrões úteis.
>
>    * Para perguntas relacionadas ao provedor, acesse a seção [Provedores Terraform](https://discuss.hashicorp.com/c/terraform-providers) do portal da comunidade.


## <a name="create-azure-connection-and-resource-group"></a>Criar conexão e grupo de recursos do Azure

Vamos percorrer cada seção de um modelo do Terraform. Você também poderá ver a versão completa do [modelo do Terraform](#complete-terraform-script) que você pode copiar e colar.

A seção `provider` informa o Terraform para usar um provedor do Azure. Para obter valores para `subscription_id`, `client_id`, `client_secret` e `tenant_id`, confira [Instalar e configurar o Terraform](terraform-install-configure.md). 

> [!TIP]
> Se você criar variáveis de ambiente para os valores ou se estiver usando a [experiência do Bash do Azure Cloud Shell](/azure/cloud-shell/overview) , não precisará incluir as declarações de variáveis nesta seção.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

A seção a seguir cria um grupo de recursos denominado `myResourceGroup` no local `eastus`:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

Nas seções adicionais, você faz referência ao grupo de recursos com `${azurerm_resource_group.myterraformgroup.name}`.

## <a name="create-virtual-network"></a>Criar rede virtual
A seção a seguir em cria uma rede virtual chamada `myVnet` no espaço de endereço `10.0.0.0/16`:

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

A seção a seguir cria uma sub-rede denominada `mySubnet` na rede virtual `myVnet`:

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Criar um endereço IP público
Para acessar os recursos na Internet, crie e atribua um endereço IP público para a sua VM. A seção a seguir cria um endereço IP público denominado `myPublicIP`:

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Criar um grupo de segurança de rede
Os grupos de segurança de rede controlam o fluxo de entrada e saída de tráfego de rede de sua VM. A seção a seguir cria um grupo de segurança de rede denominado `myNetworkSecurityGroup` e define uma regra para permitir o tráfego de SSH na porta TCP 22:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Criar placa de adaptador de rede virtual
Uma placa de adaptador de rede virtual (NIC) conecta-se à VM para uma determinada rede virtual, um endereço IP público e um grupo de segurança de rede. A seção a seguir em um modelo do Terraform cria uma NIC virtual denominada `myNIC` conectada aos recursos da rede virtual que você criou:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```


## <a name="create-storage-account-for-diagnostics"></a>Criar conta de armazenamento para diagnóstico
Para armazenar o diagnóstico de inicialização para uma máquina virtual, você precisará de uma conta de armazenamento. Estes diagnósticos de inicialização podem ajudá-lo a solucionar problemas e monitorar o status da VM. A conta de armazenamento que você cria serve apenas para armazenar os dados de diagnóstico de inicialização. Como cada conta de armazenamento deve ter um nome exclusivo, a seção a seguir gera textos aleatórios:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Agora você pode criar uma conta de armazenamento. A seção a seguir cria uma conta de armazenamento com o nome com base no texto aleatório gerado na etapa anterior:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Criar máquina virtual

A etapa final é criar uma máquina virtual e usar todos os recursos criados. A seção a seguir cria uma VM denominada `myVM` e anexa a NIC virtual denominada `myNIC`. A imagem mais recente do `Ubuntu 16.04-LTS` é usada e um usuário chamado `azureuser` é criado com a autenticação de senha desabilitada.

 Os dados de chave SSH são fornecidos na seção `ssh_keys`. Forneça uma chave SSH pública no campo `key_data`.

```hcl
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Script completo do Terraform

Para reunir todas essas seções e ver Terraform em ação, crie um arquivo chamado `terraform_azure.tf` e cole o seguinte conteúdo:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn't exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Criar e i,Implantar a infraestrutura
Com o modelo do Terraform criado, a primeira etapa é inicializar o Terraform. Esta etapa garante que o Terraform tem todos os pré-requisitos para criar o modelo no Azure.

```bash
terraform init
```

A próxima etapa é fazer o Terraform revisar e validar o modelo. Esta etapa compara os recursos solicitados para as informações de estado salvo por Terraform e, em seguida, gera a execução planejada. Os recursos do Azure não são criados neste momento.

```bash
terraform plan
```

Depois de executar o comando anterior, você deverá ver algo semelhante à seguinte tela:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Se tudo estiver correto, e você estiver pronto para criar a infraestrutura no Azure, aplique o modelo no Terraform:

```bash
terraform apply
```

Após a conclusão do Terraform, sua infraestrutura de VM estará pronta. Obtenha o endereço IP público da sua VM com [az vm show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Então você poderá enviar por SSH para sua VM:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)