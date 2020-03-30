---
title: Visão geral dos logs da plataforma Azure | Microsoft Docs
description: Visão geral dos logs no Azure Monitor que fornecem dados ricos e freqüentes sobre a operação de um recurso do Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659313"
---
# <a name="overview-of-azure-platform-logs"></a>Visão geral dos logs de plataforma do Azure
Os registros da plataforma fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure da qual dependem. Eles são gerados automaticamente, embora você precise configurar certos logs de plataforma para serem encaminhados para um ou mais destinos a serem retidos. Este artigo fornece uma visão geral dos logs da plataforma, incluindo quais informações eles fornecem e como você pode configurá-las para coleta e análise.

## <a name="types-of-platform-logs"></a>Tipos de logs de plataforma
A tabela a seguir lista os logs de plataforma específicos que estão disponíveis em diferentes camadas do Azure.

| Log | Camada | Descrição |
|:---|:---|:---|
| Logs de recursos | Recursos do Azure | Forneça informações sobre as operações que foram realizadas dentro de um recurso do Azure (o plano de *dados),* por exemplo, obter um segredo de um Key Vault ou fazer uma solicitação a um banco de dados. O conteúdo dos registros de recursos varia de acordo com o serviço e o tipo de recurso do Azure.<br><br>*Os registros de recursos foram previamente referidos como registros de diagnóstico.*  |
| Log de atividades | Assinatura do Azure | Fornece informações sobre as operações em cada recurso do Azure na assinatura de fora (*o plano*de gerenciamento ), além de atualizações sobre eventos de Saúde do Serviço. Use o Registro de Atividades, para determinar _o que_, _e_ _quando_ para quaisquer operações de gravação (PUT, POST, DELETE) tomadas sobre os recursos em sua assinatura. Também é possível compreender o status da operação e outras propriedades relevantes.  Há um único registro de atividade supérse cada assinatura do Azure. |
| Logs do Azure Active Directory | Locatário do Azure |  Contém o histórico de atividade de login e trilha de auditoria de alterações feitas no Diretório Ativo do Azure para um inquilino específico. Veja [quais são os relatórios do Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md) para uma descrição completa dos Logs de Diretório Ativo do Azure.   |

> [!NOTE]
> O Log de Atividades do Azure é usado principalmente para atividades que ocorrem no Azure Resource Manager. Ele não controla os recursos usando o modelo Clássico/RDFE. Alguns tipos de recursos Clássicos têm um provedor de recursos de proxy no Azure Resource Manager (por exemplo, Microsoft.ClassicCompute). Se você interagir com um tipo de recurso Clássico por meio do Azure Resource Manager usando esses provedores de recursos de proxy, as operações aparecerão no Log de Atividades. Se você interagir com um tipo de recurso clássico fora dos proxies do Azure Resource Manager, suas ações somente serão registradas no Log de Operação. O Log de Operação pode ser pesquisado em uma seção separada do portal.

![Visão geral de logs de plataforma](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Visualização de logs de plataforma
Existem diferentes opções para visualizar e analisar os diferentes registros da plataforma Azure.

- Exibir o login de atividade no portal do Azure e acessar eventos da PowerShell e cli. Consulte [Exibir e recuperar eventos de registro de atividades do Azure](activity-log-view.md) para obter detalhes. 
- Veja os relatórios de segurança e atividade do diretório ativo do Azure no portal Azure. Veja [quais são os relatórios do Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)  para obter detalhes.
- Os logs de recursos são gerados automaticamente pelos recursos suportados do Azure, mas eles não estão disponíveis para serem visualizados a menos que você os envie para um [destino](#destinations). 

## <a name="destinations"></a>Destinos
Você pode enviar logs de plataforma para um ou mais dos destinos na tabela a seguir, dependendo dos seus requisitos de monitoramento. Configure destinos para logs de plataforma [criando uma configuração Diagnóstico](diagnostic-settings.md).

| Destino | Cenário | Referências |
|:---|:---|:---|:---|
| Espaço de trabalho do Log Analytics | Analise os logs com outros dados de monitoramento e aproveite os recursos do Azure Monitor, como consultas de log e alertas. | [Registro de atividades e registros de recursos](resource-logs-collect-workspace.md)<br>[Logs do Diretório de Atividades do Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Armazenamento do Azure | Arquive os registros para auditoria, análise estática ou backup. |[Registro de atividades e registros de recursos](archive-diagnostic-logs.md)<br>[Logs do Diretório de Atividades do Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de Eventos | Transmita os registros para sistemas de registro e telemetria de terceiros.  |[Registro de atividades e registros de recursos](resource-logs-stream-event-hubs.md)<br>[Logs do Diretório de Atividades do Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Próximas etapas

* [Exibir o esquema de registro de atividades para diferentes categorias](activity-log-schema.md)
* [Exibir o esquema de registro de recursos para diferentes serviços do Azure](diagnostic-logs-schema.md)
