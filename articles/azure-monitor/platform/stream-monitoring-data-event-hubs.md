---
title: Transmitir dados de monitoramento do Azure para um hub de eventos
description: Saiba como transmitir seus dados de monitoramento do Azure para um hub de eventos para obter os dados em uma ferramenta de análise SIEM ou parceiro.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 32bc90cc069ac82641c3aa7692c900c60db7ba87
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733095"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Transmitir dados de monitoramento do Azure para um hub de eventos
O Azure Monitor fornece uma solução completa de monitoramento de pilha completa para aplicativos e serviços no Azure, em outras nuvens e no local. Além de usar o Azure Monitor para analisar esses dados e aproveitá-los para diferentes cenários de monitoramento, você pode precisar enviá-los para outras ferramentas de monitoramento em seu ambiente. O método mais eficaz para transmitir dados de monitoramento para ferramentas externas na maioria dos casos é o uso [do Azure Event Hubs](/azure/event-hubs/). Este artigo fornece uma breve descrição de como você pode transmitir dados de monitoramento de diferentes fontes para um hub de eventos e links para orientação detalhada.


## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

Antes de configurar o streaming para qualquer fonte de dados, você precisa [criar um namespace e um hub de eventos do Event Hubs](../../event-hubs/event-hubs-create.md). Esse namespace e hub de eventos é o destino para todos os seus dados de monitoramento. Um namespace dos Hubs de Eventos é um agrupamento lógico de hubs de eventos que compartilham a mesma política de acesso, assim como uma conta de armazenamento tem blobs individuais dentro dessa conta de armazenamento. Considere os seguintes detalhes sobre os hubs de eventos namespace e hubs de eventos que você usa para transmitir dados de monitoramento:

* O número de unidades de taxa de transferência permite aumentar a escala de taxa de transferência para os hubs de eventos. Apenas uma unidade de throughput é normalmente necessária. Se você precisar aumentar à medida que o uso do log aumenta, você pode aumentar manualmente o número de unidades de throughput para o namespace ou ativar a inflação automática.
* O número de partições permite paralelizar o consumo em muitos consumidores. Uma única partição pode suportar até 20MBps ou aproximadamente 20.000 mensagens por segundo. Dependendo da ferramenta que consome os dados, poderá ou não dar suporte ao consumo de várias partições. Quatro partições é razoável para começar se você não tiver certeza se você não tem certeza sobre o número de partições a definir.
* Você define a retenção de mensagens no seu hub de eventos para pelo menos 7 dias. Se a sua ferramenta de consumo cair por mais de um dia, isso garante que a ferramenta possa continuar de onde parou para eventos com até 7 dias de idade.
* Você deve usar o grupo de consumidores padrão para o seu hub de eventos. Não é necessário criar outros grupos de consumidores ou utilizar um grupo de consumidores separado, exceto se você planejar ter duas ferramentas diferentes que consumam os mesmos dados do mesmo hub de eventos.
* Para o registro de atividades do Azure, você escolhe um namespace do Event Hubs e o Azure Monitor cria um hub de eventos dentro desse namespace chamado _insights-logs-operational-logs_. Para outros tipos de log, você pode escolher um hub de eventos existente ou fazer com que o Azure Monitor crie um hub de eventos por categoria de log.
* As portas de saída 5671 e 5672 devem ser normalmente abertas no computador ou no VNET consumindo dados do hub de eventos.

## <a name="monitoring-data-available"></a>Dados de monitoramento disponíveis
[Fontes de dados de monitoramento do Azure Monitor](data-sources.md) descrevem os diferentes níveis de dados para aplicativos Do Zure e os tipos de dados de monitoramento disponíveis para cada um. A tabela a seguir lista cada um desses níveis e uma descrição de como esses dados podem ser transmitidos para um hub de eventos. Siga os links fornecidos para mais detalhes.

