---
title: Aumente o desempenho do disco gerenciado do Azure
description: Saiba mais sobre as camadas de desempenho para discos gerenciados, além de como atualizar as camadas de desempenho para os discos gerenciados.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: aa188babf56d4a825059fe6103e2e07745eb134f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974129"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Níveis de desempenho para discos gerenciados (versão prévia)

O Armazenamento em Disco do Azure atualmente oferece recursos internos de intermitência para alcançar um desempenho mais alto para o tratamento de tráfego inesperado de curto prazo. O SSDs Premium tem a flexibilidade para aumentar o desempenho do disco sem aumentar o tamanho real do disco, permitindo que você corresponda às necessidades de desempenho da carga de trabalho e reduza os custos, pois esse recurso está atualmente em versão prévia. Isso é ideal para eventos que exigem temporariamente um nível de desempenho consistentemente mais alto, como compras de feriados, testes de desempenho ou execução de um ambiente de treinamento. Para lidar com esses eventos, você pode selecionar um nível de desempenho superior, desde que seja necessário, e retornar à camada original quando o desempenho adicional não for mais necessário.

## <a name="how-it-works"></a>Como ele funciona

Quando você implanta ou provisiona um disco pela primeira vez, o nível de desempenho de linha de base desse disco é definido com base no tamanho do disco provisionado. Um nível de desempenho mais alto pode ser selecionado para atender à demanda mais alta e, quando esse desempenho não for mais necessário, você poderá retornar ao nível de desempenho de linha de base inicial. Por exemplo, se você provisionar um disco P10 (128 GiB), o nível de desempenho de linha de base será definido como P10 (500 IOPS e 100 MB/s). Você pode atualizar a camada para corresponder ao desempenho de p50 (7500 IOPS e 250 MB/s) sem aumentar o tamanho do disco e retornar ao P10 quando o desempenho superior não for mais necessário.

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

## <a name="restrictions"></a>Restrições

- Atualmente, só tem suporte para SSDs Premium.
- Os discos devem ser desanexados de uma VM em execução antes de alterar as camadas.
- O uso dos níveis de desempenho P60, P70 e P80 é restrito a discos de 4096 GiB ou superior.

## <a name="regional-availability"></a>Disponibilidade regional

O ajuste do nível de desempenho de um disco gerenciado está atualmente disponível apenas para SSDs Premium nas seguintes regiões:

- Centro-Oeste dos EUA 
- Leste dos EUA 2 
- Europa Ocidental
- Austrália oriental 
- Sudeste da Austrália 
- Sul da Índia

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Criar/atualizar um disco de dados com uma camada superior à camada de linha de base

1. Crie um disco de dados vazio com uma camada superior à camada de linha de base ou atualize a camada de um disco maior do que a camada de linha de base usando o modelo de exemplo [CreateUpdateDataDiskWithTier.jsem](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. Confirmar a camada do disco

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>Criar/atualizar um disco do sistema operacional com uma camada superior à camada de linha de base

1. Criar um disco do sistema operacional de uma imagem do Marketplace ou atualizar a camada de um disco do sistema operacional maior que a camada de linha de base usando o modelo de exemplo [CreateUpdateOSDiskWithTier.jsem](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json)

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. Confirmar a camada do disco
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>Próximas etapas

Se você precisar redimensionar um disco para aproveitar os níveis de desempenho maiores, consulte nossos artigos sobre o assunto:

- [Expandir discos rígidos virtuais em uma VM do Linux com a CLI do Azure](linux/expand-disks.md)
- [Expandir um disco gerenciado conectado a uma máquina virtual do Windows](windows/expand-os-disk.md)