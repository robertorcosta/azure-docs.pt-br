---
title: Disponibilidade de recursos por região
description: Disponibilidade de recursos de computação e de memória para o serviço de Instâncias de Contêiner do Azure em diferentes regiões do Azure.
ms.topic: article
ms.date: 12/17/2019
ms.author: danlep
ms.openlocfilehash: 456b9754d7a6e88705853d4c4d701110e4841eb0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445826"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos para Instâncias de Contêiner do Azure nas regiões do Azure

Este artigo fornece detalhes sobre a disponibilidade de recursos de computação e de memória de Instâncias de Contêiner do Azure em regiões do Azure. 

Os valores apresentados são o máximo de recursos disponíveis para a implantação de um [grupo de contêineres](container-instances-container-groups.md). Os valores estavam atualizados no momento da publicação. 

> [!NOTE]
> Os grupos de contêiner criados dentro desses limites de recursos estão sujeitas à disponibilidade dentro da região de implantação. Quando uma região está sob carga pesada, você pode enfrentar uma falha durante a implantação de instâncias. Para atenuar essa falha de implantação, tente implantar instâncias com configurações de recursos inferiores ou tente implantar em um momento posterior.

Para obter informações sobre cotas e outros limites em suas implantações, confira [cotas e limites para as Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilidade – geral

As regiões e os recursos a seguir estão disponíveis para grupos de contêineres com o Linux e contêineres baseados em Windows Server 2016 com [suporte](container-instances-faq.md#what-windows-base-os-images-are-supported).

| Regiões | SISTEMA OPERACIONAL | CPU máxima | Memória máxima (GB) |
| -------- | -- | :---: | :-----------: |
| Sul do Brasil, Canadá central, Índia central, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, Europa Ocidental, oeste dos EUA, oeste dos EUA 2 | Linux | 4 | 16 |
| Leste da Austrália, Leste do Japão | Linux | 2 | 8 |
| Centro-Norte dos EUA | Linux | 2 | 3,5 |
| Sul do Brasil, leste do Japão, Europa Ocidental | Windows | 4 | 16 |
| Leste dos Estados Unidos, Oeste dos Estados Unidos | Windows | 4 | 14 |
| Leste da Austrália, Canadá central, Índia central, EUA Central, Ásia Oriental, leste dos EUA 2, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, oeste dos EUA 2 | Windows | 2 | 3,5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Disponibilidade – Implantações do Windows Server 2019 LTSC, 1809 (versão prévia)

As regiões e os recursos a seguir estão disponíveis para grupos de contêineres baseados em Windows Server 2019 (versão prévia).

| Regiões | SISTEMA OPERACIONAL | CPU máxima | Memória máxima (GB) |
| -------- | -- | :---: | :-----------: |
| Leste da Austrália, sul do Brasil, centro-Canadá, Índia central, EUA Central, Ásia Oriental, leste dos EUA, leste do Japão, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, Europa Ocidental | Windows | 4 | 16 |
| Leste dos EUA 2, oeste dos EUA 2 | Windows | 2 | 3,5 |


## <a name="availability---virtual-network-deployment-preview"></a>Disponibilidade – implantação da rede virtual (versão prévia)

As seguintes regiões e recursos estão disponíveis para um grupo de contêineres implantado em uma [rede virtual do Azure](container-instances-vnet.md) (versão prévia).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilidade – recursos de GPU (versão prévia)

As seguintes regiões e recursos estão disponíveis para um grupo de contêineres implantados com [recursos de GPU](container-instances-gpu.md) (versão prévia).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Próximos passos

Informe à equipe se você gostaria de ver outras regiões ou uma maior disponibilidade de recursos em [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre como solucionar problemas de implantação de instâncias de contêiner, confira [Solucionar problemas de implantação com Instâncias de Contêiner do Azure](container-instances-troubleshooting.md).
