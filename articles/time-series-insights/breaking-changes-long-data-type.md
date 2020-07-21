---
title: Adicionando suporte para tipo de dados Long | Microsoft Docs
description: Suporte para tipo de dados Long
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: dpalled
ms.openlocfilehash: c31ca7fd3eca89159d583b8a51b59a7bd6b8ed67
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530897"
---
# <a name="adding-support-for-long-data-type"></a>Adicionando suporte para tipo de dados Long

Essas alterações serão aplicadas somente a ambientes Gen2. Se você tiver um ambiente Gen1, poderá desconsiderar essas alterações.

Estamos fazendo alterações em como armazenamos e indexamos dados numéricos em Azure Time Series Insights Gen2 que podem afetar você. Se você for afetado por qualquer um dos casos abaixo, faça as alterações necessárias assim que possível. Seus dados começarão a ser indexados como longos e duplos entre 29 de junho e 30 de junho de 2020, dependendo da sua região. Se você tiver dúvidas ou preocupações sobre essa alteração, envie um tíquete de suporte por meio do portal do Azure e mencione essa comunicação.

Essa alteração afeta você nos seguintes casos:

1. Se você usa atualmente variáveis de modelo de série temporal e envia apenas tipos de dados integral em seus dados de telemetria.
1. Se você usa atualmente variáveis de modelo de série temporal e envia tipos de dados integral e não integral em seus dados de telemetria.
1. Se você usar variáveis categóricas para mapear valores inteiros para categorias.
1. Se você usar o SDK do JavaScript para criar um aplicativo de front-end personalizado.
1. Se você estiver se aproximando do limite de nome da propriedade 1.000 na loja a quente (WS) e enviar dados integral e não integral, a contagem de propriedades poderá ser exibida como uma métrica no [portal do Azure](https://portal.azure.com/).

Se qualquer um dos casos acima se aplicar a você, você precisará fazer alterações no modelo para acomodar essa alteração. Atualize a expressão de série temporal em sua definição de variável no Azure Time Series Insights Explorer Gen2 e em qualquer cliente personalizado usando nossas APIs com as alterações recomendadas. Veja abaixo para obter mais detalhes.

Dependendo de sua solução e restrições de IoT, talvez você não tenha visibilidade dos dados que estão sendo enviados para seu ambiente de Azure Time Series Insights Gen2. Se você não tiver certeza se seus dados são somente integrais ou integralmente e não integrantes, você tem algumas opções. Você pode aguardar a liberação do recurso e, em seguida, explorar seus eventos brutos na interface do usuário do Explorer para entender quais propriedades foram salvas em duas colunas separadas. Você poderia, de forma preventiva, fazer as alterações abaixo para todas as marcas numéricas ou rotear temporariamente um subconjunto de eventos para o armazenamento para entender melhor e explorar seu esquema. Para armazenar eventos, ative a [captura de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) para os hubs de eventos ou a [rota](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) do Hub IOT para o armazenamento de BLOBs do Azure. Os dados também podem ser observados por meio do [Gerenciador de Hub de eventos](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)ou usando o host do processador de [eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events). Se você usar o Hub IoT, consulte a documentação [aqui](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) sobre como acessar o ponto de extremidade interno.

Observe que, se você for afetado por essas alterações e não puder fazê-las pelas datas acima, poderá ocorrer uma interrupção em que as variáveis de série temporal afetadas acessadas por meio das APIs de consulta ou Time Series Insights Explorer retornará *NULL* (ou seja, não mostrar nenhum dado no Gerenciador).

## <a name="recommended-changes"></a>Alterações recomendadas

Caso 1 & 2: **usando variáveis de modelo de série temporal e enviando apenas tipos de dados inteiros ou enviando tipos integrais e não integrais em dados de telemetria.**

Se você estiver enviando dados de telemetria de inteiros no momento, seus dados serão divididos em duas colunas: "propertyValue_double" e "propertyValue_long".

Seus dados inteiros serão gravados em "propertyValue_long" quando as alterações entrarem em vigor e dados numéricos ingeridos anteriormente (e ingeridos futuramente) em "propertyValue_double" não serão copiados.

Se você quiser consultar dados entre essas duas colunas para a propriedade "propertyValue", precisará usar a função escalar de *adesão ()* no TSX. A função aceita argumentos do mesmo tipo de dados e retorna o primeiro valor não nulo na lista de argumentos (Leia mais sobre o uso [aqui](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)).

### <a name="variable-definition-in-time-series-explorer---numeric"></a>Definição de variável no Gerenciador de séries temporais – numeric

*Definição de variável anterior:*

[![Definição de variável anterior](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Nova definição de variável:*

[![Nova definição de variável](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Você também pode usar *"adesão ($Event. PropertyValue. Double e toduplo ($Event. PropertyValue. Long))"* como a expressão de [série temporal personalizada.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>Definição de variável embutida usando APIs de consulta de série temporal – numeric

*Definição de variável anterior:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*Nova definição de variável:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

Você também pode usar *"adesão ($Event. PropertyValue. Double e toduplo ($Event. PropertyValue. Long))"* como a expressão de [série temporal personalizada.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Recomendamos que você atualize essas variáveis em todos os locais em que elas possam ser usadas (modelo de série temporal, consultas salvas, Power BI consultas de conector).

Caso 3: **usando variáveis categóricas para mapear valores inteiros para categorias**

Se você estiver usando variáveis categóricas que mapeiam valores inteiros para categorias, provavelmente está usando a função tolong para converter dados de tipo duplo para tipo longo. Como nos casos acima, você precisará fazer a União das colunas de tipo de dados Double e Long.

### <a name="variable-definition-in-time-series-explorer---categorical"></a>Definição de variável no Gerenciador de séries temporais-categórica

*Definição de variável anterior:*

[![Definição de variável anterior](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Nova definição de variável:*

[![Nova definição de variável](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Você também pode usar *"adesão ($Event. PropertyValue. Double e toduplo ($Event. PropertyValue. Long))"* como a expressão de [série temporal personalizada.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

Variáveis categóricas ainda exigem que o valor seja de um tipo inteiro. O DataType de todos os argumentos em adesão () deve ser do tipo Long na [expressão de série temporal personalizada.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>Definição de variável embutida usando APIs de consulta de série temporal-categórica

*Definição de variável anterior:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*Nova definição de variável:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

Variáveis categóricas ainda exigem que o valor seja de um tipo inteiro. O DataType de todos os argumentos em adesão () deve ser do tipo Long na [expressão de série temporal personalizada.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Recomendamos que você atualize essas variáveis em todos os locais em que elas possam ser usadas (modelo de série temporal, consultas salvas, Power BI consultas de conector).

Caso 4: **usando o SDK do JavaScript para criar um aplicativo de front-end personalizado**

Se você for afetado pelos casos 1-3 acima e criar aplicativos personalizados, precisará atualizar suas consultas para usar a função de *adesão ()* , conforme demonstrado nos exemplos acima.

Caso 5: **perto do limite de propriedades de armazenamento quente 1.000**

Se você for um usuário de armazenamento quente com um grande número de propriedades e acreditar que essa alteração enviaria ao seu ambiente o limite de nome de propriedade WS 1.000, envie um tíquete de suporte por meio do portal do Azure e mencione essa comunicação.

## <a name="next-steps"></a>Próximas etapas

* Consulte [tipos de dados com suporte](concepts-supported-data-types.md) para exibir a lista completa de tipos de dados com suporte.
