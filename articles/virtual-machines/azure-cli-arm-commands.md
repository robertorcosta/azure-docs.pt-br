---
title: Comandos da CLI clássica do Azure
description: Comandos da CLI (interface de linha de comando) do Azure para gerenciar recursos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/18/2017
ms.author: cynthn
ms.openlocfilehash: 5b7d6f4efa4a910d3141638602c603d484fac6da
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86181855"
---
# <a name="azure-classic-cli-commands"></a>Comandos da CLI clássica do Azure 

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Este tópico descreve como instalar a CLI clássica do Azure. A CLI clássica foi preterida e só deve ser usada com o modelo de implantação clássico. Para todas as outras implantações, use o [CLI do Azure](https://docs.microsoft.com/cli/azure/).

Este artigo fornece sintaxe e opções para os comandos da CLI (interface de linha de comando) clássicas do Azure que você normalmente usaria para criar e gerenciar recursos do Azure. Essa não é uma referência completa, e sua versão da CLI poderá mostrar comandos ou parâmetros um pouco diferentes. 

Para começar, primeiro [Instale a CLI clássica do Azure](../cli-install-nodejs.md) e [Conecte-se à sua assinatura do Azure](/cli/azure/authenticate-azure-cli).

Para ver as atuais opções e a sintaxe de comandos na linha de comando no modo do Gerenciador de Recursos, digite `azure help` ou, para exibir a ajuda para um comando específico, `azure help [command]`. Também é possível encontrar exemplos da CLI na documentação de criação e gerenciamento de serviços específicos do Azure.

Parâmetros opcionais são mostrados entre colchetes (por exemplo, `[parameter]`). Todos os outros parâmetros são obrigatórios.

Além dos parâmetros opcionais específicos aos comandos documentados aqui, há três parâmetros opcionais que podem ser usados para exibir saída detalhada, como opções de solicitação e códigos de status. O parâmetro `-v` fornece uma saída detalhada e o parâmetro `-vv` fornece uma saída mais detalhada ainda. A opção `--json` produzi o resultado no formato JSON bruto.

## <a name="setting-the-resource-manager-mode"></a>Definindo o modo do Gerenciador de Recursos
Use o comando a seguir para habilitar os comandos do modo de Gerenciador de Recursos da CLI do Azure.

```azurecli
azure config mode arm
```

> [!NOTE]
> O modo do Gerenciador de Recursos do Azure da CLI e o modo do Gerenciamento de Serviços do Azure são mutuamente exclusivos. Ou seja, recursos criados em um modo não podem ser gerenciados no outro modo.
>


## <a name="account-information"></a>Informações da conta
As informações da assinatura do Azure são utilizadas pela ferramenta para se conectar à sua conta.

**Lista as assinaturas importadas**

```azurecli
account list [options]
```

**Mostra detalhes sobre uma assinatura**  

```azurecli
account show [options] [subscriptionNameOrId]
```

**Define a assinatura atual**
```azurecli
account set [options] <subscriptionNameOrId>
```

**Remove uma assinatura ou ambiente ou desmarcar todas as informações de conta e ambiente armazenada**  
```azurecli
account clear [options]
```

**Comandos para gerenciar seu ambiente de conta**  

```azurecli
account env list [options]
account env show [options] [environment]
account env add [options] [environment]
account env set [options] [environment]
account env delete [options] [environment]
```

## <a name="active-directory-objects"></a>Objetos do Active Directory
**Comandos para exibir aplicativos do active directory**

```azurecli
ad app create [options]
ad app delete [options] <object-id>
```

**Comandos para exibir grupos do active directory**

```azurecli
ad group list [options]
ad group show [options]
```

**Comandos para fornecer uma informação de sobre um subgrupo ou membro do active directory**

```azurecli
ad group member list [options] [objectId]
```

**Comandos para exibir entidades de serviço do active directory**

```azurecli
ad sp list [options]
ad sp show [options]
ad sp create [options] <application-id>
ad sp delete [options] <object-id>
```

**Comandos para exibir usuários do active directory**

```azurecli
ad user list [options]
ad user show [options]
```

## <a name="availability-sets"></a>Conjuntos de disponibilidade
**Cria um conjunto de disponibilidade dentro de um grupo de recursos**

```azurecli
availset create [options] <resource-group> <name> <location> [tags]
```

**Lista os conjuntos de disponibilidade dentro de um grupo de recursos**

```azurecli
availset list [options] <resource-group>
```

**Obtém um conjunto de disponibilidade dentro de um grupo de recursos**

```azurecli
availset show [options] <resource-group> <name>
```

**Exclui um conjunto de disponibilidade dentro de um grupo de recursos**

```azurecli
availset delete [options] <resource-group> <name>
```

## <a name="local-settings"></a>Configurações locais
**Lista definições de configuração de CLI do Azure**

```azurecli
config list [options]
```

**Exclui uma definição de configuração**

```azurecli
config delete [options] <name>
```

**Atualiza uma definição de configuração**

```azurecli
config set <name> <value>
```

**Define o modo de funcionamento da CLI do Azure como `arm` ou `asm`**

```azurecli
config mode [options] <modename>
```


## <a name="account-features"></a>Recursos da conta
**Lista todos os recursos disponíveis para sua assinatura**

```azurecli
feature list [options]
```

**Mostra um recurso**

```azurecli
feature show [options] <providerName> <featureName>
```

**Registra um recurso visualizado de um provedor de recursos**

```azurecli
feature register [options] <providerName> <featureName>
```

## <a name="resource-groups"></a>Grupos de recursos
**Crie um grupos de recursos**

```azurecli
group create [options] <name> <location>
```

**Define marcas para um grupo de recursos**

```azurecli
group set [options] <name> <tags>
```

**Exclui um grupo de recursos**

```azurecli
group delete [options] <name>
```

**Lista os grupos de recursos para sua assinatura**

```azurecli
group list [options]
```

**Mostra um grupo de recursos para sua assinatura**

```azurecli
group show [options] <name>
```

**Comandos para gerenciar logs de grupo de recursos**

```azurecli
group log show [options] [name]
```

**Comandos para gerenciar a implantação em um grupo de recursos**

```azurecli
group deployment create [options] [resource-group] [name]
group deployment list [options] <resource-group> [state]
group deployment show [options] <resource-group> [deployment-name]
group deployment stop [options] <resource-group> [deployment-name]
```

**Comandos para gerenciar o modelo de grupo de recurso local ou galeria**

```azurecli
group template list [options]
group template show [options] <name>
group template download [options] [name] [file]
group template validate [options] <resource-group>
```

## <a name="hdinsight-clusters"></a>Clusters do HDInsight
**Comandos para criar ou adicionar a um arquivo de configuração de cluster**

```azurecli
hdinsight config create [options] <configFilePath> <overwrite>
hdinsight config add-config-values [options] <configFilePath>
hdinsight config add-script-action [options] <configFilePath>
```


Exemplo: crie um arquivo de configuração que contém uma ação de script para ser executada durante a criação de um cluster.

```azurecli
hdinsight config create "C:\myFiles\configFile.config"
hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"
```

**Comando para criar um cluster em um grupo de recursos**

```azurecli
hdinsight cluster create [options] <clusterName>
```

Exemplo: criar um Storm no cluster do Linux

```azurecli
azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Storm --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 myNewCluster01

info:    Executing command hdinsight cluster create
+ Submitting the request to create cluster...
info:    hdinsight cluster create command OK
```

Exemplo: criar um cluster com uma ação de script

```azurecli
azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01

info:    Executing command hdinsight cluster create
+ Submitting the request to create cluster...
info:    hdinsight cluster create command OK
```

Opções de parâmetro:

```txt
-h, --help                                                 output usage information
-v, --verbose                                              use verbose output
-vv                                                        more verbose with debug output
--json                                                     use json output
-g --resource-group <resource-group>                       The name of the resource group
-c, --clusterName <clusterName>                            HDInsight cluster name
-l, --location <location>                                  Data center location for the cluster
-y, --osType <osType>                                      HDInsight cluster operating system
'Windows' or 'Linux'
--version <version>                                        HDInsight cluster version
--clusterType <clusterType>                                HDInsight cluster type.
Hadoop | HBase | Spark | Storm
--defaultStorageAccountName <storageAccountName>           Storage account url to use for default HDInsight storage
--defaultStorageAccountKey <storageAccountKey>             Key to the storage account to use for default HDInsight storage
--defaultStorageContainer <storageContainer>               Container in the storage account to use for HDInsight default storage
--headNodeSize <headNodeSize>                              (Optional) Head node size for the cluster
--workerNodeCount <workerNodeCount>                        Number of worker nodes to use for the cluster
--workerNodeSize <workerNodeSize>                          (Optional) Worker node size for the cluster)
--zookeeperNodeSize <zookeeperNodeSize>                    (Optional) Zookeeper node size for the cluster
--userName <userName>                                      Cluster username
--password <password>                                      Cluster password
--sshUserName <sshUserName>                                SSH username (only for Linux clusters)
--sshPassword <sshPassword>                                SSH password (only for Linux clusters)
--sshPublicKey <sshPublicKey>                              SSH public key (only for Linux clusters)
--rdpUserName <rdpUserName>                                RDP username (only for Windows clusters)
--rdpPassword <rdpPassword>                                RDP password (only for Windows clusters)
--rdpAccessExpiry <rdpAccessExpiry>                        RDP access expiry.
For example 12/12/2015 (only for Windows clusters)
--virtualNetworkId <virtualNetworkId>                      (Optional) Virtual network ID for the cluster.
Value is a GUID for Windows cluster and ARM resource ID for Linux cluster)
--subnetName <subnetName>                                  (Optional) Subnet for the cluster
--additionalStorageAccounts <additionalStorageAccounts>    (Optional) Additional storage accounts.
Can be multiple.
In the format of 'accountName#accountKey'.
For example, --additionalStorageAccounts "acc1#key1;acc2#key2"
--hiveMetastoreServerName <hiveMetastoreServerName>        (Optional) SQL Server name for the external metastore for Hive
--hiveMetastoreDatabaseName <hiveMetastoreDatabaseName>    (Optional) Database name for the external metastore for Hive
--hiveMetastoreUserName <hiveMetastoreUserName>            (Optional) Database username for the external metastore for Hive
--hiveMetastorePassword <hiveMetastorePassword>            (Optional) Database password for the external metastore for Hive
--oozieMetastoreServerName <oozieMetastoreServerName>      (Optional) SQL Server name for the external metastore for Oozie
--oozieMetastoreDatabaseName <oozieMetastoreDatabaseName>  (Optional) Database name for the external metastore for Oozie
--oozieMetastoreUserName <oozieMetastoreUserName>          (Optional) Database username for the external metastore for Oozie
--oozieMetastorePassword <oozieMetastorePassword>          (Optional) Database password for the external metastore for Oozie
--configurationPath <configurationPath>                    (Optional) HDInsight cluster configuration file path
-s, --subscription <id>                                    The subscription id
--tags <tags>                                              Tags to set to the cluster.
Can be multiple.
In the format of 'name=value'.
Name is required and value is optional.
For example, --tags tag1=value1;tag2
```


**Comando para excluir um cluster**

```azurecli
hdinsight cluster delete [options] <clusterName>
```

**Comando para mostrar detalhes do cluster**

```azurecli
hdinsight cluster show [options] <clusterName>
```

**Comando para listar todos os clusters (em um grupo de recursos específico, se fornecido)**

```azurecli
hdinsight cluster list [options]
```

**Comando para redimensionar um cluster**

```azurecli
hdinsight cluster resize [options] <clusterName> <targetInstanceCount>
```

**Comando para habilitar o acesso HTTP a um cluster**

```azurecli
hdinsight cluster enable-http-access [options] <clusterName> <userName> <password>
```

**Comando para desabilitar o acesso HTTP a um cluster**

```azurecli
hdinsight cluster disable-http-access [options] <clusterName>
```

**Comando para habilitar o acesso RDP a um cluster**

```azurecli
hdinsight cluster enable-rdp-access [options] <clusterName> <rdpUserName> <rdpPassword> <rdpExpiryDate>
```

**Comando para desabilitar o acesso HTTP a um cluster**

```azurecli
hdinsight cluster disable-rdp-access [options] <clusterName>
```

## <a name="insights-events-alert-rules-autoscale-settings-metrics"></a>Percepções (eventos, regras de alerta, configurações de dimensionamento automático, métricas)
**Recupera os logs de operação para uma assinatura, uma correlationId, um grupo de recursos, o recurso ou o provedor de recursos**

```azurecli
insights logs list [options]
```

## <a name="locations"></a>Localizações 
**Lista os locais disponíveis**

```azurecli
location list [options]
```

## <a name="network-resources"></a>Recursos de rede
**Comandos para gerenciar redes virtuais**

```azurecli
network vnet create [options] <resource-group> <name> <location>
```
Cria uma rede virtual. No exemplo a seguir, criamos uma rede virtual denominada newvnet para o grupo de recursos myresourcegroup na região Oeste dos EUA.

```azurecli
azure network vnet create myresourcegroup newvnet "west us"
info:    Executing command network vnet create
+ Looking up virtual network "newvnet"
+ Creating virtual network "newvnet"
    Loading virtual network state
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
data:    Name:                 newvnet
data:    Type:                 Microsoft.Network/virtualNetworks
data:    Location:             westus
data:    Tags:
data:    Provisioning state:   Succeeded
data:    Address prefixes:
data:     10.0.0.0/8
data:    DNS servers:
data:    Subnets:
data:
info:    network vnet create command OK
```


Opções de parâmetro:

```txt
-h, --help                                 output usage information
-v, --verbose                              use verbose output
--json                                     use json output
-g, --resource-group <resource-group>      the name of the resource group
-n, --name <name>                          the name of the virtual network
-l, --location <location>                  the location
-a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network
For example -a 10.0.0.0/24,10.0.1.0/24.
Default value is 10.0.0.0/8

-d, --dns-servers <dns-servers>            the comma separated list of DNS servers IP addresses
-t, --tags <tags>                          the tags set on this virtual network.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
-s, --subscription <subscription>          the subscription identifier
```
<BR>

```azurecli
network vnet set [options] <resource-group> <name>
```

Atualiza uma configuração de rede virtual dentro de um grupo de recursos.

```azurecli
azure network vnet set myresourcegroup newvnet

info:    Executing command network vnet set
+ Looking up virtual network "newvnet"
+ Updating virtual network "newvnet"
+ Loading virtual network state
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
data:    Name:                 newvnet
data:    Type:                 Microsoft.Network/virtualNetworks
data:    Location:             westus
data:    Tags:
data:    Provisioning state:   Succeeded
data:    Address prefixes:
data:     10.0.0.0/8
data:    DNS servers:
data:    Subnets:
data:
info:    network vnet set command OK
```

Opções de parâmetro:

```txt
-h, --help                                 output usage information
-v, --verbose                              use verbose output
--json                                     use json output
-g, --resource-group <resource-group>      the name of the resource group
-n, --name <name>                          the name of the virtual network
-a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network.
For example -a 10.0.0.0/24,10.0.1.0/24.
This list will be appended to the current list of address prefixes.
The address prefixes in this list should not overlap between them.
The address prefixes in this list should not overlap with existing address prefixes in the vnet.

-d, --dns-servers [dns-servers]            the comma separated list of DNS servers IP addresses.
This list will be appended to the current list of DNS server IP addresses.

-t, --tags <tags>                          the tags set on this virtual network.
Can be multiple. In the format of "name=value".
Name is required and value is optional. For example, -t tag1=value1;tag2.
This list will be appended to the current list of tags

--no-tags                                  remove all existing tags
-s, --subscription <subscription>          the subscription identifier
```
<BR>

```azurecli
network vnet list [options] <resource-group>
```

O comando lista todas as redes virtuais em um grupo de recursos.

```azurecli
C:\>azure network vnet list myresourcegroup

info:    Executing command network vnet list
+ Listing virtual networks
    data:    ID
    Name      Location  Address prefixes  DNS servers
data:    -------------------------------------------------------------------
------  --------  --------  ----------------  -----------
data:    /subscriptions/###############################/resourceGroups/
wvnet   newvnet   westus    10.0.0.0/8
info:    network vnet list command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network vnet show [options] <resource-group> <name>
```
O comando mostra as propriedades de rede virtual em um grupo de recursos.

```azurecli
azure network vnet show -g myresourcegroup -n newvnet

info:    Executing command network vnet show
+ Looking up virtual network "newvnet"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
data:    Name:                 newvnet
data:    Type:                 Microsoft.Network/virtualNetworks
data:    Location:             westus
data:    Tags:
data:    Provisioning state:   Succeeded
data:    Address prefixes:
data:     10.0.0.0/8
data:    DNS servers:
data:    Subnets:
data:
info:    network vnet show command OK
```
<BR>

```azurecli
network vnet delete [options] <resource-group> <name>
```
O comando remove uma rede virtual.

```azurecli
azure network vnet delete myresourcegroup newvnetX

info:    Executing command network vnet delete
+ Looking up virtual network "newvnetX"
Delete virtual network newvnetX? [y/n] y
+ Deleting virtual network "newvnetX"
info:    network vnet delete command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the virtual network
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```


**Comandos para gerenciar sub-redes de redes virtuais**

```azurecli
network vnet subnet create [options] <resource-group> <vnet-name> <name>
```

Adiciona outra sub-rede para uma rede virtual existente.

```azurecli
azure network vnet subnet create -g myresourcegroup --vnet-name newvnet -n subnet --address-prefix 10.0.1.0/24

info:    Executing command network vnet subnet create
+ Looking up the subnet "subnet"
+ Creating subnet "subnet"
+ Looking up the subnet "subnet"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
data:    Name:                      subnet
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet create command OK
```

Opções de parâmetro:

```txt
-h, --help                                                       output usage information
-v, --verbose                                                    use verbose output
    --json                                                           use json output
-g, --resource-group <resource-group>                            the name of the resource group
-e, --vnet-name <vnet-name>                                      the name of the virtual network
-n, --name <name>                                                the name of the subnet
-a, --address-prefix <address-prefix>                            the address prefix
-w, --network-security-group-id <network-security-group-id>      the network security group identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
-o, --network-security-group-name <network-security-group-name>  the network security group name
-s, --subscription <subscription>                                the subscription identifier
```

<BR>

```azurecli
network vnet subnet set [options] <resource-group> <vnet-name> <name>
```

Define uma sub-rede de rede virtual específica dentro de um grupo de recursos.

```azurecli
C:\>azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet set
+ Looking up the subnet "subnet1"
+ Setting subnet "subnet1"
+ Looking up the subnet "subnet1"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
data:    Name:                      subnet1
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet set command OK
```
<BR>

```azurecli
network vnet subnet list [options] <resource-group> <vnet-name>
```

Lista todas as sub-redes de rede virtual para uma rede virtual específica dentro de um grupo de recursos.

```azurecli
azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet set
+ Looking up the subnet "subnet1"
+ Setting subnet "subnet1"
+ Looking up the subnet "subnet1"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
data:    Name:                      subnet1
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet set command OK
```
<BR>

```azurecli
network vnet subnet show [options] <resource-group> <vnet-name> <name>
```
Exibe as propriedades de sub-rede da rede virtual

```azurecli
azure network vnet subnet show -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet show
+ Looking up the subnet "subnet1"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft
.Network/virtualNetworks/newvnet/subnets/subnet1
data:    Name:                      subnet1
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet show command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-e, --vnet-name <vnet-name>            the name of the virtual network
-n, --name <name>                      the name of the subnet
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network vnet subnet delete [options] <resource-group> <vnet-name> <subnet-name>
```
Remove uma sub-rede de uma rede virtual existente.

```azurecli
azure network vnet subnet delete -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet delete
+ Looking up the subnet "subnet1"
Delete subnet "subnet1"? [y/n] y
+ Deleting subnet "subnet1"
info:    network vnet subnet delete command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-e, --vnet-name <vnet-name>            the name of the virtual network
-n, --name <name>                      the subnet name
-s, --subscription <subscription>      the subscription identifier
-q, --quiet                            quiet mode, do not ask for delete confirmation
```

**Comandos para gerenciar os balanceadores de carga**

```azurecli
network lb create [options] <resource-group> <name> <location>
```
Cria um conjunto de balanceadores de carga.

```azurecli
azure network lb create -g myresourcegroup -n mylb -l westus

info:    Executing command network lb create
+ Looking up the load balancer "mylb"
+ Creating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
data:    Name:                         mylb
data:    Type:                         Microsoft.Network/loadBalancers
data:    Location:                     westus
data:    Provisioning state:           Succeeded
info:    network lb create command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the load balancer
-l, --location <location>              the location
-t, --tags <tags>                      the list of tags.
    Can be multiple. In the format of "name=value".
    Name is required and value is optional. For example, -t tag1=value1;tag2
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb list [options] <resource-group>
```
Lista os recursos do balanceador de carga dentro de um grupo de recursos.

```azurecli
azure network lb list myresourcegroup

info:    Executing command network lb list
+ Getting the load balancers
data:    Name  Location
data:    ----  --------
data:    mylb  westus
info:    network lb list command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb show [options] <resource-group> <name>
```

Exibe informações do balanceador de carga de um balanceador de carga específico dentro de um grupo de recursos

```azurecli
azure network lb show myresourcegroup mylb -v

info:    Executing command network lb show
verbose: Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
data:    Name:                         mylb
data:    Type:                         Microsoft.Network/loadBalancers
data:    Location:                     westus
data:    Provisioning state:           Succeeded
info:    network lb show command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb delete [options] <resource-group> <name>
```

Exclui recursos do balanceador de carga.

```azurecli
azure network lb delete  myresourcegroup mylb

info:    Executing command network lb delete
+ Looking up the load balancer "mylb"
Delete load balancer "mylb"? [y/n] y
+ Deleting load balancer "mylb"
info:    network lb delete command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the load balancer
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**Comandos para gerenciar testes de um balanceador de carga**

```azurecli
network lb probe create [options] <resource-group> <lb-name> <name>
```

Crie a configuração de teste para o status de integridade no balanceador de carga. Lembre-se de executar esse comando, o balanceador de carga requer um recurso de ip front-end (Check-out de comando "azure rede ip front-end" para atribuir um endereço ip ao balanceador de carga).

```azurecli
azure network lb probe create -g myresourcegroup --lb-name mylb -n mylbprobe --protocol tcp --port 80 -i 300

info:    Executing command network lb probe create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
info:    network lb probe create command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the probe
-p, --protocol <protocol>              the probe protocol
-o, --port <port>                      the probe port
-f, --path <path>                      the probe path
-i, --interval <interval>              the probe interval in seconds
-c, --count <count>                    the number of probes
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb probe set [options] <resource-group> <lb-name> <name>
```

Atualiza uma investigação do balanceador de carga existente com novos valores para o mesmo.

```azurecli
azure network lb probe set -g myresourcegroup -l mylb -n mylbprobe -p mylbprobe1 -p TCP -o 443 -i 300

info:    Executing command network lb probe set
    + Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
info:    network lb probe set command OK
```

Opções de parâmetro

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the probe
-e, --new-probe-name <new-probe-name>  the new name of the probe
-p, --protocol <protocol>              the new value for probe protocol
-o, --port <port>                      the new value for probe port
-f, --path <path>                      the new value for probe path
-i, --interval <interval>              the new value for probe interval in seconds
-c, --count <count>                    the new value for number of probes
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb probe list [options] <resource-group> <lb-name>
```

Lista as propriedades de teste para um conjunto de balanceadores de carga.

```azurecli
C:\>azure network lb probe list -g myresourcegroup -l mylb

info:    Executing command network lb probe list
+ Looking up the load balancer "mylb"
data:    Name       Protocol  Port  Path  Interval  Count
data:    ---------  --------  ----  ----  --------  -----
data:    mylbprobe  Tcp       443         300       2
info:    network lb probe list command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

```azurecli
network lb probe delete [options] <resource-group> <lb-name> <name>
```
Remove o teste criado para o balanceador de carga.

```azurecli
azure network lb probe delete -g myresourcegroup -l mylb -n mylbprobe

info:    Executing command network lb probe delete
+ Looking up the load balancer "mylb"
Delete a probe "mylbprobe?" [y/n] y
+ Updating load balancer "mylb"
info:    network lb probe delete command OK
```

**Comandos para gerenciar as configurações de ip de front-end de um balanceador de carga**

```azurecli
network lb frontend-ip create [options] <resource-group> <lb-name> <name>
```
Cria uma configuração de IP de front-end para um conjunto existente de balanceadores de carga.

```azurecli
azure network lb frontend-ip create -g myresourcegroup --lb-name mylb -n myfrontendip -o Dynamic -e subnet -m newvnet

info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "mylb"
+ Looking up the subnet "subnet"
+ Creating frontend IP configuration "myfrontendip"
+ Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/Myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
/frontendIPConfigurations/myfrontendip
data:    Name:                         myfrontendip
data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
data:    Provisioning state:           Succeeded
data:    Private IP allocation method: Dynamic
data:    Private IP address:           10.0.1.4
data:    Subnet:                       id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
data:    Public IP address:
data:    Inbound NAT rules
data:    Outbound NAT rules
data:    Load balancing rules
data:
info:    network lb frontend-ip create command OK
```

<BR>

```azurecli
network lb frontend-ip set [options] <resource-group> <lb-name> <name>
```

Atualiza uma configuração existente de um IP front-end. O comando a seguir adiciona um IP público chamado mypubip5 para um IP de front-end de balanceador carga existente chamado myfrontendip.

```azurecli
azure network lb frontend-ip set -g myresourcegroup --lb-name mylb -n myfrontendip -i mypubip5

info:    Executing command network lb frontend-ip set
+ Looking up the load balancer "mylb"
+ Looking up the public ip "mypubip5"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Name:                         myfrontendip
data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
data:    Provisioning state:           Succeeded
data:    Private IP allocation method: Dynamic
data:    Private IP address:
data:    Subnet:
data:    Public IP address:            id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypubip5
data:    Inbound NAT rules
data:    Outbound NAT rules
data:    Load balancing rules
data:
info:    network lb frontend-ip set command OK
```

Opções de parâmetro:

```txt
-h, --help                                                         output usage information
-v, --verbose                                                      use verbose output
--json                                                             use json output
-g, --resource-group <resource-group>                              the name of the resource group
-l, --lb-name <lb-name>                                            the name of the load balancer
-n, --name <name>                                                  the name of the frontend ip configuration
-a, --private-ip-address <private-ip-address>                      the private ip address
-o, --private-ip-allocation-method <private-ip-allocation-method>  the private ip allocation method [Static, Dynamic]
-u, --public-ip-id <public-ip-id>                                  the public ip identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
-i, --public-ip-name <public-ip-name>                              the public ip name.
This public ip must exist in the same resource group as the lb.
Please use public-ip-id if that is not the case.
-b, --subnet-id <subnet-id>                                        the subnet id.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/VirtualNetworks/<vnet-name>/subnets/<subnet-name>
-e, --subnet-name <subnet-name>                                    the subnet name
-m, --vnet-name <vnet-name>                                        the virtual network name.
This virtual network must exist in the same resource group as the lb.
Please use subnet-id if that is not the case.
-s, --subscription <subscription>                                  the subscription identifier
```

<BR>

```azurecli
network lb frontend-ip list [options] <resource-group> <lb-name>
```

Lista todos os recursos IP front-end configurados para o balanceador de carga.

```azurecli
azure network lb frontend-ip list -g myresourcegroup -l mylb

info:    Executing command network lb frontend-ip list
+ Looking up the load balancer "mylb"
data:    Name         Provisioning state  Private IP allocation method  Subnet
data:    -----------  ------------------  ----------------------------  ------
data:    myprivateip  Succeeded           Dynamic
info:    network lb frontend-ip list command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb frontend-ip delete [options] <resource-group> <lb-name> <name>
```
Exclui o objeto IP de front-end associado ao balanceador de carga

```azurecli
network lb frontend-ip delete -g myresourcegroup -l mylb -n myfrontendip
info:    Executing command network lb frontend-ip delete
+ Looking up the load balancer "mylb"
Delete frontend ip configuration "myfrontendip"? [y/n] y
+ Updating load balancer "mylb"
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the frontend ip configuration
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**Comandos para gerenciar pools de endereços de back-end de um balanceador de carga**

```azurecli
network lb address-pool create [options] <resource-group> <lb-name> <name>
```

Cria um pool de endereços de back-end para um balanceador de carga.

```azurecli
azure network lb address-pool create -g myresourcegroup --lb-name mylb -n myaddresspool

info:    Executing command network lb address-pool create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourgroup/providers/Microso.Network/loadBalancers/mylb/backendAddressPools/myaddresspool
data:    Name:                      myaddresspool
data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
data:    Provisioning state:        Succeeded
data:    Backend IP configurations:
data:    Load balancing rules:
data:
info:    network lb address-pool create command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the backend address pool
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb address-pool list [options] <resource-group> <lb-name>
```

Lista o intervalo de pool de endereços IP de back-end para um grupo de recursos específicos

```azurecli
azure network lb address-pool list -g myresourcegroup -l mylb

info:    Executing command network lb address-pool list
+ Looking up the load balancer "mylb"
data:    Name           Provisioning state
data:    -------------  ------------------
data:    mybackendpool  Succeeded
info:    network lb address-pool list command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb address-pool delete [options] <resource-group> <lb-name> <name>
```

Remove o recurso de intervalo de pool de IP de back-end do balanceador de carga.

```azurecli
azure network lb address-pool delete -g myresourcegroup -l mylb -n mybackendpool

info:    Executing command network lb address-pool delete
+ Looking up the load balancer "mylb"
Delete backend address pool "mybackendpool"? [y/n] y
+ Updating load balancer "mylb"
info:    network lb address-pool delete command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the backend address pool
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**Comandos para gerenciar as regras dos balanceadores de carga**

```azurecli
network lb rule create [options] <resource-group> <lb-name> <name>
```
Crie regras para o balanceador de carga.

Você pode criar uma regra para o balanceador de carga configurando o ponto de extremidade de front-end para o balanceador de carga e o intervalo de pool de endereços de back-end para receber o tráfego de rede de entrada. As configurações também incluem as portas para o ponto de extremidade do IP de front-end e as portas para o intervalo de pool de endereços de back-end.

O exemplo a seguir mostra como criar uma regra para o balanceador de carga, o ponto de extremidade do front-end escutando a porta TCP 80 e o tráfego de rede do balanceamento de carga enviando para a porta 8080 para o intervalo de pool de endereços de back-end.

```azurecli
azure network lb rule create -g myresourcegroup -l mylb -n mylbrule -p tcp -f 80 -b 8080 -i 10


info:    Executing command network lb rule create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Loading rule state
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mylbrule
data:    Name:                      mylbrule
data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
data:    Provisioning state:        Succeeded
data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
data:    Protocol:                  Tcp
data:    Frontend port:             80
data:    Backend port:              8080
data:    Enable floating IP:        false
data:    Idle timeout in minutes:   10
data:    Probes
data:
info:    network lb rule create command OK
```

<BR>

```azurecli
network lb rule set [options] <resource-group> <lb-name> <name>
```

Atualiza uma regra de balanceador de carga existente definida em um grupo de recursos específico. No exemplo a seguir o nome da regra mylbrule é alterado para mynewlbrule.

```azurecli
azure network lb rule set -g myresourcegroup -l mylb -n mylbrule -r mynewlbrule -p tcp -f 80 -b 8080 -i 10 -t myfrontendip -o mybackendpool

info:    Executing command network lb rule set
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Loading rule state
data:    Id:                        /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mynewlbrule
data:    Name:                      mynewlbrule
data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
data:    Provisioning state:        Succeeded
data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
data:    Protocol:                  Tcp
data:    Frontend port:             80
data:    Backend port:              8080
data:    Enable floating IP:        false
data:    Idle timeout in minutes:   10
data:    Probes
data:
info:    network lb rule set command OK
```

Opções de parâmetro:

```txt
-h, --help                                         output usage information
-v, --verbose                                      use verbose output
--json                                             use json output
-g, --resource-group <resource-group>              the name of the resource group
-l, --lb-name <lb-name>                            the name of the load balancer
-n, --name <name>                                  the name of the rule
-r, --new-rule-name <new-rule-name>                new rule name
-p, --protocol <protocol>                          the rule protocol
-f, --frontend-port <frontend-port>                the frontend port
-b, --backend-port <backend-port>                  the backend port
-e, --enable-floating-ip <enable-floating-ip>      enable floating point ip
-i, --idle-timeout <idle-timeout>                  the idle timeout in minutes
-a, --probe-name [probe-name]                      the name of the probe defined in the same load balancer
-t, --frontend-ip-name <frontend-ip-name>          the name of the frontend ip configuration in the same load balancer
-o, --backend-address-pool <backend-address-pool>  name of the backend address pool defined in the same load balancer
-s, --subscription <subscription>                  the subscription identifier
```


```azurecli
network lb rule list [options] <resource-group> <lb-name>
```

Lista todas as regras do balanceador de carga configuradas para um balanceador de carga em um grupo de recursos específico.

```azurecli
azure network lb rule list -g myresourcegroup -l mylb

info:    Executing command network lb rule list
+ Looking up the load balancer "mylb"
data:    Name         Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend address pool  Probe data

data:    mynewlbrule  Succeeded           Tcp       80             8080          false               10                       /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
info:    network lb rule list command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

```azurecli
network lb rule delete [options] <resource-group> <lb-name> <name>
```

Exclui uma regra de balanceador de carga.

```azurecli
azure network lb rule delete -g myresourcegroup -l mylb -n mynewlbrule

info:    Executing command network lb rule delete
+ Looking up the load balancer "mylb"
Delete load balancing rule mynewlbrule? [y/n] y
+ Updating load balancer "mylb"
info:    network lb rule delete command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the rule
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**Comandos para gerenciar as regras NAT de entrada dos balanceadores de carga**

```azurecli
network lb inbound-nat-rule create [options] <resource-group> <lb-name> <name>
```
Cria uma regra de NAT de entrada para o balanceador de carga.

No exemplo a seguir, criamos uma regra NAT de IP de front-end (que foi anteriormente definida usando o comando "azure network frontend-ip") com uma porta de escuta de entrada e a porta de saída que o balanceador de carga usa para enviar o tráfego de rede.

```azurecli
azure network lb inbound-nat-rule create -g myresourcegroup -l mylb -n myinboundnat -p tcp -f 80 -b 8080 -i myfrontendip

info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
data:    Name:                      myinboundnat
data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
data:    Provisioning state:        Succeeded
data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend IP configuration
data:    Protocol                   Tcp
data:    Frontend port              80
data:    Backend port               8080
data:    Enable floating IP         false
info:    network lb inbound-nat-rule create command OK
```

Opções de parâmetro:

```txt
-h, --help                                     output usage information
-v, --verbose                                  use verbose output
--json                                         use json output
-g, --resource-group <resource-group>          the name of the resource group
-l, --lb-name <lb-name>                        the name of the load balancer
-n, --name <name>                              the name of the inbound NAT rule
-p, --protocol <protocol>                      the rule protocol [tcp,udp]
-f, --frontend-port <frontend-port>            the frontend port [0-65535]
-b, --backend-port <backend-port>              the backend port [0-65535]
-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
-m, --vm-id <vm-id>                            the VM id.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
-a, --vm-name <vm-name>                        the VM name.This VM must exist in the same resource group as the lb.
Please use vm-id if that is not the case.
this parameter will be ignored if --vm-id is specified
-s, --subscription <subscription>              the subscription identifier
```
<BR>

```azurecli
network lb inbound-nat-rule set [options] <resource-group> <lb-name> <name>
```
Atualiza uma regra de nat de entrada existente. No exemplo a seguir alteramos a porta de escuta de entrada de 80 para 81.

```azurecli
azure network lb inbound-nat-rule set -g group-1 -l mylb -n myinboundnat -p tcp -f 81 -b 8080 -i myfrontendip

info:    Executing command network lb inbound-nat-rule set
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                        /subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
data:    Name:                      myinboundnat
data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
data:    Provisioning state:        Succeeded
data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend IP configuration
data:    Protocol                   Tcp
data:    Frontend port              81
data:    Backend port               8080
data:    Enable floating IP         false
info:    network lb inbound-nat-rule set command OK
```

Opções de parâmetro:

```txt
-h, --help                                     output usage information
-v, --verbose                                  use verbose output
--json                                         use json output
-g, --resource-group <resource-group>          the name of the resource group
-l, --lb-name <lb-name>                        the name of the load balancer
-n, --name <name>                              the name of the inbound NAT rule
-p, --protocol <protocol>                      the rule protocol [tcp,udp]
-f, --frontend-port <frontend-port>            the frontend port [0-65535]
-b, --backend-port <backend-port>              the backend port [0-65535]
-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
-m, --vm-id [vm-id]                            the VM id.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
-a, --vm-name <vm-name>                        the VM name.
This virtual machine must exist in the same resource group as the lb.
Please use vm-id if that is not the case
-s, --subscription <subscription>              the subscription identifier
```
<BR>

```azurecli
network lb inbound-nat-rule list [options] <resource-group> <lb-name>
```

Lista todas as regras de nat de entrada para o balanceador de carga.

```azurecli
azure network lb inbound-nat-rule list -g myresourcegroup -l mylb

info:    Executing command network lb inbound-nat-rule list
+ Looking up the load balancer "mylb"
data:    Name          Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend IP configuration
data:    ------------  ------------------  --------  -------------  ------------  ------------------  -----------------------  ---
---------------------
data:    myinboundnat  Succeeded           Tcp       81             8080          false               4

info:    network lb inbound-nat-rule list command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb inbound-nat-rule delete [options] <resource-group> <lb-name> <name>
```

Exclui a regra NAT para o balanceador de carga em um grupo de recursos específico.

```azurecli
azure network lb inbound-nat-rule delete -g myresourcegroup -l mylb -n myinboundnat

info:    Executing command network lb inbound-nat-rule delete
+ Looking up the load balancer "mylb"
Delete inbound NAT rule "myinboundnat?" [y/n] y
+ Updating load balancer "mylb"
info:    network lb inbound-nat-rule delete command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the inbound NAT rule
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**Comandos para gerenciar endereços ip públicos**

```azurecli
network public-ip create [options] <resource-group> <name> <location>
```
Cria um recurso de ip público. Você criará o recurso ip público e o associará a um nome de domínio.

```azurecli
azure network public-ip create -g myresourcegroup -n mytestpublicip1 -l eastus -d azureclitest -a "Dynamic"
info:    Executing command network public-ip create
+ Looking up the public ip "mytestpublicip1"
+ Creating public ip address "mytestpublicip1"
+ Looking up the public ip "mytestpublicip1"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
data:    Name:                 mytestpublicip1
data:    Type:                 Microsoft.Network/publicIPAddresses
data:    Location:             eastus
data:    Provisioning state:   Succeeded
data:    Allocation method:    Dynamic
data:    Idle timeout:         4
data:    Domain name label:    azureclitest
data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
info:    network public-ip create command OK
```


Opções de parâmetro:

```txt
-h, --help                                   output usage information
-v, --verbose                                use verbose output
--json                                       use json output
-g, --resource-group <resource-group>        the name of the resource group
-n, --name <name>                            the name of the public ip
-l, --location <location>                    the location
-d, --domain-name-label <domain-name-label>  the domain name label.
This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
-a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
-i, --idletimeout <idletimeout>              the idle timeout in minutes
-f, --reverse-fqdn <reverse-fqdn>            the reverse fqdn
-t, --tags <tags>                            the list of tags.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
-s, --subscription <subscription>            the subscription identifier
```
<br>

```azurecli
network public-ip set [options] <resource-group> <name>
```
Atualiza as propriedades de um recurso ip público existente. No exemplo a seguir alteramos o endereço IP público de Dinâmico para Estático.

```azurecli
azure network public-ip set -g group-1 -n mytestpublicip1 -d azureclitest -a "Static"
info:    Executing command network public-ip set
+ Looking up the public ip "mytestpublicip1"
+ Updating public ip address "mytestpublicip1"
+ Looking up the public ip "mytestpublicip1"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
data:    Name:                 mytestpublicip1
data:    Type:                 Microsoft.Network/publicIPAddresses
data:    Location:             eastus
data:    Provisioning state:   Succeeded
data:    Allocation method:    Static
data:    Idle timeout:         4
data:    IP Address:           (static IP address)
data:    Domain name label:    azureclitest
data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
info:    network public-ip set command OK
```

Opções de parâmetro:

```txt
-h, --help                                   output usage information
-v, --verbose                                use verbose output
--json                                       use json output
-g, --resource-group <resource-group>        the name of the resource group
-n, --name <name>                            the name of the public ip
-d, --domain-name-label [domain-name-label]  the domain name label.
This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
-a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
-i, --idletimeout <idletimeout>              the idle timeout in minutes
-f, --reverse-fqdn [reverse-fqdn]            the reverse fqdn
-t, --tags <tags>                            the list of tags.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
--no-tags                                    remove all existing tags
-s, --subscription <subscription>            the subscription identifier
```

<br>

```azurecli
network public-ip list [options] <resource-group>
```
Lista todos os recursos de IP público dentro de um grupo de recursos.

```azurecli
azure network public-ip list -g myresourcegroup

info:    Executing command network public-ip list
+ Getting the public ip addresses
data:    Name             Location  Allocation  IP Address    Idle timeout  DNS Name
data:    ---------------  --------  ----------  ------------  ------------  -------------------------------------------
data:    mypubip5         westus    Dynamic                   4             "domain name".westus.cloudapp.azure.com
data:    myPublicIP       eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
data:    mytestpublicip   eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
data:    mytestpublicip1  eastus   Static (Static IP address) 4             azureclitest.eastus.cloudapp.azure.com
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network public-ip show [options] <resource-group> <name>
```

Exibe as propriedades de ip público de um recurso de ip público dentro de um grupo de recursos.

```azurecli
azure network public-ip show -g myresourcegroup -n mytestpublicip

info:    Executing command network public-ip show
+ Looking up the public ip "mytestpublicip1"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip
data:    Name:                 mytestpublicip
data:    Type:                 Microsoft.Network/publicIPAddresses
data:    Location:             eastus
data:    Provisioning state:   Succeeded
data:    Allocation method:    Static
data:    Idle timeout:         4
data:    IP Address:           (static IP address)
data:    Domain name label:    azureclitest
data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
info:    network public-ip show command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the public IP
-s, --subscription <subscription>      the subscription identifier
```


```azurecli
network public-ip delete [options] <resource-group> <name>
```

Exclui um recurso de ip público.

```azurecli
azure network public-ip delete -g group-1 -n mypublicipname
info:    Executing command network public-ip delete
+ Looking up the public ip "mypublicipname"
Delete public ip address "mypublicipname"? [y/n] y
+ Deleting public ip address "mypublicipname"
info:    network public-ip delete command OK
```

Opções de parâmetro:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the public IP
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```


**Comandos para gerenciar as interfaces de rede**

```azurecli
network nic create [options] <resource-group> <name> <location>
```
Cria um recurso chamado de interface de rede (NIC) que pode ser usado para balanceadores de carga ou associado a uma máquina virtual.

```azurecli
azure network nic create -g myresourcegroup -l eastus -n testnic1 --subnet-name subnet-1 --subnet-vnet-name myvnet

info:    Executing command network nic create
+ Looking up the network interface "testnic1"
+ Looking up the subnet "subnet-1"
+ Creating network interface "testnic1"
+ Looking up the network interface "testnic1"
data:    Id:                     /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/networkInterfaces/testnic1
data:    Name:                   testnic1
data:    Type:                   Microsoft.Network/networkInterfaces
data:    Location:               eastus
data:    Provisioning state:     Succeeded
data:    IP configurations:
data:       Name:                         NIC-config
data:       Provisioning state:           Succeeded
data:       Private IP address:           10.0.0.5
data:       Private IP Allocation Method: Dynamic
data:       Subnet:                       /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/virtualNetworks/myVNET/subnets/Subnet-1
```

Opções de parâmetro:

```txt
-h, --help                                                       output usage information
-v, --verbose                                                    use verbose output
--json                                                           use json output
-g, --resource-group <resource-group>                            the name of the resource group
-n, --name <name>                                                the name of the network interface
-l, --location <location>                                        the location
-w, --network-security-group-id <network-security-group-id>      the network security group identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
-o, --network-security-group-name <network-security-group-name>  the network security group name.
This network security group must exist in the same resource group as the nic.
Please use network-security-group-id if that is not the case.
-i, --public-ip-id <public-ip-id>                                the public IP identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
-p, --public-ip-name <public-ip-name>                            the public IP name.
This public ip must exist in the same resource group as the nic.
Please use public-ip-id if that is not the case.
-a, --private-ip-address <private-ip-address>                    the private IP address
-u, --subnet-id <subnet-id>                                      the subnet identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/subnets/<subnet-name>
--subnet-name <subnet-name>                                      the subnet name
-m, --subnet-vnet-name <subnet-vnet-name>                        the vnet name under which subnet-name exists
-t, --tags <tags>                                                the comma separated list of tags.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
-s, --subscription <subscription>                                the subscription identifier
data:
info:    network nic create command OK
```

<BR>

```azurecli
network nic set [options] <resource-group> <name>
network nic list [options] <resource-group>
network nic show [options] <resource-group> <name>
network nic delete [options] <resource-group> <name>
```

**Comandos para gerenciar grupos de segurança de rede**

```azurecli
network nsg create [options] <resource-group> <name> <location>
network nsg set [options] <resource-group> <name>
network nsg list [options] <resource-group>
network nsg show [options] <resource-group> <name>
network nsg delete [options] <resource-group> <name>
```

**Comandos para gerenciar regras de segurança de rede**

```azurecli
network nsg rule create [options] <resource-group> <nsg-name> <name>
network nsg rule set [options] <resource-group> <nsg-name> <name>
network nsg rule list [options] <resource-group> <nsg-name>
network nsg rule show [options] <resource-group> <nsg-name> <name>
network nsg rule delete [options] <resource-group> <nsg-name> <name>
```

**Comandos para gerenciar o perfil do gerenciador de tráfego**

```azurecli
network traffic-manager profile create [options] <resource-group> <name>
network traffic-manager profile set [options] <resource-group> <name>
network traffic-manager profile list [options] <resource-group>
network traffic-manager profile show [options] <resource-group> <name>
network traffic-manager profile delete [options] <resource-group> <name>
network traffic-manager profile is-dns-available [options] <resource-group> <relative-dns-name>
```

**Comandos para gerenciar os pontos de extremidade do gerenciador de tráfego**

```azurecli
network traffic-manager profile endpoint create [options] <resource-group> <profile-name> <name> <endpoint-location>
network traffic-manager profile endpoint set [options] <resource-group> <profile-name> <name>
network traffic-manager profile endpoint delete [options] <resource-group> <profile-name> <name>
```

**Comandos para gerenciar gateways de redes virtuais**

```azurecli
network gateway list [options] <resource-group>
```

## <a name="resource-provider-registrations"></a>Registros do provedor de recursos
**Liste os provedores registrados atualmente no Resource Manager**

```azurecli
provider list [options]
```

**Mostra os detalhes sobre o namespace do provedor solicitado**

```azurecli
provider show [options] <namespace>
```

**Registra o provedor com a assinatura**

```azurecli
provider register [options] <namespace>
```

**Cancela o registro do provedor com a assinatura**

```azurecli
provider unregister [options] <namespace>
```

## <a name="resources"></a>Recursos
**Cria um recurso em um grupo de recursos**

```azurecli
resource create [options] <resource-group> <name> <resource-type> <location> <api-version>
```

**Atualiza um recurso em um grupo de recursos sem parâmetros ou modelos**

```azurecli
resource set [options] <resource-group> <name> <resource-type> <properties> <api-version>
```

**Lista os recursos**

```azurecli
resource list [options] [resource-group]
```

**Obtém um recurso dentro de um grupo de recursos ou assinatura**

```azurecli
resource show [options] <resource-group> <name> <resource-type> <api-version>
```

**Exclui um recurso em um grupo de recursos**

```azurecli
resource delete [options] <resource-group> <name> <resource-type> <api-version>
```

## <a name="azure-roles"></a>Funções do Azure
**Obtenha todas as definições de função disponíveis**

```azurecli
role list [options]
```

**Obtenha uma definição de função disponível**

```azurecli
role show [options] [name]
```

**Comandos para gerenciar sua atribuição de função**

```azurecli
role assignment create [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
role assignment list [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
role assignment delete [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
```

## <a name="storage-objects"></a>Objetos de armazenamento
**Comandos para gerenciar suas contas de Armazenamento**

```azurecli
storage account list [options]
storage account show [options] <name>
storage account create [options] <name>
storage account set [options] <name>
storage account delete [options] <name>
```

**Comandos para gerenciar suas chaves de contas de Armazenamento**

```azurecli
storage account keys list [options] <name>
storage account keys renew [options] <name>
```

**Comandos para mostrar a cadeia de conexão de Armazenamento**

```azurecli
storage account connectionstring show [options] <name>
```

**Comandos para gerenciar seu contêiner de Armazenamento**

```azurecli
storage container list [options] [prefix]
storage container show [options] [container]
storage container create [options] [container]
storage container delete [options] [container]
storage container set [options] [container]
```

**Comandos para gerenciar assinaturas de acesso compartilhado do seu contêiner de Armazenamento**

```azurecli
storage container sas create [options] [container] [permissions] [expiry]
```

**Comandos para gerenciar políticas de acesso compartilhado do seu contêiner de Armazenamento**

```azurecli
storage container policy create [options] [container] [name]
storage container policy show [options] [container] [name]
storage container policy list [options] [container]
storage container policy set [options] [container] [name]
storage container policy delete [options] [container] [name]
```

**Comandos para gerenciar seus blobs de Armazenamento**

```azurecli
storage blob list [options] [container] [prefix]
storage blob show [options] [container] [blob]
storage blob delete [options] [container] [blob]
storage blob upload [options] [file] [container] [blob]
storage blob download [options] [container] [blob] [destination]
```

**Comandos para gerenciar suas operações de cópia de blob**

```azurecli
storage blob copy start [options] [sourceUri] [destContainer]
storage blob copy show [options] [container] [blob]
storage blob copy stop [options] [container] [blob] [copyid]
```

**Comandos para gerenciar a assinatura de acesso compartilhado do blob de Armazenamento**

```azurecli
storage blob sas create [options] [container] [blob] [permissions] [expiry]
```

**Comandos para gerenciar seus compartilhamentos de arquivos de Armazenamento**

```azurecli
storage share create [options] [share]
storage share show [options] [share]
storage share delete [options] [share]
storage share list [options] [prefix]
```

**Comandos para gerenciar seus arquivos de Armazenamento**

```azurecli
storage file list [options] [share] [path]
storage file delete [options] [share] [path]
storage file upload [options] [source] [share] [path]
storage file download [options] [share] [path] [destination]
```

**Comandos para gerenciar seu diretório de arquivos de Armazenamento**

```azurecli
storage directory create [options] [share] [path]
storage directory delete [options] [share] [path]
```

**Comandos para gerenciar suas filas de Armazenamento**

```azurecli
storage queue create [options] [queue]
storage queue list [options] [prefix]
storage queue show [options] [queue]
storage queue delete [options] [queue]
```

**Comandos para gerenciar assinaturas de acesso compartilhado da sua fila de Armazenamento**

```azurecli
storage queue sas create [options] [queue] [permissions] [expiry]
```

**Comandos para gerenciar políticas de acesso armazenado da sua fila de Armazenamento**

```azurecli
storage queue policy create [options] [queue] [name]
storage queue policy show [options] [queue] [name]
storage queue policy list [options] [queue]
storage queue policy set [options] [queue] [name]
storage queue policy delete [options] [queue] [name]
```

**Comandos para gerenciar as propriedades de log do Armazenamento**

```azurecli
storage logging show [options]
storage logging set [options]
```

**Comandos para gerenciar as propriedades métricas do Armazenamento**

```azurecli
storage metrics show [options]
storage metrics set [options]
```

**Comandos para gerenciar suas tabelas de armazenamento**

```azurecli
storage table create [options] [table]
storage table list [options] [prefix]
storage table show [options] [table]
storage table delete [options] [table]
```

**Comandos para gerenciar assinaturas de acesso compartilhado da sua tabela de Armazenamento**

```azurecli
storage table sas create [options] [table] [permissions] [expiry]
```

**Comandos para gerenciar políticas de acesso armazenado da sua tabela de Armazenamento**

```azurecli
storage table policy create [options] [table] [name]
storage table policy show [options] [table] [name]
storage table policy list [options] [table]
storage table policy set [options] [table] [name]
storage table policy delete [options] [table] [name]
```

## <a name="tags"></a>Marcas
**Adicione uma marca**

```azurecli
tag create [options] <name> <value>
```

**Remova uma marca inteira ou um valor de marca**

```azurecli
tag delete [options] <name> <value>
```

**Lista as informações da marca**

```azurecli
tag list [options]
```

**Obtém uma marca**

```azurecli
tag show [options] [name]
```

## <a name="virtual-machines"></a>Máquinas Virtuais
**Criar uma máquina virtual**

```azurecli
vm create [options] <resource-group> <name> <location> <os-type>
```

**Cria uma máquina virtual com recursos padrões**

```azurecli
vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password
```

> [!TIP]
> Começando na versão 0.10 da CLI, você pode fornecer um alias curto, como "UbuntuLTS" ou "Win2012R2Datacenter", como o `image-urn` para algumas imagens populares do Marketplace. Execute `azure help vm quick-create` para opções. Além disso, a partir da versão 0.10, `azure vm quick-create` usará o armazenamento premium por padrão, se ele estiver disponível na região selecionada.
> 
> 

**Listar as máquinas virtuais dentro de uma conta**

```azurecli
vm list [options]
```

**Obtém uma máquina virtual dentro de um grupo de recursos**

```azurecli
vm show [options] <resource-group> <name>
```

**Exclui uma máquina virtual dentro de um grupo de recursos**

```azurecli
vm delete [options] <resource-group> <name>
```

**Desliga uma máquina virtual dentro de um grupo de recursos**

```azurecli
vm stop [options] <resource-group> <name>
```

**Reinicia uma máquina virtual dentro de um grupo de recursos**

```azurecli
vm restart [options] <resource-group> <name>
```

**Inicia uma máquina virtual dentro de um grupo de recursos**

```azurecli
vm start [options] <resource-group> <name>
```

**Desliga uma máquina virtual dentro de um grupo de recursos e libera os recursos de computação**

```azurecli
vm deallocate [options] <resource-group> <name>
```

**Lista os tamanhos de máquina virtual disponíveis**

```azurecli
vm sizes [options]
```

**Captura a VM como uma imagem do sistema operacional ou imagem da VM**

```azurecli
vm capture [options] <resource-group> <name> <vhd-name-prefix>
```

**Define o estado da VM como Generalized**

```azurecli
vm generalize [options] <resource-group> <name>
```

**Obtém a exibição da instância da VM**

```azurecli
vm get-instance-view [options] <resource-group> <name>
```

**Permite redefinir as configurações de acesso de área de trabalho remota ou SSH em uma máquina virtual e redefinir a senha para a conta que tem autoridade sudo ou de administrador**

```azurecli
vm reset-access [options] <resource-group> <name>
```

**Atualiza a VM com novos dados**

```azurecli
vm set [options] <resource-group> <name>
```

**Comandos para gerenciar seus discos de dados de máquinas virtuais**

```azurecli
vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]
vm disk detach [options] <resource-group> <vm-name> <lun>
vm disk attach [options] <resource-group> <vm-name> [vhd-url]
```

**Comandos para gerenciar as extensões de recurso da VM**

```azurecli
vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>
vm extension get [options] <resource-group> <vm-name>
```

**Comandos para gerenciar sua máquina virtual Docker**

```azurecli
vm docker create [options] <resource-group> <name> <location> <os-type>
```

**Comandos para gerenciar as imagens de VM**

```azurecli
vm image list-publishers [options] <location>
vm image list-offers [options] <location> <publisher>
vm image list-skus [options] <location> <publisher> <offer>
vm image list [options] <location> <publisher> [offer] [sku]
```
