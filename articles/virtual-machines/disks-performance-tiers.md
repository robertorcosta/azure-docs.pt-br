---
title: Alterar o desempenho dos Azure Managed disks
description: Saiba mais sobre as camadas de desempenho para discos gerenciados, além de como alterar as camadas de desempenho para os discos gerenciados existentes.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7da500c3f18b7bf7057b0c5875bc9b39136a6483
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396579"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Níveis de desempenho para discos gerenciados (versão prévia)

O Armazenamento em Disco do Azure atualmente oferece recursos internos de intermitência para alcançar um desempenho mais alto para o tratamento de tráfego inesperado de curto prazo. O SSDs Premium tem a flexibilidade para aumentar o desempenho do disco sem aumentar o tamanho real do disco, permitindo que você corresponda às necessidades de desempenho da carga de trabalho e reduza os custos, pois esse recurso está atualmente em versão prévia. Isso é ideal para eventos que exigem temporariamente um nível de desempenho consistentemente mais alto, como compras de feriados, testes de desempenho ou execução de um ambiente de treinamento. Para lidar com esses eventos, você pode selecionar um nível de desempenho superior, desde que seja necessário, e retornar à camada original quando o desempenho adicional não for mais necessário.

## <a name="how-it-works"></a>Como ele funciona

Quando você implanta ou provisiona um disco pela primeira vez, o nível de desempenho de linha de base desse disco é definido com base no tamanho do disco provisionado. Um nível de desempenho mais alto pode ser selecionado para atender à demanda mais alta e, quando esse desempenho não for mais necessário, você poderá retornar ao nível de desempenho de linha de base inicial.

Sua cobrança muda à medida que sua camada é alterada. Por exemplo, se você provisionar um disco P10 (128 GiB), o nível de desempenho de linha de base será definido como P10 (500 IOPS e 100 MB/s) e você será cobrado na taxa de P10. Você pode atualizar a camada para corresponder ao desempenho de p50 (7500 IOPS e 250 MB/s) sem aumentar o tamanho do disco, durante o qual você será cobrado na taxa de P50. Quando o melhor desempenho não for mais necessário, você poderá retornar para a camada P10 e o disco será novamente cobrado na taxa de P10.

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

- Atualmente, só tem suporte para SSDs Premium.
- Os discos devem ser desanexados de uma VM em execução antes de alterar as camadas.
- O uso dos níveis de desempenho P60, P70 e P80 é restrito a discos de 4096 GiB ou superior.
- Um nível de desempenho de discos só pode ser alterado uma vez A cada 24 horas.

## <a name="regional-availability"></a>Disponibilidade regional

O ajuste do nível de desempenho de um disco gerenciado está atualmente disponível apenas para SSDs Premium nas seguintes regiões:

- Centro-Oeste dos EUA 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Criar um disco de dados vazio com uma camada superior à camada de linha de base

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
     
## <a name="update-the-tier-of-a-disk"></a>Atualizar a camada de um disco

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>Mostrar a camada de um disco

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Próximas etapas

Se você precisar redimensionar um disco para aproveitar os níveis de desempenho maiores, consulte nossos artigos sobre o assunto:

- [Expandir discos rígidos virtuais em uma VM do Linux com a CLI do Azure](linux/expand-disks.md)
- [Expandir um disco gerenciado conectado a uma máquina virtual do Windows](windows/expand-os-disk.md)
