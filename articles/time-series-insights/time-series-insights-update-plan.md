---
title: Plan your Preview environment - Azure Time Series Insights | Microsoft Docs
description: Learn how to plan your Azure Time Series Insights Preview environment.
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

This article describes best practices to plan and get started quickly by using Azure Time Series Insights Preview.

> [!NOTE]
> For best practices to plan a general availability Time Series Insights instance, see [Plan your Azure Time Series Insights general availability environment](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Melhores práticas de planejamento e preparação

Best practices surrounding planning for and preparing your environment are described further in the following articles:

* What you get when you [provision a Time Series Insights Preview environment](#the-preview-environment).
* What your [Time Series IDs and Timestamp properties are](#configure-time-series-ids-and-timestamp-properties).
* What the new [Time Series Model is](#understand-the-time-series-model), and how to build your own.
* How to [send events efficiently in JSON](#shape-your-events).
* Time Series Insights [business disaster recovery options](#business-disaster-recovery).

Azure Time Series Insights employs a pay-as-you-go business model. Para saber mais sobre cobranças e capacidade, confira o [preço do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>The preview environment

Ao provisionar um ambiente de versão prévia do Time Series Insights, é possível criar dois recursos do Azure:

* Um ambiente de Versão Prévia do Azure Time Series Insights
* Uma conta de Uso Geral V1 do Armazenamento do Azure

As part of the provisioning process, you specify whether you want to enable a warm store. Warm store provides you with a tiered query experience. When enabled, you must specify a retention period between 7 and 30 days. Queries executed within the warm store retention period generally provide faster response times. When a query spans over the warm store retention period, it's served from cold store.

Queries on warm store are free, while queries on cold store incur costs. It's important to understand your query patterns and plan your warm store configuration accordingly. We recommend that interactive analytics on the most recent data reside in your warm store and pattern analysis and long-term trends reside in cold.

> [!NOTE]
> We currently support a maximum of 1,000 properties with warm store.

Para começar, são necessários três itens adicionais:

* Um [modelo de série temporal](./time-series-insights-update-tsm.md)
* Uma [origem de evento conectada ao Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Os eventos fluem para a origem do evento](./time-series-insights-send-events.md) mapeada no modelo e válida em formato JSON

## <a name="review-preview-limits"></a>Review preview limits

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configure Time Series IDs and Timestamp properties

Para criar um novo ambiente do Time Series Insights, selecione uma ID de série temporal, que atuará como uma partição lógica dos dados. Conforme observado, deixe suas IDs de série temporal prontas.

> [!IMPORTANT]
> Time Series IDs *can't be changed later*. Verifique cada uma antes da seleção final e do primeiro uso.

You can select up to three keys to uniquely differentiate your resources. Para saber mais, leia [Melhores práticas para escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md) e [Armazenamento e entrada](./time-series-insights-update-storage-ingress.md).

The **Timestamp** property is also important. É possível designar essa propriedade ao adicionar origens de eventos. Cada origem de evento tem uma propriedade opcional de carimbo de data/hora usada para rastrear origens de eventos ao longo do tempo. Os valores de carimbo de data/hora diferenciam maiúsculas de minúsculas e precisam estar formatados de acordo com a especificação individual de cada origem de evento.

> [!TIP]
> Verifique os requisitos de formatação e análise das origens de evento.

Quando deixado em branco, o Tempo de Enfileiramento do Evento de uma origem do evento é usado como o carimbo de data/hora do evento. Se você enviar dados históricos ou eventos em lote, personalizar a propriedade carimbo de data/hora será mais útil que o padrão Tempo de Enfileiramento do Evento. For more information, read about how to [add event sources in Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Entenda o modelo de série temporal

Agora, é possível configurar o modelo de série temporal do ambiente do Time Series Insights. O novo modelo facilita localizar e analisar dados IoT. Permite a preservação, a manutenção e o enriquecimento dos dados de série temporal e ajuda a preparar conjuntos de dados prontos para consumo. The model uses Time Series IDs, which map to an instance that associates the unique resource with variables, known as types, and hierarchies. Leia sobre o novo [modelo de série temporal](./time-series-insights-update-tsm.md).

O modelo é dinâmico, portanto, pode ser criado a qualquer momento. Para começar rapidamente, crie e envie-o por upload antes de efetuar push dos dados para o Time Series Insights. Para criar seu modelo, confira [Usar o modelo de série temporal](./time-series-insights-update-how-to-tsm.md).

Para muitos clientes, o modelo de série temporal mapeia um modelo existente do ativo ou sistema ERP já em vigor. Se você não tiver um modelo existente, uma experiência de usuário predefinida será [fornecida](https://github.com/Microsoft/tsiclient) para começar rapidamente. Para prever como um modelo poderá ajudar você, exiba o [exemplo de ambiente de demonstração](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Formatar os eventos

É possível verificar a maneira como você envia eventos para o Time Series Insights. O ideal é que os eventos sejam desnormalizados de maneira eficiente.

Uma boa regra prática:

* Store metadata in your Time Series Model.
* Ensure that Time Series Mode, instance fields, and events include only necessary information, such as a Time Series ID or Timestamp property.

Para saber mais, confira [Formatar eventos](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximos passos

- Review [Azure Advisor](../advisor/advisor-overview.md) to plan out your business recovery configuration options.
- Read more about [storage and ingress](./time-series-insights-update-storage-ingress.md) in the Time Series Insights Preview.
- Learn about [data modeling](./time-series-insights-update-tsm.md) in the Time Series Insights Preview.
