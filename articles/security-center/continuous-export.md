---
title: Exportar alertas e recomendações do Azure Security Center para SIEMs | Microsoft Docs
description: Este artigo explica como configurar a exportação contínua de alertas de segurança e recomendações para SIEMs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158955"
---
# <a name="export-security-alerts-and-recommendations"></a>Exporte alertas e recomendações

O Azure Security Center gera alertas e recomendações detalhadas de segurança. Você pode visualizá-los no portal ou através de ferramentas programáticas. Você também pode precisar exportar essas informações ou enviá-la para outras ferramentas de monitoramento em seu ambiente. 

Este artigo descreve o conjunto de ferramentas que permitem exportar alertas e recomendações manualmente ou de forma contínua e contínua.

Usando essas ferramentas você pode:

* Exporte continuamente para espaços de trabalho do Log Analytics
* Exporte continuamente para o Azure Event Hubs (para integrações com SIEMs de terceiros)
* Exportar para CSV (uma vez)


## <a name="setting-up-a-continuous-export"></a>Configuração de uma exportação contínua

As etapas abaixo são necessárias se você estiver configurando uma exportação contínua para o espaço de trabalho log analytics ou o Azure Event Hubs.

1. Na barra lateral do Security Center, **selecione Configurações de & de preços**.

1. Selecione a assinatura específica para a qual deseja configurar a exportação de dados.
    
1. Na barra lateral da página de configurações dessa assinatura, selecione **Exportação Contínua**.

    [Opções de exportação no Azure Security Center ![](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Aqui você vê as opções de exportação. Há uma guia para cada alvo de exportação disponível. 

1. Selecione o tipo de dados que deseja exportar e escolha entre os filtros de cada tipo (por exemplo, exporte apenas alertas de alta gravidade).

1. Na área "Exportar destino", escolha onde deseja que os dados sejam salvos. Os dados podem ser salvos em um destino em uma assinatura diferente (por exemplo, em uma instância do Central Event Hub ou em um espaço de trabalho central do Log Analytics).

1. Clique em **Salvar**.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Configurando a integração Do SIEM através do Azure Event Hubs

O Azure Event Hubs é uma ótima solução para consumir programaticamente qualquer dado por streaming. Para os alertas e recomendações do Azure Security Center, é a maneira preferida de se integrar a um SIEM de terceiros.

> [!NOTE]
> O método mais eficaz para transmitir dados de monitoramento para ferramentas externas na maioria dos casos é o uso do Azure Event Hubs. [Este artigo](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) fornece uma breve descrição de como você pode transmitir dados de monitoramento de diferentes fontes para um Event Hub e links para orientação detalhada.

> [!NOTE]
> Se você já exportou alertas do Security Center para um SIEM usando o registro de atividade do Azure, o procedimento abaixo substituirá essa metodologia.

Para ver os esquemas de eventos dos tipos de dados exportados, visite os [esquemas de eventos do Event Hub.](https://aka.ms/ASCAutomationSchemas)


### <a name="to-integrate-with-a-siem"></a>Para integrar-se a um SIEM 

Depois de configurar a exportação contínua dos dados do Security Center escolhidos para o Azure Event Hubs, você pode configurar o conector apropriado para o seu SIEM:

* **Azure Sentinel** - Use o [conector](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) de dados nativo do Azure Security Center oferecido lá.
* **Splunk** - Use [o adepto do Monitor Azure para Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** - Use [uma fonte de log configurada manualmente](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – Use [smartconnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Além disso, se você quiser mover os dados exportados continuamente automaticamente do seu Event Hub configurado para o Azure Data Explorer, use as instruções em [Ingest data from Event Hub para o Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Exportação contínua para um espaço de trabalho do Log Analytics

Se você quiser analisar os dados do Azure Security Center dentro de um espaço de trabalho do Log Analytics ou usar alertas do Azure em conjunto com o Security Center, configure a exportação contínua para o espaço de trabalho do Log Analytics.

Para exportar para um espaço de trabalho do Log Analytics, você deve ter as soluções log analytics do Security Center habilitadas em seu espaço de trabalho. Se você estiver usando o portal Azure, a solução de nível gratuito do Security Center será ativada automaticamente quando você habilita rescaldo continuamente. No entanto, se você estiver configurando suas configurações contínuas de exportação de forma programática, você deve selecionar manualmente o nível de preços livre ou padrão para o espaço de trabalho necessário nas **configurações de & de preços**.  

### <a name="log-analytics-tables-and-schemas"></a>Tabelas e esquemas do Log Analytics

Os alertas e recomendações de segurança são armazenados nas tabelas *SecurityAlert* e *SecurityRecommendations,* respectivamente. O nome da solução Log Analytics que contém essas tabelas depende se você está no nível livre ou padrão (veja [preços](security-center-pricing.md)): Segurança ('Segurança e Auditoria') ou SecurityCenterFree.

![A tabela *SecurityAlert* no Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Para ver os esquemas de eventos dos tipos de dados exportados, visite os [esquemas de tabela log analytics](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Veja alertas e recomendações de segurança exportados no Azure Monitor

Em alguns casos, você pode optar por visualizar os alertas de segurança exportados e/ou recomendações no [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

O Azure Monitor oferece uma experiência unificada de alerta para uma variedade de alertas do Azure, incluindo log de diagnóstico, alertas métricos e alertas personalizados com base em consultas de espaço de trabalho do Log Analytics.

Para visualizar alertas e recomendações do Security Center no Azure Monitor, configure uma regra de alerta baseada em consultas de Log Analytics (Log Alert):

1. Na página **Alertas** do Monitor do Azure, clique em **Nova regra de alerta**.

    ![Página de alertas do Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na página criar regra, configure sua nova regra (da mesma forma que você configuraria uma regra de [alerta de log no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * Em **For Resource,** selecione o espaço de trabalho Log Analytics para o qual você exportou alertas e recomendações de segurança.

    * Para **Condição,** selecione **Pesquisa de log personalizado**. Na página que aparece, configure a consulta, o período de retorno e o período de freqüência. Na consulta de pesquisa, você pode digitar *SecurityAlert* ou *SecurityRecommendation* para consultar os tipos de dados para os os que o Security Center exporta continuamente à medida que você habilita o recurso Exportação Contínua para O Log Analytics. 
    
    * Opcionalmente, configure o [Grupo de Ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) que você gostaria de acionar. Grupos de ação podem ativar o envio de e-mails, bilhetes ITSM, WebHooks e muito mais.
    ![Regra de alerta do Monitor do Azure](./media/continuous-export/azure-monitor-alert-rule.png)

Agora você verá novos alertas ou recomendações do Azure Security Center (dependendo da sua configuração) nos alertas do Azure Monitor, com acionamento automático de um grupo de ação (se fornecido).

## <a name="manual-one-time-export-of-security-alerts"></a>Exportação única manual de alertas de segurança

Para baixar um relatório csv para alertas ou recomendações, abra a página **alertas** de segurança ou **recomendações** e clique no botão **Desbaixar relatório CSV.**

[![Baixar dados de alertas como um arquivo CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Esses relatórios contêm alertas e recomendações de recursos das assinaturas atualmente selecionadas.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como configurar exportações contínuas de suas recomendações e alertas. Você também aprendeu como baixar seus dados de alerta como um arquivo CSV. 

Para material relacionado, consulte a seguinte documentação: 

- [Documentação do Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)
- [Documentação do Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentação do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Automação de fluxo de trabalho e esquemas de dados de exportação contínua](https://aka.ms/ASCAutomationSchemas)
