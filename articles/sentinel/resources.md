---
title: Recursos úteis ao trabalhar com o Azure Sentinel| Microsoft Docs
description: Este documento fornece uma lista de recursos úteis ao trabalhar com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585264"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Recursos úteis para trabalhar com o Azure Sentinel



Este artigo lista recursos que podem ajudá-lo a obter mais informações sobre como trabalhar com o Azure Sentinel.

Conectores de aplicativos azure logic:<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Auditoria e relatórios
Os registros de auditoria do Azure Sentinel são mantidos nos [Registros de Atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).

As seguintes operações suportadas podem ser auditadas.

|Nome da operação|    Tipo de recurso|
|----|----|
|Criar ou atualizar a carteira de trabalho  |Microsoft.Insights/livros de trabalho|
|Excluir agenda de trabalho    |Microsoft.Insights/livros de trabalho|
|Definir fluxo de trabalho   |Microsoft.Logic/workflows|
|Excluir fluxo de trabalho    |Microsoft.Logic/workflows|
|Criar pesquisa salva    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Excluir pesquisa salva    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Atualizar regras de alerta |Microsoft.SecurityInsights/alertRules|
|Excluir regras de alerta |Microsoft.SecurityInsights/alertRules|
|Atualizar ações de resposta a regras de alerta |Microsoft.SecurityInsights/alertaRegras/ações|
|Excluir ações de resposta a regras de alerta |Microsoft.SecurityInsights/alertaRegras/ações|
|Atualizar marcadores   |Microsoft.SecurityInsights/marcadores|
|Excluir marcadores   |Microsoft.SecurityInsights/marcadores|
|Atualizar casos   |Microsoft.SecurityInsights/Cases|
|Atualizar investigação de caso  |Microsoft.SecurityInsights/Cases/investigações|
|Criar comentários de caso   |Microsoft.SecurityInsights/Cases/comentários|
|Atualizar conectores de dados |Microsoft.SecurityInsights/dataConnectors|
|Excluir conectores de dados |Microsoft.SecurityInsights/dataConnectors|
|Atualizar configurações    |Microsoft.SecurityInsights/configurações|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Ver dados de auditoria e relatórios no Azure Sentinel

Você pode visualizar esses dados transmitindo-os do log do Azure Activity no Azure Sentinel, onde você pode então realizar pesquisas e análises sobre ele.

1. Conecte a fonte de dados [do Azure Activity.](connect-azure-activity.md) Depois disso, os eventos de auditoria são transmitidos para uma nova tabela na tela **Logs** chamada AzureActivity.
2. Em seguida, consultar os dados usando KQL, como faria com qualquer outra tabela.



## <a name="vendor-documentation"></a>Documentação do fornecedor

| **Fornecedor**  | **Use incidente no Azure Sentinel** | **Link**|
|----|----|----|
| GitHub| Usado para acessar a página da Comunidade| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Configurar CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| Pluralsight | Curso da Linguagem de Consulta Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs e fóruns

Poste suas perguntas no [espaço TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) para o Azure Sentinel.

Veja as postagens do blog do Azure Sentinel da [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) e [do Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Próximas etapas
Neste documento, você tem uma lista de recursos que são úteis quando você está trabalhando com o Azure Sentinel. Você encontrará informações adicionais sobre segurança e conformidade do Azure no [blog Microsoft Azure Security and Compliance](https://blogs.msdn.com/b/azuresecurity/).
