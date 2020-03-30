---
title: Planeje seu ambiente de pré-visualização - Azure Time Series Insights | Microsoft Docs
description: Práticas recomendadas para configurar, gerenciar, planejar e implantar o ambiente de visualização de insights da série do tempo do Azure.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b16d78c9670d05fcec8126c5544d1dd97f6a03bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045711"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planeje o ambiente de versão prévia do Azure Time Series Insights

Este artigo descreve as melhores práticas para planejar e começar rapidamente usando o Azure Time Series Insights Preview.

> [!NOTE]
> Para obter as melhores práticas para planejar uma instância geral de disponibilidade do Time Series Insights, leia [O ambiente geral de disponibilidade do Azure Time Series Insights](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas de planejamento e preparação

As melhores práticas em torno do planejamento e preparação do seu ambiente são descritas ainda nos seguintes artigos:

* O que você ganha quando [você provisiona um ambiente de visualização de insights da série do tempo](#the-preview-environment).
* Quais são suas [propriedades de IDs de série soda e carimbo de tempo](#configure-time-series-ids-and-timestamp-properties).
* O que é o novo [Modelo de Série do Tempo](#understand-the-time-series-model)e como construir o seu próprio.
* Como [enviar eventos de forma eficiente no JSON](#shape-your-events).
* Time Series Insights [opções de recuperação de desastres de negócios](#business-disaster-recovery).

O Azure Time Series Insights emprega um modelo de negócio pago à medida que você vai. Para obter mais informações sobre taxas e capacidade, leia [os preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>O ambiente de visualização

Ao provisionar um ambiente de versão prévia do Time Series Insights, é possível criar dois recursos do Azure:

* Um ambiente de Versão Prévia do Azure Time Series Insights
* Uma conta de Uso Geral V1 do Armazenamento do Azure

Como parte do processo de provisionamento, você especifica se deseja habilitar uma loja quente. A loja quente fornece uma experiência de consulta hierárquica. Quando ativado, você deve especificar um período de retenção entre 7 e 30 dias. As consultas executadas dentro do período de retenção do armazenamento quente geralmente fornecem tempos de resposta mais rápidos. Quando uma consulta se estende sobre o período de retenção de lojas quentes, ela é servida a partir de cold store.

As consultas na loja quente são gratuitas, enquanto as consultas sobre o cold store incorrem em custos. É importante entender seus padrões de consulta e planejar sua configuração de loja quente de acordo. Recomendamos que as análises interativas sobre os dados mais recentes residam em sua loja quente e análise de padrões e tendências de longo prazo residem no frio.

> [!NOTE]
> Para ler mais sobre como consultar seus dados quentes, leia a Referência da [API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters).

Para começar, são necessários três itens adicionais:

* Um [modelo de série temporal](./time-series-insights-update-tsm.md)
* Uma [origem de evento conectada ao Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Os eventos fluem para a origem do evento](./time-series-insights-send-events.md) mapeada no modelo e válida em formato JSON

## <a name="review-preview-limits"></a>Revisar limites de visualização

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configure ids de série de tempo e propriedades de carimbo de tempo

Para criar um novo ambiente do Time Series Insights, selecione uma ID de série temporal, que atuará como uma partição lógica dos dados. Conforme observado, deixe suas IDs de série temporal prontas.

> [!IMPORTANT]
> As IDs da série temporal *não podem ser alteradas mais tarde*. Verifique cada uma antes da seleção final e do primeiro uso.

Você pode selecionar até três chaves para diferenciar exclusivamente seus recursos. Para saber mais, leia [Melhores práticas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md) e [Armazenamento e entrada](./time-series-insights-update-storage-ingress.md).

A propriedade **Timestamp** também é importante. É possível designar essa propriedade ao adicionar origens de eventos. Cada origem de evento tem uma propriedade opcional de carimbo de data/hora usada para rastrear origens de eventos ao longo do tempo. Os valores de carimbo de data/hora diferenciam maiúsculas de minúsculas e precisam estar formatados de acordo com a especificação individual de cada origem de evento.

> [!TIP]
> Verifique os requisitos de formatação e análise das origens de evento.

Quando deixado em branco, o Tempo de Enfileiramento do Evento de uma origem do evento é usado como o carimbo de data/hora do evento. Se você enviar dados históricos ou eventos em lote, personalizar a propriedade carimbo de data/hora será mais útil que o padrão Tempo de Enfileiramento do Evento. Para obter mais informações, leia sobre como [adicionar fontes de eventos no Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Entenda o modelo de série temporal

Agora, é possível configurar o modelo de série temporal do ambiente do Time Series Insights. O novo modelo facilita localizar e analisar dados IoT. Permite a preservação, a manutenção e o enriquecimento dos dados de série temporal e ajuda a preparar conjuntos de dados prontos para consumo. O modelo usa IDs de série temporal, que mapeiam para uma instância que associa o recurso exclusivo com variáveis, conhecidas como tipos e hierarquias. Leia sobre o novo [modelo de série temporal](./time-series-insights-update-tsm.md).

O modelo é dinâmico, portanto, pode ser criado a qualquer momento. Para começar rapidamente, crie e envie-o por upload antes de efetuar push dos dados para o Time Series Insights. Para construir seu modelo, leia [Use o Modelo de Série do Tempo](./time-series-insights-update-how-to-tsm.md).

Para muitos clientes, o modelo de série temporal mapeia um modelo existente do ativo ou sistema ERP já em vigor. Se você não tiver um modelo existente, uma experiência de usuário predefinida será [fornecida](https://github.com/Microsoft/tsiclient) para começar rapidamente. Para prever como um modelo poderá ajudar você, exiba o [exemplo de ambiente de demonstração](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Formatar os eventos

É possível verificar a maneira como você envia eventos para o Time Series Insights. O ideal é que os eventos sejam desnormalizados de maneira eficiente.

Uma boa regra prática:

* Armazene metadados em seu modelo de série soadora.
* Certifique-se de que o modo de série de tempo, os campos de ocorrência e os eventos incluam apenas informações necessárias, como uma id de série de tempo ou propriedade de carimbo de tempo.

Para obter mais informações, leia [os eventos Shape](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximas etapas

- Revise [o Azure Advisor](../advisor/advisor-overview.md) para planejar suas opções de configuração de recuperação de negócios.
- Leia mais sobre [armazenamento e ingestão](./time-series-insights-update-storage-ingress.md) na Visualização de Insights da Série Do Tempo.
- Saiba mais sobre [a modelagem de dados](./time-series-insights-update-tsm.md) na visualização de insights da série do tempo.
