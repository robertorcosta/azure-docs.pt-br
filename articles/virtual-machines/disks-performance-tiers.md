---
title: Alterar o desempenho dos Azure Managed disks
description: Saiba mais sobre as camadas de desempenho para discos gerenciados e saiba como alterar as camadas de desempenho para discos gerenciados existentes usando o módulo Azure PowerShell ou o CLI do Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 923c5970183bd192ac1a2f20fb775d96dcc06865
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540630"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Níveis de desempenho para discos gerenciados (versão prévia)

O Armazenamento em Disco do Azure oferece recursos internos de intermitência para fornecer um desempenho mais alto para o tratamento de tráfego inesperado de curto prazo. O SSDs Premium tem a flexibilidade para aumentar o desempenho do disco sem aumentar o tamanho real do disco. Esse recurso permite que você corresponda às necessidades de desempenho da carga de trabalho e reduza os custos. 

> [!NOTE]
> Esse recurso está atualmente na visualização. 

Esse recurso é ideal para eventos que exigem temporariamente um nível de desempenho consistentemente mais alto, como compras de feriados, testes de desempenho ou execução de um ambiente de treinamento. Para lidar com esses eventos, você pode usar um nível de desempenho mais alto pelo tempo necessário. Em seguida, você pode retornar à camada original quando não precisar mais do desempenho adicional.

## <a name="how-it-works"></a>Como ele funciona

Quando você implanta ou provisiona um disco pela primeira vez, o nível de desempenho de linha de base desse disco é definido com base no tamanho do disco provisionado. Você pode usar um nível de desempenho mais alto para atender à maior demanda. Quando você não precisar mais desse nível de desempenho, poderá retornar para o nível de desempenho de linha de base inicial.

Sua cobrança muda à medida que sua camada é alterada. Por exemplo, se você provisionar um disco P10 (128 GiB), o nível de desempenho de linha de base será definido como P10 (500 IOPS e 100 MBps). Você será cobrado na taxa de P10. Você pode atualizar a camada para corresponder ao desempenho de p50 (7.500 IOPS e 250 MBps) sem aumentar o tamanho do disco. Durante o tempo da atualização, você será cobrado na taxa de P50. Quando você não precisar mais do melhor desempenho, poderá retornar para a camada P10. O disco será novamente cobrado na taxa de P10.

| Tamanho do disco | Camada de desempenho de linha de base | Pode ser atualizado para |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Nenhum |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Nenhum |

Para obter informações de cobrança, consulte [preços de disco gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Restrições

- Atualmente, esse recurso tem suporte apenas para SSDs Premium.
- Você deve desalocar sua VM ou desanexar o disco de uma VM em execução antes de poder alterar a camada do disco.
- O uso dos níveis de desempenho P60, P70 e P80 é restrito a discos de 4.096 GiB ou superior.
- O nível de desempenho de um disco pode ser rebaixado apenas uma vez a cada 24 horas.

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Criar um disco de dados vazio com uma camada superior à camada de linha de base

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Criar um disco do sistema operacional com uma camada superior à camada de linha de base de uma imagem do Azure Marketplace

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk"></a>Atualizar a camada de um disco

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$performanceTier='P1'

$diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
```
---

## <a name="show-the-tier-of-a-disk"></a>Mostrar a camada de um disco

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="next-steps"></a>Próximas etapas

Se você precisar redimensionar um disco para tirar proveito dos níveis de desempenho mais alto, consulte estes artigos:

- [Expandir discos rígidos virtuais em uma VM do Linux com a CLI do Azure](linux/expand-disks.md)
- [Expandir um disco gerenciado conectado a uma máquina virtual do Windows](windows/expand-os-disk.md)