| Camada | Dados | Método |
|:---|:---|:---|
| [Locatário do Azure](data-sources.md#azure-tenant) | Logs de auditoria do Azure Active Directory | Configure uma configuração de diagnóstico de inquilino no seu inquilino AAD. Veja [tutorial: Stream Azure Active Directory registra um hub de eventos do Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) para obter detalhes. |
| [Assinatura do Azure](data-sources.md#azure-subscription) | Log de Atividades do Azure | Crie um perfil de log para exportar eventos do Registro de Atividades para hubs de eventos.  Consulte [os logs da plataforma Stream Azure no Azure Event Hubs](resource-logs-stream-event-hubs.md) para obter detalhes. |
| [Recursos do Azure](data-sources.md#azure-resources) | Métricas da plataforma<br> Logs de recursos |Ambos os tipos de dados são enviados para um hub de eventos usando uma configuração de diagnóstico de recurso. Consulte [os registros de recursos do Stream Azure em um centro de eventos](resource-logs-stream-event-hubs.md) para obter detalhes. |
| [Sistema operacional (convidado)](data-sources.md#operating-system-guest) | Máquinas Virtuais do Azure | Instale a [extensão de diagnósticos do Azure](diagnostics-extension-overview.md) em máquinas virtuais Windows e Linux no Azure. Consulte [os dados do Streaming Azure Diagnostics no caminho quente usando o Event Hubs](diagnostics-extension-stream-event-hubs.md) para obter detalhes sobre vms do Windows e use o Linux Diagnostic Extension para monitorar [métricas e logs](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) para obter detalhes sobre VMs Linux. |
| [Código do aplicativo](data-sources.md#application-code) | Application Insights | O Application Insights não fornece um método direto para transmitir dados para hubs de eventos. Você pode configurar a [exportação contínua](../../azure-monitor/app/export-telemetry.md) dos dados do Application Insights para uma conta de armazenamento e, em seguida, usar um App Lógico para enviar os dados para um hub de eventos, conforme descrito no [Manual de streaming com logic app](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Streaming manual com App Logic
Para dados que você não pode transmitir diretamente para um hub de eventos, você pode escrever para o armazenamento do Azure e, em seguida, usar um aplicativo lógico acionado por tempo que [retira dados do armazenamento blob](../../connectors/connectors-create-api-azureblobstorage.md#add-action) e [os empurra como uma mensagem para o centro de eventos](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Ferramentas parceiras com integração do Azure Monitor

O roteamento de seus dados de monitoramento para um hub de eventos com o Azure Monitor permite que você se integre facilmente com ferramentas externas de SIEM e monitoramento. Exemplos de ferramentas com integração do Azure Monitor incluem:

| Ferramenta | Hospedado no Azure | Descrição |
|:---|:---| :---|
|  IBM QRadar | Não | O Microsoft Azure DSM e protocolo de Hub de Eventos do Microsoft Azure estão disponíveis para download do [site de suporte da IBM](https://www.ibm.com/support). Você pode aprender mais sobre a integração com o Azure na [configuração QRadar DSM](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | Não | [O Azure Monitor Add-On for Splunk](https://splunkbase.splunk.com/app/3534/) é um projeto de código aberto disponível no Splunkbase. A documentação está disponível no [Azure Monitor Addon For Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Se você não puder instalar um complemento na instância splunk, se, por exemplo, estiver usando um proxy ou executando na Splunk Cloud, você poderá encaminhar esses eventos para o Coletor de Eventos Splunk HTTP usando [a função Azure For Splunk,](https://github.com/Microsoft/AzureFunctionforSplunkVS)que é acionada por novas mensagens no hub de eventos. |
| SumoLogic | Não | Instruções para configurar o SumoLogic para consumir dados de um hub de eventos estão disponíveis no [Collect Logs para o Aplicativo de Auditoria Azure do Event Hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Não | O conector inteligente ArcSight Azure Event Hub está disponível como parte da coleção de [conectores inteligentes ArcSight.](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) |
| Servidor syslog | Não | Se você quiser transmitir dados do Azure Monitor diretamente para um servidor syslog, você pode usar uma [solução baseada em uma função Azure](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | Não| Instruções para configurar o LogRhythm para coletar logs de um hub de eventos estão disponíveis [aqui](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Sim | Para obter mais informações, consulte [Começar a monitorar e registrar usando Logz.io para aplicativos Java em execução no Azure](https://docs.microsoft.com/azure/developer/java/fundamentals/java-get-started-with-logzio)


## <a name="next-steps"></a>Próximas etapas
* [Arquive o registro atividade em uma conta de armazenamento](../../azure-monitor/platform/archive-activity-log.md)
* [Leia a visão geral do registro de atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Configure um alerta com base em um evento de registro de atividades](../../azure-monitor/platform/alerts-log-webhook.md)


