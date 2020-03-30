---
title: Gerenciar o dimensionamento vertical do cluster (escala para cima) para corresponder à demanda no Azure Data Explorer
description: Este artigo descreve etapas para escalar e reduzir um cluster Azure Data Explorer com base na mudança de demanda.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 95275598febae2b6b0355a7bc3e512490dae500d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560432"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gerencie o dimensionamento vertical do cluster (scale up) no Azure Data Explorer para acomodar a demanda em mudança

Dimensionar um cluster apropriadamente é essencial para o desempenho do Azure Data Explorer. Um tamanho de cluster estático pode levar a subutilização ou superutilização, e nenhuma dessas condições é a ideal.

Uma vez que a demanda em um cluster não pode ser prevista com precisão absoluta, uma abordagem melhor é *escalar* um cluster, adicionando e removendo recursos de capacidade e CPU com a mudança de demanda. 

Existem dois fluxos de trabalho para dimensionar um cluster Azure Data Explorer:

* [Dimensionamento horizontal,](manage-cluster-horizontal-scaling.md)também chamado de escala para dentro e para fora.
* Dimensionamento vertical, também chamado de escala para cima e para baixo.

Este artigo explica o fluxo de trabalho de dimensionamento vertical:

## <a name="configure-vertical-scaling"></a>Configurar dimensionamento vertical

1. No portal Azure, acesse o recurso de cluster do Azure Data Explorer. Em **Configurações,** selecione **Dimensionar**.

1. Na janela **Escala-up,** você verá uma lista de SKUs disponíveis para o seu cluster. Por exemplo, na figura a seguir, apenas quatro SKUs estão disponíveis.

    ![Escalar verticalmente](media/manage-cluster-vertical-scaling/scale-up.png)

    As SKUs estão desativadas porque são o SKU atual, ou não estão disponíveis na região onde o cluster está localizado.

1. Para alterar seu SKU, selecione um novo SKU e clique **em Selecionar**.

> [!NOTE]
> * O processo de dimensionamento vertical pode levar alguns minutos e durante esse tempo seu cluster será suspenso. 
> * A redução pode prejudicar o desempenho do cluster.
> * O preço é uma estimativa das máquinas virtuais do cluster e dos custos de serviço do Azure Data Explorer. Outros custos não estão incluídos. Consulte a página [do estimador de custos](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) do Azure Data Explorer para obter uma estimativa e a [página de preços](https://azure.microsoft.com/pricing/details/data-explorer/) do Azure Data Explorer para obter informações completas sobre preços.

Agora, você configurou o dimensionamento vertical para o cluster Azure Data Explorer. Adicione outra regra para uma escala horizontal. Se você precisar de assistência com problemas de dimensionamento de cluster, [abra uma solicitação](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) de suporte no portal Azure.

## <a name="next-steps"></a>Próximas etapas

* [Gerencie](manage-cluster-horizontal-scaling.md) o dimensionamento horizontal do cluster para dimensionar dinamicamente a contagem de instâncias com base nas métricas especificadas.

* Monitore o uso de seus recursos seguindo este artigo: [Monitore o desempenho, a saúde e o uso do Azure Data Explorer com métricas](using-metrics.md).

