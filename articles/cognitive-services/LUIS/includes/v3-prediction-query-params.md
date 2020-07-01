---
title: Parâmetros da cadeia de consulta da API v3
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 47727f6df772669fa323a10cd099764a6d35cb6a
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610771"
---
Os parâmetros da cadeia de consulta da API v3 incluem:

|Parâmetro de consulta|Nome do portal do LUIS|Type|Versão|Padrão|Finalidade|
|--|--|--|--|--|--|
|`log`|Salvar logs|booleano|V2 & V3|false|Armazenar consulta no arquivo de log. O valor padrão é falso.|
|`query`|-|string|Somente V3|Nenhum padrão-ele é necessário na solicitação GET|**Em v2**, o expressão a ser previsto está no `q` parâmetro. <br><br>**No v3**, a funcionalidade é passada no `query` parâmetro.|
|`show-all-intents`|Incluir pontuações para todas as intenções|booleano|Somente V3|false|Retorne todas as intenções com a pontuação correspondente no objeto **preditiva. retenções** . As intenções são retornadas como objetos em um `intents` objeto pai. Isso permite o acesso programático sem a necessidade de encontrar a intenção em uma matriz: `prediction.intents.give` . Em v2, elas foram retornadas em uma matriz. |
|`verbose`|Incluir mais detalhes de entidades|booleano|V2 & V3|false|**Em v2**, quando definido como true, todas as intenções previstas foram retornadas. Se você precisar de todas as intenções previstas, use o parâmetro v3 de `show-all-intents` .<br><br>**No v3**, esse parâmetro fornece apenas detalhes de metadados de entidade de previsão de entidade.  |
|`timezoneOffset`|-|string|V2|-|Fuso horário aplicado às entidades datetimeV2.|
|`datetimeReference`|-|string|V3|-|[Fuso horário](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) aplicado às entidades datetimeV2. Substitui `timezoneOffset` de v2.|