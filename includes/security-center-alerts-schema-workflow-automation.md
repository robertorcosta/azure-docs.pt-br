---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 0b6864c3304b86e80549297fc073a2e387000d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272828"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

|Campo|Tipo de dados|Descrição|
|----|----|----|
|**AlertDisplayName**|String|O nome de exibição do alerta.|
|**AlertType**|String|O tipo de alerta. Alertas do mesmo tipo devem ter o mesmo valor. Este campo é uma seqüência de tecla com chave representando o tipo de alerta e não uma instância de alerta. Todas as instâncias de alerta da mesma lógica de detecção/análise devem ter o mesmo valor para o tipo de alerta.|
|**Entidade comprometida**|String|O nome de exibição do recurso mais relacionado a este alerta.|
|**Descrição**|String|Descrição do alerta.|
|**EndTimeUtc**|Datetime|O tempo do último evento ou atividade incluído no alerta.  O campo deve ser uma string que esteja em conformidade com o formato ISO8601, incluindo informações de fuso horário UTC.|
|**Entities**|IEnumerable (IEntity)|Uma lista de entidades relacionadas ao alerta. Esta lista pode conter uma mistura de entidades de diversos tipos. O tipo de entidades pode ser qualquer um dos tipos definidos na seção Entidades. Entidades que não estão na lista abaixo também podem ser enviadas, porém não é garantido que elas serão processadas (o alerta não deixará de ser validação com novos tipos de entidades).|
|**Extendedproperties**|Dicionário (String,String)|Os provedores podem (opcionalmente) incluir campos personalizados aqui.|
|**Intenção**|Enum|A intenção da cadeia de morte por trás do alerta. Para obter a lista de valores suportados e explicações das intenções suportadas da cadeia de morte do Azure Security Center, consulte [Intenções](../articles/security-center/alerts-reference.md#intentions).<br/>Este campo pode ter múltiplos valores (separados por comma).|
|**IsIncident**|Bool|Este campo determina se o alerta é um incidente (um agrupamento composto de vários alertas) ou um único alerta. O valor padrão para o campo é 'falso' (o que significa que é um único alerta).|
|**ProcessingEndTime**|Datetime|O momento em que o alerta estava acessível ao usuário final no produto original segurando o alerta.|
|**ProductName**|String|O nome do produto que publicou este alerta (Azure Security Center, Azure ATP, Microsoft Defender ATP, O365 ATP, MCAS, etc.).|
|**Medidas de remediação**|Lista<String>|Itens de ação manuais a serem necessários para remediar o alerta.|
|**Identificadores de recursos**|Lista (identificadores de recursos)|Os identificadores de recursos para este alerta que podem ser usados para direcionar o alerta para o grupo de exposição do produto certo (inquilino, espaço de trabalho, assinatura etc.). Pode haver vários identificadores de diferentes tipos por alerta.|
|**Severidade**|Enum|A gravidade do alerta conforme relatado pelo provedor. Valores Possíveis: Informativo, Baixo, Médio e Alto.|
|**StartTimeUtc**|Datetime|O tempo do primeiro evento ou atividade incluído no alerta. O campo deve ser uma string que esteja em conformidade com o formato ISO8601, incluindo informações de fuso horário UTC.|
|**Status**|Enum|O estado do ciclo de vida do alerta.<br/>Os status suportados são: Novo, Resolvido, Descartado, Desconhecido.<br/>Um alerta que especifica um valor diferente das opções suportadas é atribuído ao status 'Desconhecido'.<br/>Um alerta que não especifica um valor é atribuído ao status 'Novo'.|
|**SystemAlertId**|String|O identificador de alerta.|
|**TimeGenerated**|Datetime|O momento em que o alerta foi gerado pelo provedor de alerta. Se não for notificado por provedores de alerta internos, um produto pode optar por atribuir o tempo recebido para processamento pelo produto.  O campo deve ser uma string que esteja em conformidade com o formato ISO8601, incluindo informações de fuso horário UTC.|
|**Nome do fornecedor**|String|O nome do fornecedor que levanta o alerta.|
|||
