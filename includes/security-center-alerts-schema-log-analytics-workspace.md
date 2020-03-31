---
title: incluir arquivo
description: incluir arquivo
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79538825"
---
### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

|Campo|Descrição|
|----|----|
|**AlertName**|Nome de exibição de alerta|
|**AlertType**|identificador de alerta único|
|**Nível de confiança**|(Opcional) O nível de confiança deste alerta (Alto/Baixo)|
|**Placar de Confiança**|(Opcional) Indicador numérico de confiança do alerta de segurança|
|**Descrição**|Texto de descrição para o alerta|
|**DisplayName**|O nome de exibição do alerta|
|**Endtime**|O tempo final do impacto do alerta (a hora do último evento contribuindo para o alerta)|
|**Entities**|Uma lista de entidades relacionadas ao alerta. Esta lista pode conter uma mistura de entidades de diversos tipos|
|**Links estendidos**|(Opcional) Um saco para todos os links relacionados com o alerta. Este saco pode conter uma mistura de links para diversos tipos|
|**Extendedproperties**|Um saco de campos adicionais que são relevantes para o alerta|
|**IsIncident**|Determina se o alerta é um incidente ou um alerta regular. Um incidente é um alerta de segurança que agrega vários alertas em um incidente de segurança|
|**ProcessingEndTime**|Carimbo de tempo UTC no qual o alerta foi criado|
|**Nome do componente do produto**|(Opcional) O nome de um componente dentro do produto que gerou o alerta.|
|**ProductName**|constante ('Azure Security Center')|
|**Providername**|unused|
|**Medidas de remediação**|Itens de ação manuais a serem necessários para corrigir a ameaça à segurança|
|**Resourceid**|Identificador completo do recurso afetado|
|**Severidade**|A gravidade do alerta (Alta/Média/Baixa/Informacional)|
|**SourceComputerId**|um GUID exclusivo para o servidor afetado (se o alerta for gerado no servidor)|
|**SourceSystem**|unused|
|**StartTime**|O tempo de início do impacto do alerta (a hora do primeiro evento contribuindo para o alerta)|
|**SystemAlertId**|Identificador exclusivo desta instância de alerta de segurança|
|**Inquilino**|o identificador do inquilino do diretório Azure Active da assinatura a qual o recurso digitalizado reside|
|**TimeGenerated**|Carimbo de horário UTC no qual a avaliação ocorreu (tempo de varredura do Security Center) (idêntico ao DiscoveredTimeUTC)|
|**Tipo**|constante ('SecurityAlert')|
|**Nome do fornecedor**|O nome do fornecedor que forneceu o alerta (por exemplo, 'Microsoft')|
|**VendorOriginalID**|unused|
|**WorkspaceResourceGroup**|no caso de o alerta ser gerado em uma instância vm, server, virtual machine scale set ou app service que é reportado a um espaço de trabalho, contém o nome do grupo de recursos do espaço de trabalho|
|**WorkspaceSubscriptionId**|no caso de o alerta ser gerado em uma instância vm, server, virtual machine scale set ou app service que reporta a um espaço de trabalho, contém que a assinatura do espaço de trabalhoId|
|||
