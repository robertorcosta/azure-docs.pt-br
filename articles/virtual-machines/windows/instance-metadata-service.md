---
title: Serviço de metadados de instância do Azure
description: Interface RESTful para obter informações sobre VMs de computação, rede e futuros eventos de manutenção.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: 2e0788b6a7eb6f1d43185d8b484adddd76374ea3
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086701"
---
# <a name="azure-instance-metadata-service"></a>Serviço de Metadados de Instância do Azure

O Serviço de Metadados de Instância (IMDS) do Azure fornece informações sobre instâncias da máquina virtual em execução no momento e pode ser usado para gerenciar e configurar suas máquinas virtuais.
Essas informações incluem a SKU, o armazenamento, as configurações de rede e os próximos eventos de manutenção. Para obter uma lista completa dos dados disponíveis, confira [APIs de metadados](#metadata-apis).
O serviço de metadados de instância está disponível para a execução de instâncias de máquina virtual e conjunto de dimensionamento de máquinas virtuais. Todas as APIs dão suporte a VMs criadas/gerenciadas usando [Azure Resource Manager](/rest/api/resources/). Somente os pontos de extremidade atestados e de rede dão suporte a VMs clássicas (não ARM) e o atestado faz isso apenas para uma extensão limitada.

O IMDS do Azure é um ponto de extremidade REST que está disponível em um endereço IP não roteável conhecido ( `169.254.169.254` ), ele pode ser acessado somente de dentro da VM. A comunicação entre a VM e a IMDS nunca deixa o host.
É recomendável que seus clientes HTTP ignorem proxies da Web na VM ao consultar IMDS e tratá-los `169.254.169.254` da mesma forma que [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Segurança

O ponto de extremidade de metadados de instância está acessível somente dentro da instância de máquina virtual em execução em um endereço IP não roteável. Além disso, qualquer solicitação com `X-Forwarded-For`Cabeçalho é rejeitada pelo serviço.
As solicitações também devem conter um `Metadata: true` cabeçalho para garantir que a solicitação real tenha sido desejada diretamente e não faça parte de um redirecionamento não intencional.

> [!IMPORTANT]
> O Serviço de Metadados de Instância não é um canal para dados confidenciais. O ponto de extremidade está aberto para todos os processos na VM. As informações expostas por esse serviço devem ser consideradas informações compartilhadas para todos os aplicativos em execução dentro da VM.

## <a name="usage"></a>Uso

### <a name="accessing-azure-instance-metadata-service"></a>Acesso ao Serviço de Metadados de Instância do Azure

Para acessar o Serviço de Metadados de Instância, crie uma VM no [Azure Resource Manager](/rest/api/resources/) ou [portal do Azure](https://portal.azure.com) e siga as amostras abaixo.
Mais exemplos de como consultar o IMDS podem ser encontrados em [Amostras de metadados de instância do Azure](https://github.com/microsoft/azureimds).

Veja abaixo o código de exemplo para recuperar todos os metadados de uma instância. Para acessar a fonte de dados específica, confira a seção [API de metadados](#metadata-apis). 

**Solicitação**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-06-01
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
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
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
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
        "subscriptionId": "8d10da13-8125-4ba9-a717-bf7490507b3d",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    }
}
```

### <a name="data-output"></a>Saída de dados


Por padrão, o serviço de metadados de instância retorna dados em formato JSON (`Content-Type: application/json`). No entanto, algumas APIs podem retornar dados em formatos diferentes, se solicitado.
A tabela a seguir é uma referência de outros formatos de dados que pode oferecer suporte a APIs.

API | Formato de dados padrão | Outros formatos
--------|---------------------|--------------
/attested | json | none
/Identity | json | none
/instance | json | text
/scheduledevents | json | none

Para acessar um formato de resposta não padrão, especifique o formato solicitado como um parâmetro de cadeia de caracteres de consulta na solicitação. Por exemplo:

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Para nós folha no/Metadata/instance, o `format=json` não funciona. Para essas consultas `format=text` , é necessário especificar explicitamente, pois o formato padrão é JSON.

### <a name="versioning"></a>Controle de versão

O Serviço de Metadados de Instância tem controle de versão e a especificação da versão de API na solicitação HTTP é obrigatória.

As versões de API com suporte são: 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 01-02-2018
- 2018-04-02
- 01-10-2018
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 01-08-2019
- 2019-08-15
- 2019-11-01
- 2020-06-01

Observe quando a nova versão for liberada. Vai demorar um pouco para ser distribuída para todas as regiões.

Conforme versões mais recentes são adicionadas, as versões mais antigas ainda podem ser acessadas para fins de compatibilidade se os scripts tiverem dependências de formatos de dados específicos.

Quando nenhuma versão é especificada, um erro é retornado com uma lista das versões mais recentes com suporte.

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. O exemplo a seguir indica a condição de erro quando a versão não é especificada, a resposta é impressa para facilitar a leitura.

**Solicitação**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
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

O serviço de metadados contém várias APIs que representam diferentes fontes de dados.

API | Descrição | Versão introduzida
----|-------------|-----------------------
/attested | Confira [Dados Atestados](#attested-data) | 01-10-2018
/Identity | Confira [Adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 01-02-2018
/instance | Confira [API de instância](#instance-api) | 2017-04-02
/scheduledevents | Consulte [Eventos agendados](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>API de instância

A API de instância expõe os metadados importantes para as instâncias de VM, incluindo a VM, a rede e o armazenamento. As categorias a seguir podem ser acessadas por meio de instância/computação:

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
azEnvironment | Ambiente do Azure em que a VM está em execução | 01-10-2018
customData | Este recurso está desabilitado no momento. Atualizaremos esta documentação quando ela ficar disponível | 2019-02-01
isHostCompatibilityLayerVm | Identifica se a VM é executada na camada de compatibilidade do host | 2020-06-01
local | Região do Azure na qual a máquina virtual está sendo executada | 2017-04-02
name | Nome da VM | 2017-04-02
oferta | Oferece informações para a imagem da VM e está presente apenas para imagens implantadas na galeria de imagens do Azure | 2017-04-02
osType | Linux ou Windows | 2017-04-02
placementGroupId | [Grupo de Posicionamento](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do conjunto de dimensionamento da sua Máquina Virtual | 2017-08-01
plan | O [Plano](/rest/api/compute/virtualmachines/createorupdate#plan) que contém o nome, produto e editor de uma VM, se for uma imagem do Azure Marketplace | 2018-04-02
platformUpdateDomain |  [Domínio de atualização](manage-availability.md) no qual a máquina virtual está sendo executada | 2017-04-02
platformFaultDomain | [Domínio de falha](manage-availability.md) no qual a máquina virtual está sendo executada | 2017-04-02
provider | Provedor da VM | 01-10-2018
publicKeys | [Coleção de Chaves Públicas](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) atribuídas à VM e aos caminhos | 2018-04-02
publicador | Publicador da imagem da máquina virtual | 2017-04-02
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/management/overview.md) para a sua Máquina Virtual | 2017-08-01
resourceId | A ID [totalmente qualificada](/rest/api/resources/resources/getbyid) do recurso | 2019-03-11
sku | SKU específica para a imagem da máquina virtual | 2017-04-02
securityProfile. secureBootEnabled | Identifica se a inicialização segura UEFI está habilitada na VM | 2020-06-01
securityProfile.virtualTpmEnabled | Identifica se o Trusted Platform Module virtual (TPM) está habilitado na VM | 2020-06-01
storageProfile | Confira [Perfil de armazenamento](#storage-metadata) | 2019-06-01
subscriptionId | Assinatura do Azure para a Máquina Virtual | 2017-08-01
marcas | [Marcas](../../azure-resource-manager/management/tag-resources.md) para a sua Máquina Virtual  | 2017-08-01
tagsList | Marcas formatadas como uma matriz JSON para facilitar a análise programática  | 2019-06-04
version | Versão da imagem da máquina virtual | 2017-04-02
vmId | [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a máquina virtual | 2017-04-02
vmScaleSetName | [Nome do conjunto de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/overview.md) do seu conjunto de dimensionamento de máquinas virtuais | 2017-12-01
vmSize | [Tamanho da VM](../sizes.md) | 2017-04-02
zona | [Zona de Disponibilidade](../../availability-zones/az-overview.md) da máquina virtual | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>Exemplo 1: VM de controle em execução no Azure

Como provedor de serviço, você talvez precise controlar o número de máquinas virtuais que executam o seu software ou ter agentes que precisam controlar a exclusividade da máquina virtual. Para obter uma ID exclusiva de uma máquina virtual, use o `vmId` campo do serviço de metadados de instância.

**Solicitação**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
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

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Resposta**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Exemplo 3: Obtendo mais informações sobre a máquina virtual durante a ocorrência de suporte

Como provedor de serviços, você poderá receber uma chamada de suporte na qual gostaria de ter mais informações sobre a máquina virtual. Pedir ao cliente para informar os metadados de computação pode fornecer informações básicas para o profissional de suporte saber o tipo de VM no Azure.

**Solicitação**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01
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

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Resposta**

```text
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

**Solicitação**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

**Resposta**

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

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
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
encryptionSettings | Configurações de criptografia do disco
image   | Disco rígido virtual de imagem do usuário de origem
managedDisk | Parâmetros do disco gerenciado
name    | Nome do disco
osType  | Tipo de sistema operacional incluído no disco
vhd     | Disco rígido virtual
writeAcceleratorEnabled | Se writeAccelerator está habilitado ou não no disco

A matriz de discos de dados contém uma lista dos discos de dados anexada à VM. Cada objeto de disco de dados contém as seguintes informações:

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

O exemplo a seguir mostra como consultar as informações de armazenamento da VM.

**Solicitação**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
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

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Resposta**

```text
Department:IT;Environment:Test;Role:WebRole
```

O campo `tags` é uma cadeia de caracteres com as marcas delimitadas por ponto e vírgula. Essa saída pode ser um problema se ponto e vírgulas forem usados nas próprias marcas. Se um analisador for escrito para extrair as marcas programaticamente, você deverá contar com o `tagsList` campo. O `tagsList` campo é uma matriz JSON sem delimitadores e, consequentemente, mais fácil de analisar.

**Solicitação**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
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

**Solicitação**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, um valor de nonce armazenado em cache anteriormente pode ser retornado.

API-Version é um campo obrigatório. Veja a [seção de uso](#usage) para obter as versões de API com suporte.
Nonce é uma cadeia de caracteres de 10 dígitos opcional. Se não for fornecida, em vez disso, o IMDS retornará o carimbo de data/hora UTC atual.

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

O blob de assinatura é uma versão assinada do [pkcs7](https://aka.ms/pkcs7) do documento. Ele contém o certificado usado para assinar junto com determinados detalhes específicos da VM. Para VMs ARM, isso inclui vmId, SKU, nonce, SubscriptionId, carimbo de data/hora para a criação e expiração do documento e as informações do plano sobre a imagem. As informações do plano são preenchidas apenas para as imagens do Azure Marketplace. Para VMs clássicas (não ARM), somente o vmId tem a garantia de ser populada. O certificado pode ser extraído da resposta e usado para validar que a resposta é válida e proveniente do Azure.
O documento contém os seguintes campos:

Dados | Descrição
-----|------------
nonce | Uma cadeia de caracteres que pode ser fornecida opcionalmente com a solicitação. Se nenhum nonce for fornecido, o carimbo de data/hora UTC atual será usado
plan | O [plano de imagem do Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Contém a ID do plano (nome), a imagem do produto ou a oferta (produto) e a ID do editor (Publicador).
timestamp/createdOn | O carimbo de data/hora UTC para quando o documento assinado foi criado
timestamp/expiresOn | O carimbo de data/hora UTC para quando o documento assinado expira
vmId |  [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a máquina virtual
subscriptionId | Assinatura do Azure para a Máquina Virtual, apresentada em `2019-04-30`
sku | SKU específico para a imagem da máquina virtual, apresentado em `2019-11-01`

> [!NOTE]
> Para VMs clássicas (não ARM), somente o vmId tem a garantia de ser populada.

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Exemplo 2: Validar que a VM está em execução no Azure

Os fornecedores do Marketplace desejam atestar que seu software está licenciado para ser executado somente no Azure. É necessário haver uma maneira de detectar se alguém copia o VHD localmente. Ao chamar o Serviço de Metadados de Instância, os fornecedores do Marketplace podem obter dados assinados que garantam resposta apenas do Azure.

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2019-04-30
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Verifique se a assinatura é de Microsoft Azure e verifique se há erros na cadeia de certificados.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $conten
$json = $content | ConvertFrom-Json
# Do additional validation here
```

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, um valor de nonce armazenado em cache anteriormente pode ser retornado.

O nonce no documento assinado pode ser comparado se você forneceu um parâmetro nonce na solicitação inicial.

> [!NOTE]
> O certificado para a nuvem pública e cada nuvem soberanas será diferente.

Nuvem | Certificado
------|------------
[Todas as regiões globais do Azure disponíveis](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Governamental](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> Há um problema conhecido no certificado usado para assinatura. Os certificados podem não ter uma correspondência exata de `metadata.azure.com` para a nuvem pública. Portanto, a validação de certificação deve permitir um nome comum de qualquer subdomínio `.metadata.azure.com`.

Nos casos em que o certificado intermediário não pode ser baixado devido a restrições de rede durante a validação, o certificado intermediário pode ser fixado. No entanto, o Azure renovará os certificados de acordo com a prática de PKI padrão. Os certificados fixados precisariam ser atualizados quando ocorrer a renovação. Sempre que uma alteração na atualização do certificado intermediário for planejada, o blog do Azure será atualizado e os clientes do Azure serão avisados. Os certificados intermediários podem ser encontrados [aqui](https://www.microsoft.com/pki/mscorp/cps/default.htm). Os certificados intermediários de cada uma das regiões podem ser diferentes.

> [!NOTE]
> O certificado intermediário do Azure China 21Vianet será na DigiCert Global Root CA, em vez de Baltimore.
Além disso, se você fixou os certificados intermediários para Azure China como parte da alteração da autoridade da cadeia raiz, os certificados intermediários precisarão ser atualizados.

## <a name="failover-clustering-in-windows-server"></a>Clustering de failover no Windows Server

Para determinados cenários ao consultar o serviço de metadados de instância com clustering de failover, é necessário adicionar uma rota à tabela de roteamento.

1. Abra uma prompt de comando com privilégios de administrador.

1. Execute o seguinte comando e anote o endereço da Interface de rede de destino (`0.0.0.0`) na tabela de rotas do IPv4.

```bat
route print
```

> [!NOTE]
> A seguinte saída de exemplo de uma VM do Windows Server com o cluster de failover habilitado contém apenas a tabela de rotas IPv4 para manter a simplicidade.

```text
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Execute o seguinte comando e anote o endereço da Interface de rede de destino (`0.0.0.0`) que é o (`10.0.1.10`) neste exemplo.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity-via-metadata-service"></a>Identidade gerenciada por meio do serviço de metadados

Uma identidade gerenciada atribuída ao sistema pode ser habilitada na VM ou uma ou mais identidades gerenciadas atribuídas pelo usuário podem ser atribuídas à VM.
Tokens para identidades gerenciadas podem ser solicitados do serviço de metadados de instância. Esses tokens podem ser usados para autenticar com outros serviços do Azure, como Azure Key Vault.

Para obter as etapas detalhadas para habilitar esse recurso, confira [Adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events-via-metadata-service"></a>Eventos Agendados por meio do Serviço de Metadados
Você pode obter o status dos eventos agendados por meio do serviço de metadados e, em seguida, o usuário pode especificar um conjunto de ações a serem executadas sobre esses eventos.  Confira [Eventos Agendados](scheduled-events.md) para obter detalhes. 

## <a name="regional-availability"></a>Disponibilidade regional

O serviço está **geralmente disponível** em todas as nuvens do Azure.

## <a name="sample-code-in-different-languages"></a>Exemplo de código em diferentes linguagens

Amostras de como chamar o serviço de metadados usando diferentes linguagens dentro da VM:

Linguagem      | Exemplo
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="error-and-debugging"></a>Erro e depuração

Se houver um elemento de dados não encontrado ou solicitações malformadas, o serviço de metadados da instância retornará o erro de HTTP padrão. Por exemplo:

Código de status HTTP | Motivo
-----------------|-------
200 OK |
400 Solicitação Inválida | `Metadata: true`Cabeçalho ausente ou parâmetro ausente `format=json` ao consultar um nó folha
404 Não Encontrado  | O elemento solicitado não existe
405 método não permitido | Somente as solicitações `GET` são permitidas
410 não existe mais | Tente novamente após algum tempo por no máximo 70 segundos
429 Número excessivo de solicitações | A API atualmente suporta um máximo de 5 consultas por segundo
500 Erro do serviço     | Aguarde um pouco e tente novamente

### <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

1. Estou recebendo o erro `400 Bad Request, Required metadata header not specified`. O que isso significa?
   * O serviço de metadados de instância exige que o cabeçalho `Metadata: true` seja passado na solicitação. Passar o cabeçalho na chamada de REST permite acessar o serviço de metadados de instância.
1. Por que não estou obtendo informações de computação para minha máquina virtual?
   * Atualmente o serviço de metadados de instância suporta apenas instâncias criadas com o Gerenciador de recursos do Azure. No futuro, o suporte para VMs de serviço de nuvem pode ser adicionado.
1. Criei minha máquina virtual com o Azure Resource Manager há algum tempo. Por que não consigo ver as informações de metadados de computação?
   * Para todas as máquinas virtuais criadas depois de setembro de 2016, adicione uma [marca](../../azure-resource-manager/management/tag-resources.md) para começar a ver os metadados de computação. Para VMs mais antigas (criadas antes de setembro de 2016), adicione/remova extensões ou discos de dados para as instâncias de VM para atualizar os metadados.
1. Não estou vendo todos os dados preenchidos para a nova versão
   * Para todas as máquinas virtuais criadas depois de setembro de 2016, adicione uma [marca](../../azure-resource-manager/management/tag-resources.md) para começar a ver os metadados de computação. Para VMs mais antigas (criadas antes de setembro de 2016), adicione/remova extensões ou discos de dados para as instâncias de VM para atualizar os metadados.
1. Por que estou recebendo o erro `500 Internal Server Error` ou `410 Resource Gone` ?
   * Repita a solicitação com base no sistema de retirada exponencial ou em outros métodos descritos em [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults). Se o problema persistir, crie um problema de suporte no portal do Azure para a VM.
1. Isso funcionaria para instâncias do conjunto de dimensionamento de máquinas virtuais?
   * Sim, o serviço de metadados está disponível para instâncias do conjunto de dimensionamento.
1. Atualizei minhas marcas em conjuntos de dimensionamento de máquinas virtuais, mas elas não aparecem nas instâncias ao contrário de VMs de instância única?
   * Atualmente, marcas para conjuntos de dimensionamento mostram apenas para a VM em uma reinicialização, reimagem ou alteração de disco para a instância.
1. Eu recebo uma solicitação com tempo limite para minha chamada ao serviço?
   * As chamadas de metadados devem ser feitas a partir do endereço IP primário atribuído à placa de rede primária da VM. Além disso, no caso em que você alterou suas rotas, deve haver uma rota para o endereço 169.254.169.254/32 na tabela de roteamento local da VM.
   * <details>
        <summary>Verificando sua tabela de roteamento</summary>

        1. Despeje sua tabela de roteamento local e procure a entrada IMDS (por exemplo,):
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. Verifique se existe uma rota para `169.254.169.254` e anote a interface de rede correspondente (por exemplo, `172.16.69.7` ).
        1. Despeje a configuração da interface e localize a interface que corresponde à referida na tabela de roteamento, observando o endereço MAC (físico).
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. Confirme se a interface corresponde à NIC primária e ao IP primário da VM. Você pode encontrar o NIC/IP primário examinando a configuração de rede no portal do Azure ou procurando [o CLI do Azure](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show). Observe os IPs públicos e privados (e o endereço MAC se usar a CLI). Exemplo de CLI do PowerShell:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. Se eles não corresponderem, atualize a tabela de roteamento de modo que a NIC/IP primária seja destinada.
    </details>

## <a name="support-and-feedback"></a>Suporte e Comentários

Envie seus comentários e comentários sobre o https://feedback.azure.com .

Para obter suporte para o serviço, crie um problema de suporte no portal do Azure para a VM na qual você não consegue obter resposta de metadados após várias tentativas.
Use o tipo de problema de `Management` e selecione `Instance Metadata Service` como a categoria.

![Serviço de Metadados de Instância](./media/instance-metadata-service/InstanceMetadata-support.png "Captura de tela: abrindo um caso de suporte ao ter problemas com o serviço de metadados de instância")

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:
1.  [Adquirir um token de acesso da VM](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
2.  [Eventos Agendados](scheduled-events.md)
