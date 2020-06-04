---
title: Serviço de metadados de instância do Azure
description: A interface RESTful para obter informações sobre a computação, a rede e os eventos de manutenção futura da VM do Linux.
services: virtual-machines-linux
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines-linux
ms.subservice: monitoring
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: ce3463d39b17e7099f85945caa92d1f009b696c0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649857"
---
# <a name="azure-instance-metadata-service"></a>Serviço de Metadados de Instância do Azure

O Serviço de Metadados de Instância (IMDS) do Azure fornece informações sobre instâncias da máquina virtual em execução no momento e pode ser usado para gerenciar e configurar suas máquinas virtuais.
Isso inclui o SKU, o armazenamento, as configurações de rede e os eventos de manutenção futura. Para obter uma lista completa dos dados disponíveis, confira [APIs de metadados](#metadata-apis). O Serviço de Metadados de Instância está disponível para as instâncias de VM e conjunto de dimensionamento de máquinas virtuais. Está disponível somente para a execução de VMs criadas/gerenciadas usando o [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). 

O Serviço de Metadados de Instância do Azure é um ponto de extremidade REST disponível em um endereço IP não roteável conhecido (`169.254.169.254`). Pode ser acessado somente de dentro da VM.

## <a name="security"></a>Segurança

O ponto de extremidade de metadados de instância está acessível somente dentro da instância de máquina virtual em execução em um endereço IP não roteável. Além disso, qualquer solicitação com `X-Forwarded-For`Cabeçalho é rejeitada pelo serviço.
As solicitações também devem conter um `Metadata: true` cabeçalho para garantir que a solicitação real tenha sido desejada diretamente e não faça parte de um redirecionamento não intencional.

> [!IMPORTANT]
> O Serviço de Metadados de Instância não é um canal para dados confidenciais. O ponto de extremidade está aberto para todos os processos na VM. As informações expostas por esse serviço devem ser consideradas informações compartilhadas para todos os aplicativos em execução dentro da VM.

## <a name="usage"></a>Uso

### <a name="accessing-azure-instance-metadata-service"></a>Acesso ao Serviço de Metadados de Instância do Azure

Para acessar o Serviço de Metadados de Instância, crie uma VM no [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) ou [portal do Azure](https://portal.azure.com) e siga as amostras abaixo.
Mais exemplos de como consultar o IMDS podem ser encontrados em [Amostras de metadados de instância do Azure](https://github.com/microsoft/azureimds).

Veja abaixo o código de exemplo para recuperar todos os metadados de uma instância. Para acessar a fonte de dados específica, confira a seção [API de metadados](#metadata-apis). 

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

### <a name="data-output"></a>Saída de dados

Por padrão, o serviço de metadados de instância retorna dados em formato JSON (`Content-Type: application/json`). No entanto, diferentes APIs retornam dados em formatos diferentes, se solicitado.
A tabela a seguir é uma referência de outros formatos de dados que pode oferecer suporte a APIs.

API | Formato de dados padrão | Outros formatos
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | none
/attested | json | none

Para acessar um formato de resposta não padrão, especifique o formato solicitado como um parâmetro de cadeia de caracteres de consulta na solicitação. Por exemplo:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Para nós folha, o `format=json` não funciona. Para essas consultas, `format=text` precisa ser especificado explicitamente se o formato padrão for JSON.

### <a name="versioning"></a>Controle de versão

O Serviço de Metadados de Instância tem controle de versão e a especificação da versão de API na solicitação HTTP é obrigatória.

A seguir estão as versões de serviço compatíveis: 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15.

Observe quando a nova versão for liberada. Vai demorar um pouco para ser distribuída para todas as regiões. Atualmente, a versão 2019-11-01 ainda está sendo implantada e pode não estar disponível em todas as regiões.

Conforme versões mais recentes são adicionadas, as versões mais antigas ainda podem ser acessadas para fins de compatibilidade se os scripts tiverem dependências de formatos de dados específicos.

Quando nenhuma versão é especificada, um erro é retornado com uma lista das versões mais recentes com suporte.

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. O exemplo a seguir indica a condição de erro quando a versão não é especificada, a resposta é impressa para facilitar a leitura.

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Resposta**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

## <a name="metadata-apis"></a>APIs de metadados
O IMDS contém várias interfaces de API que representam diferentes fontes de dados. 

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
instance | Confira [API de instância](#instance-api) | 2017-04-02
atestados | Confira [Dados Atestados](#attested-data) | 01-10-2018
identidade | Confira [Adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 01-02-2018
scheduledevents | Consulte [Eventos agendados](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>API de instância

A API de instância expõe os metadados importantes para as instâncias de VM, incluindo a VM, a rede e o armazenamento. As categorias a seguir podem ser acessadas por meio de instância/computação:

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
azEnvironment | Ambiente do Azure em que a VM está em execução | 01-10-2018
customData | Este recurso está desabilitado no momento e atualizaremos esta documentação quando ficar disponível | 2019-02-01
local | Região do Azure na qual a máquina virtual está sendo executada | 2017-04-02
name | Nome da VM | 2017-04-02
oferta | Oferece informações para a imagem da VM e está presente apenas para imagens implantadas na galeria de imagens do Azure | 2017-04-02
osType | Linux ou Windows | 2017-04-02
placementGroupId | [Grupo de Posicionamento](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do conjunto de dimensionamento da sua Máquina Virtual | 2017-08-01
plan | O [Plano](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) que contém o nome, produto e editor de uma VM, se for uma imagem do Azure Marketplace | 2018-04-02
platformUpdateDomain |  [Domínio de atualização](manage-availability.md) no qual a máquina virtual está sendo executada | 2017-04-02
platformFaultDomain | [Domínio de falha](manage-availability.md) no qual a máquina virtual está sendo executada | 2017-04-02
provider | Provedor da VM | 01-10-2018
publicKeys | [Coleção de Chaves Públicas](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) atribuídas à VM e aos caminhos | 2018-04-02
publicador | Publicador da imagem da máquina virtual | 2017-04-02
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/management/overview.md) para a sua Máquina Virtual | 2017-08-01
resourceId | A ID [totalmente qualificada](https://docs.microsoft.com/rest/api/resources/resources/getbyid) do recurso | 2019-03-11
sku | SKU específica para a imagem da máquina virtual | 2017-04-02
storageProfile | Confira [Perfil de armazenamento](#storage-metadata) | 2019-06-01
subscriptionId | Assinatura do Azure para a Máquina Virtual | 2017-08-01
marcas | [Marcas](../../azure-resource-manager/management/tag-resources.md) para a sua Máquina Virtual  | 2017-08-01
tagsList | Marcas formatadas como uma matriz JSON para facilitar a análise programática  | 2019-06-04
version | Versão da imagem da máquina virtual | 2017-04-02
vmId | [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a máquina virtual | 2017-04-02
vmScaleSetName | [Nome do conjunto de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) do seu conjunto de dimensionamento de máquinas virtuais | 2017-12-01
vmSize | [Tamanho da VM](sizes.md) | 2017-04-02
zona | [Zona de Disponibilidade](../../availability-zones/az-overview.md) da máquina virtual | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>Exemplo 1: VM de controle em execução no Azure

Como provedor de serviço, você talvez precise controlar o número de máquinas virtuais que executam o seu software ou ter agentes que precisam controlar a exclusividade da máquina virtual. Para obter uma ID exclusiva de uma máquina virtual, use o `vmId` campo do serviço de metadados de instância.

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Resposta**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>Exemplo 2: Posicionamento de contêineres, partições de dados com base em domínio de falha/atualização

Para determinados cenários nos quais o posicionamento de réplicas diferentes é de vital importância. Por exemplo, o [posicionamento de réplica de HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou o posicionamento do contêiner por meio de um [organizador](https://kubernetes.io/docs/user-guide/node-selection/) podem exigir que você conheça os `platformFaultDomain` e `platformUpdateDomain` nas quais a máquina virtual está sendo executada.
Também é possível usar as [Zonas de Disponibilidade](../../availability-zones/az-overview.md) das instâncias para tomar essas decisões.
Você pode consultar esses dados diretamente por meio de serviço de metadados.

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Resposta**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Exemplo 3: Obtendo mais informações sobre a máquina virtual durante a ocorrência de suporte

Como provedor de serviços, você poderá receber uma chamada de suporte na qual gostaria de ter mais informações sobre a máquina virtual. Pedir ao cliente para informar os metadados de computação pode fornecer informações básicas para o profissional de suporte saber o tipo de VM no Azure.

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>Exemplo 4: Obter o Ambiente do Azure em que a VM está em execução

O Azure tem várias nuvens soberanas, como o [Azure Governamental](https://azure.microsoft.com/overview/clouds/government/). Às vezes, é necessário que o Ambiente do Azure tome algumas decisões de runtime. O exemplo a seguir mostra como você pode gerar tal comportamento.

**Solicitação**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Resposta**

```bash
AzurePublicCloud
```

A nuvem e os valores do Ambiente do Azure estão listados abaixo.

 Nuvem   | Azure Environment
---------|-----------------
[Todas as regiões globais do Azure disponíveis](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Governamental](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Metadados de rede 

Os metadados de rede fazem parte da API de instância. As seguintes categorias de rede estão disponíveis por meio do ponto de extremidade da instância/rede.

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
IPv4/privateIpAddress | Endereço IPv4 local da máquina virtual | 2017-04-02
IPv4/privateIpAddress | Endereço IPv4 local da máquina virtual | 2017-04-02
subnet/address | Endereço sub-rede da máquina virtual | 2017-04-02
subnet/prefix | Prefixo de sub-rede, exemplo 24 | 2017-04-02
ipv6/ipAddress | Endereço IPv6 local da máquina virtual | 2017-04-02
macAddress | Endereço mac da máquina virtual | 2017-04-02

> [!NOTE]
> Todas as respostas de API são cadeias de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

#### <a name="sample-1-retrieving-network-information"></a>Exemplo 1: Recuperação das informações de rede

***Solicitação***

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

***Resposta***

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieving-public-ip-address"></a>Exemplo 2: Recuperação do endereço IP público

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"

```

## <a name="storage-metadata"></a>Metadados de armazenamento

Os metadados de armazenamento fazem parte da API de instância no ponto de extremidade de instância/computação/storageProfile.
Fornecem detalhes sobre os discos de armazenamento associados à VM. 

O perfil de armazenamento de uma VM é dividido em três categorias: referência de imagem, disco do sistema operacional e discos de dados.

O objeto de referência de imagem contém as seguintes informações sobre a imagem do sistema operacional:

Dados    | Descrição
--------|-----------------
id      | ID de Recurso
oferta   | Oferta da imagem da plataforma ou do marketplace
publicador | Editor de imagem
sku     | SKU de imagem
version | Versão da imagem da plataforma ou do marketplace

O objeto de disco do sistema operacional contém as seguintes informações sobre o disco do sistema operacional pela VM:

Dados    | Descrição
--------|-----------------
cache | Requisitos de cache
createOption | Informações sobre como a VM foi criada
diffDiskSettings | Configurações do disco efêmero
diskSizeGB | Tamanho do disco em GB
image   | Disco rígido virtual de imagem do usuário de origem
lun     | Número de unidade lógica do disco
managedDisk | Parâmetros do disco gerenciado
name    | Nome do disco
vhd     | Disco rígido virtual
writeAcceleratorEnabled | Se writeAccelerator está habilitado ou não no disco

A matriz de discos de dados contém uma lista dos discos de dados anexada à VM. Cada objeto de disco de dados contém as seguintes informações:

Dados    | Descrição
--------|-----------------
cache | Requisitos de cache
createOption | Informações sobre como a VM foi criada
diffDiskSettings | Configurações do disco efêmero
diskSizeGB | Tamanho do disco em GB
encryptionSettings | Configurações de criptografia do disco
image   | Disco rígido virtual de imagem do usuário de origem
managedDisk | Parâmetros do disco gerenciado
name    | Nome do disco
osType  | Tipo de sistema operacional incluído no disco
vhd     | Disco rígido virtual
writeAcceleratorEnabled | Se writeAccelerator está habilitado ou não no disco

Veja a seguir um exemplo de como consultar as informações de armazenamento da VM.

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>Marcas de VM

As marcas de VM são incluídas na API de instância no ponto de extremidade de instância/computação/marcações.
As marcas podem ter sido aplicadas à VM do Azure para organizá-las logicamente em uma taxonomia. As marcas atribuídas a uma VM podem ser recuperadas usando a solicitação abaixo.

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Resposta**

```text
Department:IT;Environment:Test;Role:WebRole
```

O campo `tags` é uma cadeia de caracteres com as marcas delimitadas por ponto e vírgula. Isso pode ser um problema se o ponto e vírgula for usado nas próprias marcas. Se um analisador for gravado para extrair as marcas programaticamente, você deve contar com o campo `tagsList`, que é uma matriz JSON sem delimitadores e, consequentemente, mais fácil de analisar.

**Solicitação**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
```

**Resposta**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>Dados Atestados

Parte do cenário atendido pelo Serviço de Metadados de Instância é fornecer garantias de que os dados fornecidos estejam vindo do Azure. Assinamos parte dessas informações para que as imagens do Marketplace saibam que são suas as imagens em execução no Azure.

### <a name="sample-1-getting-attested-data"></a>Exemplo 1: Recebimento de dados atestados

> [!NOTE]
> Todas as respostas de API são cadeias de caracteres JSON. As respostas de exemplo a seguir têm estilos de formatação para facilitar a leitura.

***Solicitação***

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

API-Version é um campo obrigatório. Veja a [seção de uso](#usage) para obter as versões de API com suporte.
Nonce é uma cadeia de caracteres de 10 dígitos opcional. Se não for fornecida, em vez disso, o IMDS retornará o carimbo de data/hora UTC atual. Devido ao mecanismo de cache do IMDS, um valor de nonce armazenado em cache anteriormente pode ser retornado.

 ***Resposta***

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

O blob de assinatura é uma versão assinada do [pkcs7](https://aka.ms/pkcs7) do documento. Ele contém o certificado usado na assinatura, juntamente com os detalhes da VM, como vmId, sku, nonce, subscriptionId e timeStamp para a criação e a expiração do documento e informações do plano sobre a imagem. As informações do plano são preenchidas apenas para as imagens do Azure Marketplace. O certificado pode ser extraído da resposta e usado para validar que a resposta é válida e proveniente do Azure.

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Exemplo 2: Validar que a VM está em execução no Azure

Os fornecedores do Marketplace desejam atestar que seu software está licenciado para ser executado somente no Azure. É necessário haver uma maneira de detectar se alguém copia o VHD localmente. Ao chamar o Serviço de Metadados de Instância, os fornecedores do Marketplace podem obter dados assinados que garantam resposta apenas do Azure.

> [!NOTE]
> Requer que o jq seja instalado.

***Solicitação***

```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Resposta**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

Dados | Descrição
-----|------------
nonce | Cadeia de caracteres opcional fornecida pelo usuário com a solicitação. Se nenhuma nonce tiver sido fornecida na solicitação, o carimbo de data/hora UTC atual será retornado
plan | O [Plano](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) de uma VM em uma imagem do Azure Marketplace contém nome, produto e editor
timestamp/createdOn | O carimbo de data/hora UTC em que o primeiro documento assinado foi criado
timestamp/expiresOn | O carimbo de data/hora UTC em que o documento assinado expira
vmId |  [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a máquina virtual
subscriptionId | Assinatura do Azure para a Máquina Virtual, apresentada em `2019-04-30`
sku | SKU específico para a imagem da máquina virtual, apresentado em `2019-11-01`

#### <a name="sample-3-verifying-the-signature"></a>Exemplo 3: Verificar a assinatura

Depois de obter a assinatura acima, você pode verificar se a assinatura é da Microsoft. Também é possível verificar o certificado intermediário e a cadeia de certificados. Por fim, você pode verificar se a ID da assinatura está correta.

> [!NOTE]
> Os certificados para a nuvem Pública e a nuvem soberana serão diferentes.

 Nuvem | Certificado
---------|-----------------
[Todas as regiões globais do Azure disponíveis](https://azure.microsoft.com/regions/)     | *.metadata.azure.com
[Azure Governamental](https://azure.microsoft.com/overview/clouds/government/)              | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | *.metadata.azure.cn
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | *.metadata.microsoftazure.de

Há um problema conhecido no certificado usado para assinatura. Os certificados podem não ter uma correspondência exata de `metadata.azure.com` para a nuvem pública. Portanto, a validação de certificação deve permitir um nome comum de qualquer subdomínio `.metadata.azure.com`.

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

Nos casos em que o certificado intermediário não pode ser baixado devido a restrições de rede durante a validação, o certificado intermediário pode ser fixado. No entanto, o Azure renovará os certificados de acordo com a prática de PKI padrão. Os certificados fixados precisariam ser atualizados quando ocorrer a renovação. Sempre que uma alteração na atualização do certificado intermediário for planejada, o blog do Azure será atualizado e os clientes do Azure serão avisados. Os certificados intermediários podem ser encontrados [aqui](https://www.microsoft.com/pki/mscorp/cps/default.htm). Os certificados intermediários de cada uma das regiões podem ser diferentes.

> [!NOTE]
>O certificado intermediário do Azure China 21Vianet será na DigiCert Global Root CA, em vez de Baltimore.
Além disso, se você fixou os certificados intermediários para Azure China como parte da alteração da autoridade da cadeia raiz, os certificados intermediários precisarão ser atualizados.

## <a name="managed-identity-via-metadata-service"></a>Identidade gerenciada por meio do serviço de metadados
O usuário pode habilitar a identidade gerenciada em uma VM e, em seguida, utilizar o Serviço de Metadados de Instância para passar o token para acessar os serviços do Azure. Os aplicativos em execução em uma VM agora podem solicitar um token do ponto de extremidade do Serviço de Metadados de Instância do Azure e, em seguida, usar o token para autenticar nos serviços em nuvem, incluindo o Key Vault.
Para obter as etapas detalhadas para habilitar esse recurso, confira [Adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events-via-metadata-service"></a>Eventos Agendados por meio do Serviço de Metadados
Você pode obter o status dos eventos agendados por meio do serviço de metadados e, em seguida, o usuário pode especificar um conjunto de ações a serem executadas sobre esses eventos.  Confira [Eventos Agendados](scheduled-events.md) para obter detalhes. 

## <a name="regional-availability"></a>Disponibilidade regional

O serviço está **disponível geralmente** em todas as regiões do Azure. Isso inclui: 
1. [Todas as regiões globais do Azure disponíveis](https://azure.microsoft.com/regions/)
2. [Azure Governamental](https://azure.microsoft.com/overview/clouds/government/)  
3. [Azure China 21Vianet](https://www.azure.cn/) 
4. [Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/) 

## <a name="sample-code-in-different-languages"></a>Exemplo de código em diferentes linguagens

Amostras de como chamar o serviço de metadados usando diferentes linguagens dentro da VM:

Linguagem | Exemplo
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Puppet | https://github.com/keirans/azuremetadata

## <a name="error-and-debugging"></a>Erro e depuração

Se houver um elemento de dados não encontrado ou solicitações malformadas, o serviço de metadados da instância retornará o erro de HTTP padrão. Por exemplo:

Código de status HTTP | Motivo
----------------|-------
200 OK |
400 Solicitação Inválida | Cabeçalho `Metadata: true` ausente ou formato ausente ao consultar um nó folha
404 Não Encontrado | O elemento solicitado não existe
405 método não permitido | Somente as solicitações `GET` são permitidas
410 não existe mais | Tente novamente após algum tempo por no máximo 70 segundos
429 Número excessivo de solicitações | A API atualmente suporta um máximo de 5 consultas por segundo
500 Erro do serviço     | Aguarde um pouco e tente novamente

### <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

1. Estou recebendo o erro `400 Bad Request, Required metadata header not specified`. O que isso significa?
   * O serviço de metadados de instância exige que o cabeçalho `Metadata: true` seja passado na solicitação. Passar o cabeçalho na chamada de REST permite acessar o serviço de metadados de instância.
2. Por que não estou obtendo informações de computação para minha máquina virtual?
   * Atualmente o serviço de metadados de instância suporta apenas instâncias criadas com o Gerenciador de recursos do Azure. No futuro, o suporte para VMs de serviço de nuvem pode ser adicionado.
3. Criei minha máquina virtual com o Azure Resource Manager há algum tempo. Por que não consigo ver as informações de metadados de computação?
   * Para todas as máquinas virtuais criadas depois de setembro de 2016, adicione uma [marca](../../azure-resource-manager/management/tag-resources.md) para começar a ver os metadados de computação. Para máquinas virtuais mais antigas (criadas antes de setembro de 2016), adicione ou remova extensões ou dados de discos à máquina virtual para atualizar os metadados.
4. Não estou vendo todos os dados preenchidos para a nova versão
   * Para todas as máquinas virtuais criadas depois de setembro de 2016, adicione uma [marca](../../azure-resource-manager/management/tag-resources.md) para começar a ver os metadados de computação. Para máquinas virtuais mais antigas (criadas antes de setembro de 2016), adicione ou remova extensões ou dados de discos à máquina virtual para atualizar os metadados.
5. Por que estou recebendo o erro `500 Internal Server Error`?
   * Repita a solicitação com base no sistema de retirada exponencial. Se o problema persistir, contate o suporte do Azure.
6. Onde posso publicar comentários/perguntas adicionais?
   * Envie seus comentários em https://feedback.azure.com.
7. Isso funcionaria para Instância do Conjunto de Dimensionamento da Máquina Virtual?
   * Sim, o serviço de metadados está disponível para instâncias de conjunto de escala.
8. Como posso obter suporte para o serviço?
   * Para obter suporte para o serviço, crie um problema de suporte no portal do Azure para a VM na qual você não consegue obter resposta de metadados após várias tentativas.
9. Eu recebo uma solicitação com tempo limite para minha chamada ao serviço?
   * Chamadas de metadados devem ser feitas do endereço IP principal atribuído à placa de rede primária da VM, além disso, caso você tenha alterado lá suas rotas devem ser uma rota para o endereço de 169.254.0.0/16 fora de sua placa de rede.
10. Atualizei minhas marcas no conjunto de dimensionamento de máquinas virtuais, mas, ao contrário das VMs, elas não são exibidas nas instâncias?
    * Atualmente, para marcas de ScaleSets são exibidas apenas na VM em um reiníco/ao refazer imagem/ou uma alteração de disco na instância.

## <a name="support-and-feedback"></a>Suporte e Comentários

Envie seus comentários sobre o https://feedback.azure.com.
Para obter suporte para o serviço, crie um problema de suporte no portal do Azure para a VM na qual você não consegue obter resposta de metadados após várias tentativas.

![Serviço de Metadados de Instância](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:
1.  [Adquirir um token de acesso da VM](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
2.  [Eventos Agendados](scheduled-events.md) 