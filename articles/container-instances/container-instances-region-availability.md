---
title: Disponibilidade de recursos por região
description: Disponibilidade de recursos de computação e de memória para o serviço de Instâncias de Contêiner do Azure em diferentes regiões do Azure.
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: f429a165fe26cc9fc7aa973231f5a77163feef4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247131"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos para Instâncias de Contêiner do Azure nas regiões do Azure

Este artigo detalha a disponibilidade dos recursos de computação, memória e armazenamento do Azure Em regiões do Azure e pelo sistema operacional de destino. 

Os valores apresentados são o máximo de recursos disponíveis para a implantação de um [grupo de contêineres](container-instances-container-groups.md). Os valores estavam atualizados no momento da publicação. 

> [!NOTE]
> Os grupos de contêiner criados dentro desses limites de recursos estão sujeitas à disponibilidade dentro da região de implantação. Quando uma região está sob carga pesada, você pode enfrentar uma falha durante a implantação de instâncias. Para atenuar essa falha de implantação, tente implantar instâncias com configurações de recursos inferiores ou tente implantar em um momento posterior.

Para obter informações sobre cotas e outros limites em suas implantações, confira [cotas e limites para as Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilidade – geral

As seguintes regiões e recursos máximos estão disponíveis para grupos de contêineres com Linux e contêineres baseados no Windows Server 2016 [suportados.](container-instances-faq.md#what-windows-base-os-images-are-supported)

| Regiões | Sistema operacional | CPU máxima | Memória Máxima (GB) | Armazenamento (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Brasil Sul, Canadá Central, Índia Central, EUA Central, Leste Da Ásia, Leste dos EUA, Leste dos EUA 2, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Sul da Índia, Reino Unido Sul, Europa Ocidental, Oeste dos EUA, Oeste dos EUA 2 | Linux | 4 | 16 | 50 |
| Leste da Austrália, Leste do Japão | Linux | 2 | 8 | 50 |
| Centro-Norte dos EUA | Linux | 2 | 3,5 | 50 |
| Brasil Sul, Japão Leste, Europa Ocidental | Windows | 4 | 16 | 20 |
| Leste dos EUA, Oeste dos EUA | Windows | 4 | 14 | 20 |
| Austrália Leste, Canadá Central, Índia Central, Eua Central, Leste da Ásia, Leste dos EUA 2, Norte Central dos EUA, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Sul da Índia, Reino Unido Sul, Oeste dos EUA 2 | Windows | 2 | 3,5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Disponibilidade – Implantações do Windows Server 2019 LTSC, 1809 (versão prévia)

As seguintes regiões e recursos máximos estão disponíveis para grupos de contêineres com contêineres baseados no Windows Server 2019 (visualização).

| Regiões | Sistema operacional | CPU máxima | Memória Máxima (GB) | Armazenamento (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Austrália Leste, Brasil Sul, Canadá Central, Índia Central, Central dos EUA, Leste da Ásia, Leste dos EUA, Japão Leste, Norte Central dos EUA, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Sul da Índia, Reino Unido Sul, Europa Ocidental | Windows | 4 | 16 | 20 |
| Leste dos EUA 2, Oeste dos EUA 2 | Windows | 2 | 3,5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Disponibilidade - Implantação de rede virtual

As seguintes regiões e recursos máximos estão disponíveis para um grupo de contêineres implantado em uma [rede virtual Azure](container-instances-vnet.md).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilidade – recursos de GPU (versão prévia)

As seguintes regiões e recursos máximos estão disponíveis para um grupo de contêineres implantado com [recursos de GPU](container-instances-gpu.md) (visualização).

> [!IMPORTANT]
> Os recursos da GPU estão disponíveis apenas mediante solicitação. Para solicitar acesso aos recursos da GPU, envie uma [solicitação de suporte do Azure][azure-support].

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Próximas etapas

Informe à equipe se você gostaria de ver outras regiões ou uma maior disponibilidade de recursos em [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre como solucionar problemas de implantação de instâncias de contêiner, confira [Solucionar problemas de implantação com Instâncias de Contêiner do Azure](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest