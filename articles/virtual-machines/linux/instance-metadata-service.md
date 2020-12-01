---
title: Serviço de metadados de instância do Azure para Linux
description: Saiba mais sobre o serviço de metadados de instância do Azure e como ele fornece informações sobre as instâncias de máquina virtual em execução no momento no Linux.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: c9a4f5697fb667cde2cf3b4ddc3d637ff58149c9
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436582"
---
# <a name="azure-instance-metadata-service-imds"></a>Serviço de metadados de instância do Azure (IMDS)

O serviço de metadados de instância do Azure (IMDS) fornece informações sobre as instâncias de máquina virtual em execução no momento. Você pode usá-lo para gerenciar e configurar suas máquinas virtuais.
Essas informações incluem a SKU, o armazenamento, as configurações de rede e os próximos eventos de manutenção. Para obter uma lista completa dos dados disponíveis, consulte [APIs de metadados](#metadata-apis).

IMDS está disponível para a execução de instâncias de máquinas virtuais (VMs) e instâncias do conjunto de dimensionamento de máquinas virtuais. Todas as APIs dão suporte a VMs criadas e gerenciadas usando [Azure Resource Manager](/rest/api/resources/). Somente os pontos de extremidade atestados e de rede dão suporte a VMs criadas usando o modelo de implantação clássico. O ponto de extremidade atestado faz isso apenas para uma extensão limitada.

IMDS é um ponto de extremidade REST que está disponível em um endereço IP conhecido e não roteável ( `169.254.169.254` ). Você o acessa somente de dentro da VM. A comunicação entre a VM e a IMDS nunca deixa o host.
Faça com que seus clientes HTTP ignorem proxies da Web na VM ao consultar IMDS e tratem `169.254.169.254` o mesmo que [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Segurança

O ponto de extremidade IMDS é acessível somente de dentro da instância de máquina virtual em execução em um endereço IP não roteável. Além disso, qualquer solicitação com um `X-Forwarded-For` cabeçalho é rejeitada pelo serviço.
As solicitações também devem conter um `Metadata: true` cabeçalho, para garantir que a solicitação real tenha sido diretamente intencional e não faça parte do redirecionamento não intencional.

> [!IMPORTANT]
> IMDS não é um canal para dados confidenciais. O ponto de extremidade está aberto para todos os processos na VM. Considere as informações expostas por meio desse serviço como informações compartilhadas para todos os aplicativos em execução dentro da VM.

## <a name="usage"></a>Uso

### <a name="access-azure-instance-metadata-service"></a>Acessar o serviço de metadados de instância do Azure

Para acessar o IMDS, crie uma VM de [Azure Resource Manager](/rest/api/resources/) ou o [portal do Azure](https://portal.azure.com)e use os exemplos a seguir.
Para obter mais exemplos, consulte [exemplos de metadados de instância do Azure](https://github.com/microsoft/azureimds).

Aqui está o código de exemplo para recuperar todos os metadados de uma instância. Para acessar uma fonte de dados específica, consulte a seção [API de metadados](#metadata-apis) . 

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
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
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
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
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
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
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>Saída de dados

Por padrão, IMDS retorna dados no formato JSON ( `Content-Type: application/json` ). No entanto, algumas APIs podem retornar dados em formatos diferentes, se solicitado.
A tabela a seguir lista outros formatos de dados aos quais as APIs podem dar suporte.

API | Formato de dados padrão | Outros formatos
--------|---------------------|--------------
/attested | json | none
/Identity | json | none
/instance | json | text
/scheduledevents | json | none

Para acessar um formato de resposta não padrão, especifique o formato solicitado como um parâmetro de cadeia de caracteres de consulta na solicitação. Por exemplo:

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Para nós folha no `/metadata/instance` , o `format=json` não funciona. Para essas consultas, `format=text` precisa ser especificado explicitamente porque o formato padrão é JSON.

### <a name="version"></a>Versão

IMDS tem controle de versão e a especificação da versão da API na solicitação HTTP é obrigatória.

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
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> A versão 2020-10-01 pode ainda não estar disponível em todas as regiões.

Conforme as versões mais recentes forem adicionadas, você ainda poderá acessar versões mais antigas para compatibilidade se seus scripts tiverem dependências em formatos de dados específicos.

Quando você não especificar uma versão, obterá um erro com uma lista das versões mais recentes com suporte.

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. O exemplo a seguir indica a condição de erro quando a versão não é especificada. A resposta é bem impressa para facilitar a leitura.

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
```

**Resposta**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>APIs de metadados

IMDS contém várias APIs que representam diferentes fontes de dados.

API | Descrição | Versão introduzida
----|-------------|-----------------------
/attested | Ver [dados atestados](#attested-data) | 01-10-2018
/Identity | Confira [Adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 01-02-2018
/instance | Confira [API de instância](#instance-api) | 2017-04-02
/scheduledevents | Ver [eventos agendados](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>API de instância

A API de instância expõe os metadados importantes para as instâncias de VM, incluindo a VM, a rede e o armazenamento. Você pode acessar as seguintes categorias por meio de `instance/compute` :

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
azEnvironment | O ambiente do Azure no qual a VM está em execução. | 01-10-2018
customData | Este recurso está desabilitado no momento. | 2019-02-01
isHostCompatibilityLayerVm | Identifica se a VM é executada na camada de compatibilidade do host. | 2020-06-01
licenseType | O tipo de licença para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Observe que isso está presente apenas para VMs habilitadas para AHB. | 2020-09-01
local | A região do Azure na qual a VM está em execução. | 2017-04-02
name | O nome da VM. | 2017-04-02
oferta | Oferece informações para a imagem VM. Isso está presente apenas para imagens implantadas da Galeria de imagens do Azure. | 2017-04-02
osProfile.adminUsername | Especifica o nome da conta do administrador. | 2020-07-15
osProfile. ComputerName | Especifica o nome do computador. | 2020-07-15
osProfile. disablePasswordAuthentication | Especifica se a autenticação de senha está desabilitada. Observe que isso está presente apenas para VMs Linux. | 2020-10-01
osType | Linux ou Windows. | 2017-04-02
placementGroupId | [Grupo de posicionamento](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) do conjunto de dimensionamento de máquinas virtuais. | 2017-08-01
plan | [Plano](/rest/api/compute/virtualmachines/createorupdate#plan) que contém o nome, produto e publicador para uma VM se for uma imagem do Azure Marketplace. | 2018-04-02
platformUpdateDomain |  [Atualize o domínio](../manage-availability.md) no qual a VM está em execução. | 2017-04-02
platformFaultDomain | [Domínio de falha](../manage-availability.md) no qual a VM está em execução. | 2017-04-02
provider | O provedor da VM. | 01-10-2018
publicKeys | [Coleção de chaves públicas](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) atribuídas à VM e aos caminhos. | 2018-04-02
publicador | Editor da imagem da VM. | 2017-04-02
resourceGroupName | [Grupo de recursos](../../azure-resource-manager/management/overview.md) para sua VM. | 2017-08-01
resourceId | A ID [totalmente qualificada](/rest/api/resources/resources/getbyid) do recurso. | 2019-03-11
sku | A SKU específica para a imagem da VM. | 2017-04-02
securityProfile. secureBootEnabled | Identifica se a inicialização segura UEFI está habilitada na VM. | 2020-06-01
securityProfile.virtualTpmEnabled | Identifica se o Trusted Platform Module virtual (TPM) está habilitado na VM. | 2020-06-01
storageProfile | Consulte [perfil de armazenamento](#storage-metadata). | 2019-06-01
subscriptionId | Assinatura do Azure para a VM. | 2017-08-01
marcas | [Marcas](../../azure-resource-manager/management/tag-resources.md) para sua VM.  | 2017-08-01
tagsList | Marcas formatadas como uma matriz JSON para facilitar a análise programática.  | 2019-06-04
version | A versão da imagem da VM. | 2017-04-02
vmId | [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a VM. | 2017-04-02
vmScaleSetName | [Nome do conjunto de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/overview.md) do conjunto de dimensionamento de máquinas virtuais. | 2017-12-01
vmSize | Consulte [tamanho da VM](../sizes.md). | 2017-04-02
zona | [Zona de disponibilidade](../../availability-zones/az-overview.md) da VM. | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>Exemplo 1: acompanhar uma VM em execução no Azure

Como um provedor de serviços, talvez seja necessário controlar o número de VMs que executam o software ou ter agentes que precisam controlar a exclusividade da VM. Para poder obter uma ID exclusiva para uma VM, use o `vmId` campo de IMDS.

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Resposta**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>Exemplo 2: posicionamento de réplicas de dados diferentes

Para determinados cenários nos quais o posicionamento de réplicas diferentes é de vital importância. Por exemplo, o [posicionamento da réplica do HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou o posicionamento do contêiner por meio de um [orquestrador](https://kubernetes.io/docs/user-guide/node-selection/) pode exigir que você conheça o `platformFaultDomain` e `platformUpdateDomain` a VM esteja em execução.
Também é possível usar as [Zonas de Disponibilidade](../../availability-zones/az-overview.md) das instâncias para tomar essas decisões.
Você pode consultar esses dados diretamente por meio de IMDS.

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Resposta**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Exemplo 3: obter mais informações sobre a VM durante o caso de suporte

Como um provedor de serviços, você pode receber uma chamada de suporte onde deseja saber mais sobre a VM. Solicitar que o cliente compartilhe os metadados de computação pode ser útil nesse caso.

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

**Resposta**

> [!NOTE]
> A resposta é uma cadeia de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
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
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
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
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
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
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Exemplo 4: obter o ambiente do Azure onde a VM está em execução

O Azure tem várias nuvens soberanass, como o [Azure governamental](https://azure.microsoft.com/overview/clouds/government/). Às vezes, você precisa do ambiente do Azure para tomar algumas decisões de tempo de execução. O exemplo a seguir mostra como você pode gerar tal comportamento.

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Resposta**

```text
AzurePublicCloud
```

A nuvem e os valores do ambiente do Azure são listados aqui.

 Nuvem   | Ambiente do Azure
---------|-----------------
[Todas as regiões globais do Azure geralmente disponíveis](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Governamental](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Metadados de rede 

Os metadados de rede fazem parte da API de instância. As categorias de rede a seguir estão disponíveis por meio do `instance/network` ponto de extremidade.

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
IPv4/privateIpAddress | O endereço IPv4 local da VM. | 2017-04-02
IPv4/privateIpAddress | O endereço IPv4 público da VM. | 2017-04-02
subnet/address | O endereço de sub-rede da VM. | 2017-04-02
subnet/prefix | O prefixo da sub-rede. Exemplo: 24 | 2017-04-02
ipv6/ipAddress | O endereço IPv6 local da VM. | 2017-04-02
macAddress | O endereço MAC da VM. | 2017-04-02

> [!NOTE]
> Todas as respostas de API são cadeias de caracteres JSON. Todas as respostas de exemplo a seguir são estilos de formatação para facilitar a leitura.

#### <a name="sample-1-retrieve-network-information"></a>Exemplo 1: recuperar informações de rede

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Resposta**

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

#### <a name="sample-2-retrieve-public-ip-address"></a>Exemplo 2: recuperar o endereço IP público

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Metadados de armazenamento

Os metadados de armazenamento fazem parte da API da instância, em `instance/compute/storageProfile` ponto de extremidade.
Fornecem detalhes sobre os discos de armazenamento associados à VM. 

O perfil de armazenamento de uma VM é dividido em três categorias: referência de imagem, disco do sistema operacional e discos de dados.

O objeto de referência de imagem contém as seguintes informações sobre a imagem do sistema operacional:

Dados    | Descrição
--------|-----------------
id      | ID de Recurso
oferta   | Oferta da plataforma ou da imagem
publicador | Editor de imagem
sku     | SKU de imagem
Versão | Versão da plataforma ou imagem

O objeto de disco do sistema operacional contém as seguintes informações sobre o disco do sistema operacional usado pela VM:

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
writeAcceleratorEnabled | Se `writeAccelerator` o está habilitado ou não no disco

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
writeAcceleratorEnabled | Se `writeAccelerator` o está habilitado ou não no disco

O exemplo a seguir mostra como consultar as informações de armazenamento da VM.

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
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

As marcas de VM são incluídas na API de instância, sob o `instance/compute/tags` ponto de extremidade.
As marcas podem ter sido aplicadas à sua VM do Azure para organizá-las logicamente em uma taxonomia. Você pode recuperar as marcas atribuídas a uma VM usando a solicitação a seguir.

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Resposta**

```text
Department:IT;Environment:Test;Role:WebRole
```

O campo `tags` é uma cadeia de caracteres com as marcas delimitadas por ponto e vírgula. Essa saída pode ser um problema se ponto e vírgulas forem usados nas próprias marcas. Se um analisador for escrito para extrair as marcas programaticamente, você deverá contar com o `tagsList` campo. O `tagsList` campo é uma matriz JSON sem delimitadores e, consequentemente, é mais fácil de analisar.

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
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

## <a name="attested-data"></a>Dados atestados

O IMDS ajuda a fornecer garantias de que os dados fornecidos são provenientes do Azure. A Microsoft faz parte dessas informações para que você possa confirmar se uma imagem no Azure Marketplace é aquela que você está executando no Azure.

### <a name="sample-1-get-attested-data"></a>Exemplo 1: obter dados atestados

> [!NOTE]
> Todas as respostas de API são cadeias de caracteres JSON. As respostas de exemplo a seguir têm estilos de formatação para facilitar a leitura.

**Solicitação**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

`Api-version` é um campo obrigatório. Veja a [seção de uso](#usage) para obter as versões de API com suporte.
`Nonce` é uma cadeia de caracteres opcional de 10 dígitos. Se não for fornecido, IMDS retornará o carimbo de data/hora universal coordenado atual em seu lugar.

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, um valor anteriormente armazenado em cache `nonce` pode ser retornado.

**Resposta**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

O blob de assinatura é uma versão do documento assinada por [PKCS7](https://aka.ms/pkcs7). Ele contém o certificado usado para assinatura, juntamente com determinados detalhes específicos da VM. 

Para VMs criadas usando Azure Resource Manager, isso inclui `vmId` ,, `sku` , `nonce` `subscriptionId` , `timeStamp` para a criação e expiração do documento e as informações do plano sobre a imagem. As informações do plano são preenchidas apenas para as imagens do Azure Marketplace. 

Para VMs criadas usando o modelo de implantação clássico, somente `vmId` é garantido que ela seja preenchida. Você pode extrair o certificado da resposta e usá-lo para confirmar que a resposta é válida e proveniente do Azure.

O documento contém os seguintes campos:

Dados | Descrição | Versão introduzida
-----|-------------|-----------------------
licenseType | Tipo de licença para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Observe que isso está presente apenas para VMs habilitadas para AHB. | 2020-09-01
nonce | Uma cadeia de caracteres que pode ser fornecida opcionalmente com a solicitação. Se nenhum `nonce` tiver sido fornecido, o carimbo de data/hora universal coordenado atual será usado. | 01-10-2018
plan | O [plano de imagem do Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Contém a ID do plano (nome), a imagem do produto ou a oferta (produto) e a ID do editor (Publicador). | 01-10-2018
timestamp/createdOn | O carimbo de data/hora universal coordenado para quando o documento assinado foi criado. | 2018-20-01
timestamp/expiresOn | O carimbo de data/hora universal coordenado para quando o documento assinado expira. | 01-10-2018
vmId |  [Identificador exclusivo](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) para a VM. | 01-10-2018
subscriptionId | A assinatura do Azure para a VM. | 2019-04-30
sku | A SKU específica para a imagem da VM. | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>Exemplo 2: validar que a VM está em execução no Azure

Os fornecedores no Azure Marketplace querem garantir que seu software seja licenciado para ser executado somente no Azure. Se alguém copiar o VHD para um ambiente local, o fornecedor precisará ser capaz de detectar isso. Por meio do IMDS, esses fornecedores podem obter dados assinados que garantem a resposta somente do Azure.

> [!NOTE]
> Este exemplo requer que o utilitário JQ seja instalado.

**Solicitação**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Resposta**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Verifique se a assinatura é de Microsoft Azure e verifique se há erros na cadeia de certificados.

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

> [!NOTE]
> Devido ao mecanismo de cache do IMDS, um valor anteriormente armazenado em cache `nonce` pode ser retornado.

O `nonce` no documento assinado pode ser comparado se você forneceu um `nonce` parâmetro na solicitação inicial.

> [!NOTE]
> O certificado para a nuvem pública e cada nuvem soberanas será diferente.

Nuvem | Certificado
------|------------
[Todas as regiões globais do Azure geralmente disponíveis](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Governamental](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> Os certificados podem não ter uma correspondência exata do `metadata.azure.com` para a nuvem pública. Por esse motivo, a validação de certificação deve permitir um nome comum de qualquer `.metadata.azure.com` subdomínio.

Nos casos em que o certificado intermediário não pode ser baixado devido a restrições de rede durante a validação, você pode fixar o certificado intermediário. Observe que o Azure passa pelos certificados, que é a prática PKI padrão. Você precisa atualizar os certificados fixados quando a substituição acontece. Sempre que uma alteração para atualizar o certificado intermediário é planejada, o blog do Azure é atualizado e os clientes do Azure são notificados. 

Você pode encontrar os certificados intermediários no [repositório PKI](https://www.microsoft.com/pki/mscorp/cps/default.htm). Os certificados intermediários de cada uma das regiões podem ser diferentes.

> [!NOTE]
> O certificado intermediário para o Azure China 21Vianet será da CA raiz DigiCert global, em vez de Baltimore.
Se você tiver fixado os certificados intermediários para o Azure China como parte de uma alteração de autoridade de cadeia raiz, os certificados intermediários deverão ser atualizados.

## <a name="managed-identity"></a>Identidade gerenciada

Uma identidade gerenciada, atribuída pelo sistema, pode ser habilitada na VM. Você também pode atribuir uma ou mais identidades gerenciadas atribuídas pelo usuário à VM.
Em seguida, você pode solicitar tokens para identidades gerenciadas de IMDS. Use esses tokens para autenticar com outros serviços do Azure, como Azure Key Vault.

Para obter as etapas detalhadas para habilitar esse recurso, confira [Adquirir um token de acesso](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Eventos agendados
Você pode obter o status dos eventos agendados usando IMDS. Em seguida, o usuário pode especificar um conjunto de ações a serem executadas sobre esses eventos. Para obter mais informações, consulte [eventos agendados](scheduled-events.md).

## <a name="regional-availability"></a>Disponibilidade regional

O serviço está geralmente disponível em todas as nuvens do Azure.

## <a name="sample-code-in-different-languages"></a>Código de exemplo em idiomas diferentes

A tabela a seguir lista exemplos de como chamar IMDS usando diferentes idiomas dentro da VM:

Linguagem      | Exemplo
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Erros e depuração

Se houver um elemento de dados não encontrado ou solicitações malformadas, o serviço de metadados da instância retornará o erro de HTTP padrão. Por exemplo:

Código de status HTTP | Motivo
-----------------|-------
200 OK |
400 Solicitação Inválida | `Metadata: true`Cabeçalho ausente ou parâmetro ausente `format=json` ao consultar um nó folha.
404 Não Encontrado  | O elemento solicitado não existe.
405 método não permitido | Somente `GET` as solicitações têm suporte.
410 não existe mais | Tente novamente mais uma vez por um máximo de 70 segundos.
429 Número excessivo de solicitações | A API atualmente dá suporte a um máximo de 5 consultas por segundo.
500 Erro do serviço     | Tente novamente após algum tempo.

### <a name="frequently-asked-questions"></a>Perguntas frequentes

**Estou recebendo o erro `400 Bad Request, Required metadata header not specified` . O que isso significa?**

IMDS exige que o cabeçalho `Metadata: true` seja passado na solicitação. Passar esse cabeçalho na chamada REST permite o acesso ao IMDS.

**Por que não estou obtendo informações de computação para minha máquina virtual?**

Atualmente, o IMDS só dá suporte a instâncias criadas com Azure Resource Manager.

**Eu criei minha VM por Azure Resource Manager algum tempo atrás. Por que não vejo informações de metadados de computação?**

Se você criou sua VM após setembro de 2016, adicione uma [marca](../../azure-resource-manager/management/tag-resources.md) para começar a ver os metadados de computação. Se você criou sua VM antes de setembro de 2016, adicione ou remova extensões ou discos de dados para a instância de VM para atualizar os metadados.

**Por que não vejo todos os dados populados para uma nova versão?**

Se você criou sua VM após setembro de 2016, adicione uma [marca](../../azure-resource-manager/management/tag-resources.md) para começar a ver os metadados de computação. Se você criou sua VM antes de setembro de 2016, adicione ou remova extensões ou discos de dados para a instância de VM para atualizar os metadados.

**Por que estou recebendo o erro `500 Internal Server Error` ou `410 Resource Gone` ?**

Repita a solicitação. Para obter mais informações, consulte [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults). Se o problema persistir, crie um problema de suporte no portal do Azure para a VM.

**Isso funcionaria para instâncias do conjunto de dimensionamento de máquinas virtuais?**

Sim, IMDS está disponível para instâncias do conjunto de dimensionamento de máquinas virtuais.

**Atualizei minhas marcas em conjuntos de dimensionamento de máquinas virtuais, mas elas não aparecem nas instâncias (ao contrário de VMs de instância única). Estou fazendo algo errado?**

Atualmente, marcas para conjuntos de dimensionamento de máquinas virtuais mostram apenas para a VM em uma reinicialização, reimagem ou alteração de disco para a instância.

**Por que minha solicitação atingiu o tempo limite para minha chamada para o serviço?**

As chamadas de metadados devem ser feitas a partir do endereço IP primário atribuído à placa de rede primária da VM. Além disso, se você alterou suas rotas, deve haver uma rota para o endereço 169.254.169.254/32 na tabela de roteamento local da VM.

   * <details>
        <summary>Verificando sua tabela de roteamento</summary>

        1. Despeje sua tabela de roteamento local e procure a entrada IMDS. Por exemplo:
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
        1. Confirme se a interface corresponde à NIC primária e ao IP primário da VM. Você pode encontrar a NIC e o IP primário examinando a configuração de rede na portal do Azure ou examinando-a com o CLI do Azure. Observe os IPs públicos e privados (e o endereço MAC se você estiver usando a CLI). Aqui está um exemplo de CLI do PowerShell:
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
        1. Se eles não corresponderem, atualize a tabela de roteamento para que a NIC primária e o IP sejam direcionados.
    </details>

## <a name="support"></a>Suporte

Se você não conseguir obter uma resposta de metadados após várias tentativas, poderá criar um problema de suporte no portal do Azure.
Para **tipo de problema**, selecione **Gerenciamento**. Para **categoria**, selecione **serviço de metadados de instância**.

![Captura de tela do suporte ao serviço de metadados de instância](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Próximas etapas

[Adquirir um token de acesso para a VM](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Eventos agendados](scheduled-events.md)

