---
author: baanders
description: arquivo de inclusão para opções de filtro de rota de gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: c5b78d80b1aa958af50b81dc152b4a746ce85f70
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902175"
---
| Nome do filtro | Descrição | Filtrar esquema de texto | Valores com suporte | 
| --- | --- | --- | --- |
| Verdadeiro/falso | Permite criar uma rota sem filtragem ou desabilitar uma rota para que nenhum evento seja enviado | `<true/false>` | `true`= a rota está habilitada sem filtragem <br> `false`= a rota está desabilitada |
| Type | O [tipo de evento](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) que flui pela sua instância de cópia digital | `type = '<eventType>'` | Estes são os valores de tipo de evento possíveis: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Fonte | Nome da instância do gêmeos digital do Azure | `source = '<hostname>'`| Estes são os possíveis valores de nome de host: <br> **Para notificações**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Para telemetria**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Assunto | Uma descrição do evento no contexto da origem do evento acima | `subject = '<subject>'` | Estes são os possíveis valores de assunto: <br>**Para notificações**: o assunto é`<twinid>` <br> ou um formato de URI para entidades, que são identificadas exclusivamente por várias partes ou IDs:<br>`<twinid>/relationships/<relationshipid>`<br> **Para telemetria**: o assunto é o caminho do componente (se a telemetria for emitida de um componente de entrelaçamento), como `comp1.comp2` . Se a telemetria não for emitida de um componente, o campo assunto estará vazio. |
| Esquema de dados | ID do modelo DTDL | `dataschema = '<model-dtmi-ID>'` | **Para telemetria**: o esquema de dados é a ID do modelo de a/ou o componente que emite a telemetria. Por exemplo, `dtmi:example:com:floor4;2` <br>**Para notificações**: não há suporte para o esquema de dados|
| Tipo de conteúdo | Tipo de conteúdo do valor de dados | `datacontenttype = '<contentType>'` | O tipo de conteúdo é`application/json` |
| Versão de especificação | A versão do esquema de evento que você está usando | `specversion = '<version>'` | A versão deve ser `1.0` . Isso indica o esquema CloudEvents versão 1,0 |

Também é possível combinar filtros usando as seguintes operações:

| Nome do filtro | Filtrar esquema de texto | Exemplo | 
| --- | --- | --- |
| E/OU | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| E/OU | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| Operações aninhadas | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> Durante a visualização, há suporte apenas para igualdade de cadeia de caracteres (=,! =).

Quando você implementa ou atualiza um filtro, a alteração pode levar alguns minutos para ser refletida no pipeline de dados.
