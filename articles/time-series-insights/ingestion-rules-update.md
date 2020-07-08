---
title: Alterações futuras nas regras de ingestão e mesclagem no Azure Time Series Insights | Microsoft Docs
description: Alterações da regra de ingestão
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: lyhughes
ms.openlocfilehash: 067244aa40256e3cc76239343790974bc3c06481
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85919026"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Alterações futuras nas regras de saída e mesclagem JSON para novos ambientes

Essas alterações serão aplicadas somente a *novos* ambientes de Azure Time Series insights PAYG (pré-pago). Essas alterações não se aplicam a ambientes de SKU Standard (S).

Seu ambiente de Azure Time Series Insights cria dinamicamente suas colunas de armazenamento, seguindo um determinado conjunto de convenções de nomenclatura. Quando um evento é ingerido, um conjunto de regras é aplicado à carga JSON e aos nomes de propriedade. As alterações na forma como os dados JSON são achatados e armazenados entrarão em vigor para novos Azure Time Series Insights ambientes pagos conforme o uso em julho de 2020. Essa alteração afeta você nos seguintes casos:

* Se seu conteúdo JSON contiver objetos aninhados
*  Se seu conteúdo JSON contiver matrizes
*  Se você usar qualquer um dos quatro caracteres especiais a seguir em um nome de propriedade JSON: [\. '
*  Se uma ou mais das suas propriedades de ID do TS estiverem dentro de um objeto aninhado.

Se você criar um novo ambiente e um ou mais dos casos acima se aplicar à carga do evento, você verá seus dados achatados e armazenados de forma diferente. Veja abaixo um resumo das alterações:

| Regra atual | Nova Regra | JSON de exemplo | Nome da coluna anterior | Nome da nova coluna
|---|---| ---| ---|  ---|
| O JSON aninhado é achatado usando um sublinhado como o delineador |O JSON aninhado é achatado usando um ponto como o delineador  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Caracteres especiais não são de escape | Nomes de propriedades JSON que incluem os caracteres especiais. [\ e ' têm escape usando [' e ']. Em [' e '] há escape adicional de aspas simples e barras invertidas. Uma aspa simples será escrita como \' e uma barra invertida será escrita como\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` | 
| Matrizes de primitivos são armazenadas como uma cadeia de caracteres | Matrizes de tipos primitivos são armazenadas como um tipo dinâmico  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Matrizes de objetos são sempre achatadas, produzindo vários eventos | Se os objetos dentro de uma matriz não tiverem a ID de TS ou o carimbo de data/hora (s), a matriz de objetos será armazenada inteira como um tipo dinâmico | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Alterações recomendadas para novos ambientes

#### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Se a sua ID TS e/ou a propriedade Timestamp estiver aninhada dentro de um objeto:

*  Todas as novas implantações precisarão corresponder às novas regras de ingestão. Por exemplo, se sua ID de TS for, `telemetry_tagId` você precisará atualizar todos os modelos de ARM ou scripts de implantação automatizados para configurar `telemetry.tagId` como a ID de TS do ambiente. Essa alteração é necessária para carimbos de data/hora de origem do evento em JSON aninhado também.

 #### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Se sua carga contiver caracteres JSON ou especiais aninhados e você automatizar a criação de expressões de variável do [modelo de série temporal](.\time-series-insights-update-tsm.md)

*  Atualize o código do cliente executando [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch#typesbatchput) para corresponder às novas regras de ingestão. Por exemplo, uma [expressão de série temporal](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) anterior de `"value": {"tsx": "$event.series_value.Double"}` deve ser atualizada para uma das opções abaixo:
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`



## <a name="next-steps"></a>Próximas etapas

- Leia [adicionando suporte para tipo de dados Long](./time-series-insights-long-data-type.md).

- Leia [Azure Time Series insights visualização de armazenamento e entrada](./time-series-insights-update-storage-ingress.md).

