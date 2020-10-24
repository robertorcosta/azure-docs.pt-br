---
title: Disponibilidade de recursos por região
description: Disponibilidade de recursos de computação e de memória para o serviço de Instâncias de Contêiner do Azure em diferentes regiões do Azure.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 1ed3f50198c0410d9c893fe87523fa214ca03d88
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521451"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos para Instâncias de Contêiner do Azure nas regiões do Azure

Este artigo detalha a disponibilidade de recursos de computação, memória e armazenamento das instâncias de contêiner do Azure nas regiões do Azure e pelo sistema operacional de destino. Para obter uma lista geral de regiões disponíveis para instâncias de contêiner do Azure, consulte [regiões disponíveis](https://azure.microsoft.com/regions/services/).

Os valores apresentados são o máximo de recursos disponíveis para a implantação de um [grupo de contêineres](container-instances-container-groups.md). Os valores estavam atualizados no momento da publicação.

> [!NOTE]
> Os grupos de contêiner criados dentro desses limites de recursos estão sujeitas à disponibilidade dentro da região de implantação. Quando uma região está sob carga pesada, você pode enfrentar uma falha durante a implantação de instâncias. Para atenuar essa falha de implantação, tente implantar instâncias com configurações de recursos menores ou tente a implantação mais tarde ou em uma região diferente com os recursos disponíveis.

Para obter informações sobre cotas e outros limites em suas implantações, confira [cotas e limites para as Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="linux-container-groups"></a>Grupos de contêineres do Linux

As seguintes regiões e recursos máximos estão disponíveis para grupos de contêineres com contêineres do Linux em implantações gerais, implantações de [rede virtual do Azure](container-instances-vnet.md) e implantações com [recursos de GPU](container-instances-gpu.md) (versão prévia).

> [!IMPORTANT]
> O máximo de recursos em uma região é diferente dependendo da sua implantação. Por exemplo, uma região pode ter um tamanho máximo de CPU e memória diferente em uma implantação de rede virtual do Azure do que para uma implantação geral. Essa mesma região também pode ter um conjunto diferente de valores máximos para uma implantação com recursos de GPU. Verifique seu tipo de implantação antes de verificar as tabelas abaixo para obter os valores máximos em sua região.

| Região | CPU máxima | Memória máxima (GB) | CPU máxima da VNET | Memória VNET máxima (GB) | Armazenamento (GB) | SKUs de GPU (versão prévia) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Leste da Austrália | 4 | 16 | 4 | 16 | 50 | N/D |
| Brazil South | 4 | 16 | 2 | 8 | 50 | N/D |
| Canadá Central | 4 | 16 | 4 | 16 | 50 | N/D |
| Índia Central | 4 | 16 | N/D | N/D | 50 | V100 |
| Centro dos EUA | 4 | 16 | 4 | 16 | 50 | N/D |
| Leste da Ásia | 4 | 16 | 4 | 16 | 50 | N/D |
| Leste dos EUA | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Leste dos EUA 2 | 4 | 16 | 4 | 16 | 50 | N/D |
| Leste do Japão | 2 | 8 | 4 | 16 | 50 | N/D |
| Coreia Central | 4 | 16 | N/D | N/D | 50 | N/D |
| Centro-Norte dos EUA | 2 | 3,5 | 4 | 16 | 50 | N/D |
| Norte da Europa | 4 | 16 | 4 | 16 | 50 | K80 |
| Centro-Sul dos Estados Unidos | 4 | 16 | 4 | 16 | 50 | N/D |
| Sudeste Asiático | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| Sul da Índia | 4 | 16 | N/D | N/D | 50 | N/D |
| Sul do Reino Unido | 4 | 16 | 4 | 16 | 50 | N/D |
| Centro-Oeste dos EUA| 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Europa Ocidental | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Oeste dos EUA | 4 | 16 | 2 | 4 | 16| N/D |
| Oeste dos EUA 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

Os recursos máximos a seguir estão disponíveis para um grupo de contêineres implantados com [recursos de GPU](container-instances-gpu.md) (versão prévia).

| SKUs de GPU | Contagem de GPU | CPU máxima | Memória máxima (GB) | Armazenamento (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Grupos de contêineres do Windows

As seguintes regiões e recursos máximos estão disponíveis para grupos de contêineres com [suporte e visualização](container-instances-faq.md#what-windows-base-os-images-are-supported) de contêineres do Windows Server.

| Região | CPU máxima do Windows Server 2016 | Memória máxima do Windows Server 2016 (GB) | CPU máxima do Windows Server 2019 LTSC | Memória máxima do Windows Server 2019 LTSC (GB) | Armazenamento (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Leste da Austrália | 2 | 3,5 | 4 | 16 | 20 |
| Brazil South | 4 | 16 | 4 | 16 | 20 |
| Canadá Central | 2 | 3,5 | 4 | 16 | 20 |
| Índia Central | 2 | 3,5 | 4 | 16 | 20 |
| Centro dos EUA | 2 | 3,5 | 4 | 16 | 20 |
| Leste da Ásia | 2 | 3,5 | 4 | 16 | 20 |
| Leste dos EUA | 2 | 8 | 4 | 16 | 20 |
| Leste dos EUA 2 | 2 | 3,5 | 2 | 3,5 | 20 |
| França Central | 4 | 16 | 4 | 16 | 20 |
| Leste do Japão | 4 | 16 | 4 | 16 | 20 |
| Coreia Central | 4 | 16 | 4 | 16 | 20 |
| Centro-Norte dos EUA | 2 | 3,5 | 4 | 16 | 20 |
| Norte da Europa | 2 | 3,5 | 4 | 16 | 20 |
| Centro-Sul dos Estados Unidos | 2 | 3,5 | 4 | 16 | 20 |
| Sul da Índia | 2 | 3,5 | 4 | 16 | 20 |
| Sudeste Asiático | 2 | 3,5 | 4 | 16 | 20 |
| Sul do Reino Unido | 2 | 3,5 | 4 | 16 | 20 |
| Centro-Oeste dos EUA | 4 | 16 | 4 | 16 | 20 |
| Europa Ocidental | 4 | 16 | 4 | 16 | 20 |
| Oeste dos EUA | 4 | 14 | N/D | N/D | 20 |
| Oeste dos EUA 2 | 2 | 3,5 | 2 | 3,5 | 20 |

## <a name="next-steps"></a>Próximas etapas

Informe à equipe se você gostaria de ver outras regiões ou uma maior disponibilidade de recursos em [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre como solucionar problemas de implantação de instâncias de contêiner, confira [Solucionar problemas de implantação com Instâncias de Contêiner do Azure](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
