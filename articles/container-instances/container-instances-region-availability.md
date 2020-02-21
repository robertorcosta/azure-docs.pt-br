---
title: Disponibilidade de recursos por região
description: Disponibilidade de recursos de computação e de memória para o serviço de Instâncias de Contêiner do Azure em diferentes regiões do Azure.
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: f429a165fe26cc9fc7aa973231f5a77163feef4a
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525287"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos para Instâncias de Contêiner do Azure nas regiões do Azure

Este artigo detalha a disponibilidade de recursos de computação, memória e armazenamento das instâncias de contêiner do Azure nas regiões do Azure e pelo sistema operacional de destino. 

Os valores apresentados são o máximo de recursos disponíveis para a implantação de um [grupo de contêineres](container-instances-container-groups.md). Os valores estavam atualizados no momento da publicação. 

> [!NOTE]
> Os grupos de contêiner criados dentro desses limites de recursos estão sujeitas à disponibilidade dentro da região de implantação. Quando uma região está sob carga pesada, você pode enfrentar uma falha durante a implantação de instâncias. Para atenuar essa falha de implantação, tente implantar instâncias com configurações de recursos inferiores ou tente implantar em um momento posterior.

Para obter informações sobre cotas e outros limites em suas implantações, confira [cotas e limites para as Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilidade – geral

As seguintes regiões e recursos máximos estão disponíveis para grupos de contêineres com Linux e contêineres com [suporte](container-instances-faq.md#what-windows-base-os-images-are-supported) do Windows Server 2016.

| Regiões | Sistema operacional | CPU máxima | Memória máxima (GB) | Armazenamento (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Sul do Brasil, Canadá central, Índia central, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, Europa Ocidental, oeste dos EUA, oeste dos EUA 2 | Linux | 4 | 16 | 50 |
| Leste da Austrália, Leste do Japão | Linux | 2 | 8 | 50 |
| Centro-Norte dos EUA | Linux | 2 | 3,5 | 50 |
| Sul do Brasil, leste do Japão, Europa Ocidental | Windows | 4 | 16 | 20 |
| Leste dos EUA, Oeste dos EUA | Windows | 4 | 14 | 20 |
| Leste da Austrália, Canadá central, Índia central, EUA Central, Ásia Oriental, leste dos EUA 2, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, oeste dos EUA 2 | Windows | 2 | 3,5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Disponibilidade – Implantações do Windows Server 2019 LTSC, 1809 (versão prévia)

As seguintes regiões e recursos máximos estão disponíveis para grupos de contêineres com contêineres baseados no Windows Server 2019 (versão prévia).

| Regiões | Sistema operacional | CPU máxima | Memória máxima (GB) | Armazenamento (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Leste da Austrália, sul do Brasil, centro-Canadá, Índia central, EUA Central, Ásia Oriental, leste dos EUA, leste do Japão, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, Europa Ocidental | Windows | 4 | 16 | 20 |
| Leste dos EUA 2, oeste dos EUA 2 | Windows | 2 | 3,5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Disponibilidade-implantação de rede virtual

As seguintes regiões e recursos máximos estão disponíveis para um grupo de contêineres implantados em uma [rede virtual do Azure](container-instances-vnet.md).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilidade – recursos de GPU (versão prévia)

As seguintes regiões e recursos máximos estão disponíveis para um grupo de contêineres implantados com [recursos de GPU](container-instances-gpu.md) (versão prévia).

> [!IMPORTANT]
> Os recursos de GPU estão disponíveis somente mediante solicitação. Para solicitar acesso aos recursos de GPU, envie uma [solicitação de suporte do Azure][azure-support].

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Próximas etapas

Informe à equipe se você gostaria de ver outras regiões ou uma maior disponibilidade de recursos em [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre como solucionar problemas de implantação de instâncias de contêiner, confira [Solucionar problemas de implantação com Instâncias de Contêiner do Azure](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest