---
title: Alterações futuras nas regras de ingestão e mesclagem no Azure Time Series Insights Gen2 | Microsoft Docs
description: Alterações da regra de ingestão
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: lyhughes
ms.openlocfilehash: cdf90614e1f766fc37e04a1416081bd35e19b74e
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168194"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Alterações futuras nas regras de saída e mesclagem JSON para novos ambientes

**Essas alterações serão aplicadas somente a ambientes de Azure Time Series Insights Gen2 *criados recentemente* . Essas alterações não se aplicam a ambientes Gen1.**

O ambiente do Azure Time Series Insights Gen2 cria dinamicamente suas colunas de armazenamento, seguindo um conjunto específico de convenções de nomenclatura. Quando um evento é ingerido, um conjunto de regras é aplicado à carga JSON e aos nomes de propriedade. Alterações na forma como os dados JSON são nivelados e armazenados entrarão em vigor para novos Azure Time Series Insights ambientes de Gen2 em julho de 2020. Essa alteração afeta você nos seguintes casos:

* Se seu conteúdo JSON contiver objetos aninhados
* Se seu conteúdo JSON contiver matrizes
* Se você usar qualquer um dos quatro caracteres especiais a seguir em um nome de propriedade JSON: [\. '
* Se uma ou mais das suas propriedades de ID do TS estiverem dentro de um objeto aninhado.

Se você criar um novo ambiente e um ou mais dos casos acima se aplicar à carga do evento, você verá seus dados achatados e armazenados de forma diferente. Veja abaixo um resumo das alterações:

| Regra atual | Nova Regra | JSON de exemplo | Nome da coluna anterior | Nome da nova coluna
|---|---| ---| ---|  ---|
| O JSON aninhado é achatado usando um sublinhado como o delineador |O JSON aninhado é achatado usando um ponto como o delineador  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Caracteres especiais não são de escape | Nomes de propriedades JSON que incluem os caracteres especiais. [\ e ' têm escape usando [' e ']. Em [' e '] há escape adicional de aspas simples e barras invertidas. Uma aspa simples será escrita como \' e uma barra invertida será escrita como\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Matrizes de primitivos são armazenadas como uma cadeia de caracteres | Matrizes de tipos primitivos são armazenadas como um tipo dinâmico  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Matrizes de objetos são sempre achatadas, produzindo vários eventos | Se os objetos dentro de uma matriz não tiverem a ID de TS ou o carimbo de data/hora (s), a matriz de objetos será armazenada inteira como um tipo dinâmico | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Alterações recomendadas para novos ambientes

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Se sua ID de TS e/ou a propriedade Timestamp estiver aninhada dentro de um objeto

* Todas as novas implantações precisarão corresponder às novas regras de ingestão. Por exemplo, se sua ID TS for, `telemetry_tagId` você precisará atualizar qualquer modelo de Azure Resource Manager (ARM) ou scripts de implantação automatizada para configurar `telemetry.tagId` como a ID TS do ambiente. Essa alteração é necessária para carimbos de data/hora de origem do evento em JSON aninhado também.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Se sua carga contiver caracteres JSON ou especiais aninhados e você automatizar a criação de expressões de variável do [modelo de série temporal](.\time-series-insights-update-tsm.md)

* Atualize o código do cliente executando [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) para corresponder às novas regras de ingestão. Por exemplo, uma [expressão de série temporal](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) anterior de `"value": {"tsx": "$event.series_value.Double"}` deve ser atualizada para uma das opções abaixo:
  * `"value": {"tsx": "$event.series.value.Double"}`
  * `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Próximas etapas

* Leia [Azure Time Series insights armazenamento e entrada do Gen2](./time-series-insights-update-storage-ingress.md).

* Leia mais sobre como consultar seus dados usando [APIs de consulta de série temporal](./concepts-query-overview.md).

* Leia mais sobre a [nova sintaxe de expressão de série temporal](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).
