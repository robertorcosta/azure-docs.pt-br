---
title: Planejar o ambiente de visualização-Azure Time Series Insights | Microsoft Docs
description: Saiba como planejar seu ambiente de Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 9fb2dcf2c05b709340f8e9ae549bab5756e6abf2
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420314"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planeje o ambiente de versão prévia do Azure Time Series Insights

Este artigo descreve as práticas recomendadas para planejar e começar rapidamente usando o Azure Time Series Insights Preview.

> [!NOTE]
> Para obter as práticas recomendadas para planejar uma instância de Time Series Insights de disponibilidade geral, consulte [planejar seu Azure Time Series insights ambiente de disponibilidade geral](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas de planejamento e preparação

As práticas recomendadas sobre o planejamento e a preparação de seu ambiente são descritas mais detalhadamente nos seguintes artigos:

* O que você obtém ao [provisionar um ambiente de visualização de time Series insights](#the-preview-environment).
* Quais [são suas IDs de série temporal e propriedades de carimbo de data/hora](#configure-time-series-ids-and-timestamp-properties).
* Qual é o novo [modelo de série temporal](#understand-the-time-series-model)e como criar os seus próprios.
* Como [enviar eventos com eficiência no JSON](#shape-your-events).
* Time Series Insights [Opções de recuperação de desastres de negócios](#business-disaster-recovery).

Azure Time Series Insights emprega um modelo de negócios pago conforme o uso. Para saber mais sobre cobranças e capacidade, confira o [preço do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>O ambiente de visualização

Ao provisionar um ambiente de versão prévia do Time Series Insights, é possível criar dois recursos do Azure:

* Um ambiente de Versão Prévia do Azure Time Series Insights
* Uma conta de Uso Geral V1 do Armazenamento do Azure

Como parte do processo de provisionamento, você especifica se deseja habilitar uma loja a quente. A loja a quente fornece uma experiência de consulta em camadas. Quando habilitado, você deve especificar um período de retenção entre 7 e 30 dias. As consultas executadas no período de retenção de armazenamento quente geralmente fornecem tempos de resposta mais rápidos. Quando uma consulta se estende pelo período de retenção de armazenamento quente, ela é servida por meio da loja fria.

As consultas na loja a quente são gratuitas, enquanto as consultas em armazenamento frio incorrem em custos. É importante entender seus padrões de consulta e planejar sua configuração de armazenamento quente adequadamente. É recomendável que a análise interativa nos dados mais recentes resida em sua loja e análise de padrões quentes e tendências de longo prazo residam em frio.

> [!NOTE]
> Atualmente, damos suporte a um máximo de 1.000 propriedades com armazenamento quente.

Para começar, são necessários três itens adicionais:

* Um [modelo de série temporal](./time-series-insights-update-tsm.md)
* Uma [origem de evento conectada ao Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Os eventos fluem para a origem do evento](./time-series-insights-send-events.md) mapeada no modelo e válida em formato JSON

## <a name="review-preview-limits"></a>Examinar os limites de visualização

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurar IDs de série temporal e propriedades de carimbo de data/hora

Para criar um novo ambiente do Time Series Insights, selecione uma ID de série temporal, que atuará como uma partição lógica dos dados. Conforme observado, deixe suas IDs de série temporal prontas.

> [!IMPORTANT]
> As IDs de série temporal *não podem ser alteradas posteriormente*. Verifique cada uma antes da seleção final e do primeiro uso.

Você pode selecionar até três chaves para diferenciar exclusivamente seus recursos. Para saber mais, leia [Melhores práticas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md) e [Armazenamento e entrada](./time-series-insights-update-storage-ingress.md).

A propriedade **timestamp** também é importante. É possível designar essa propriedade ao adicionar origens de eventos. Cada origem de evento tem uma propriedade opcional de carimbo de data/hora usada para rastrear origens de eventos ao longo do tempo. Os valores de carimbo de data/hora diferenciam maiúsculas de minúsculas e precisam estar formatados de acordo com a especificação individual de cada origem de evento.

> [!TIP]
> Verifique os requisitos de formatação e análise das origens de evento.

Quando deixado em branco, o Tempo de Enfileiramento do Evento de uma origem do evento é usado como o carimbo de data/hora do evento. Se você enviar dados históricos ou eventos em lote, personalizar a propriedade carimbo de data/hora será mais útil que o padrão Tempo de Enfileiramento do Evento. Para obter mais informações, leia sobre como [adicionar fontes de eventos no Hub IOT do Azure](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Entenda o modelo de série temporal

Agora, é possível configurar o modelo de série temporal do ambiente do Time Series Insights. O novo modelo facilita localizar e analisar dados IoT. Permite a preservação, a manutenção e o enriquecimento dos dados de série temporal e ajuda a preparar conjuntos de dados prontos para consumo. O modelo usa IDs de série temporal, que mapeiam para uma instância que associa o recurso exclusivo a variáveis, conhecidas como tipos e hierarquias. Leia sobre o novo [modelo de série temporal](./time-series-insights-update-tsm.md).

O modelo é dinâmico, portanto, pode ser criado a qualquer momento. Para começar rapidamente, crie e envie-o por upload antes de efetuar push dos dados para o Time Series Insights. Para criar seu modelo, confira [Usar o modelo de série temporal](./time-series-insights-update-how-to-tsm.md).

Para muitos clientes, o modelo de série temporal mapeia um modelo existente do ativo ou sistema ERP já em vigor. Se você não tiver um modelo existente, uma experiência de usuário predefinida será [fornecida](https://github.com/Microsoft/tsiclient) para começar rapidamente. Para prever como um modelo poderá ajudar você, exiba o [exemplo de ambiente de demonstração](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Formatar os eventos

É possível verificar a maneira como você envia eventos para o Time Series Insights. O ideal é que os eventos sejam desnormalizados de maneira eficiente.

Uma boa regra prática:

* Armazene metadados no seu modelo de série temporal.
* Verifique se o modo de série temporal, os campos de instância e os eventos incluem apenas as informações necessárias, como uma ID de série temporal ou uma propriedade Timestamp.

Para saber mais, confira [Formatar eventos](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximas etapas

- Examine o [Azure Advisor](../advisor/advisor-overview.md) para planejar suas opções de configuração de recuperação de negócios.
- Leia mais sobre [armazenamento e entrada](./time-series-insights-update-storage-ingress.md) na visualização de time Series insights.
- Saiba mais sobre a [modelagem de dados](./time-series-insights-update-tsm.md) na visualização de time Series insights.
