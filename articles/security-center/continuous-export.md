---
title: Exportar alertas e recomendações da central de segurança do Azure para SIEMs | Microsoft Docs
description: Este artigo explica como configurar a exportação contínua de alertas de segurança e recomendações para os SIEMs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 4d5cff416c1ac54e54d06e8def121db65bb7d191
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89433918"
---
# <a name="export-security-alerts-and-recommendations"></a>Exporte alertas e recomendações

A central de segurança do Azure gera recomendações e alertas de segurança detalhados. Você pode exibi-los no portal ou por meio de ferramentas programáticas. Talvez você também precise exportar essas informações ou enviá-las para outras ferramentas de monitoramento em seu ambiente. 

Este artigo descreve o conjunto de ferramentas que permitem que você exporte alertas e recomendações manualmente ou de modo contínuo e contínuo.

Usando essas ferramentas, você pode:

* Exportar continuamente para Log Analytics espaços de trabalho
* Exportar continuamente para os hubs de eventos do Azure (para integrações com SIEMs de terceiros)
* Exportar para CSV (uma vez)



## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Disponível|
|Refere|Camada gratuita|
|Funções e permissões necessárias:|**Função de administrador de segurança** no grupo de recursos (ou **proprietário**)<br>Também deve ter permissões de gravação para o recurso de destino|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Sim](./media/icons/yes-icon.png) China gov (para o Hub de eventos), outros gov|
|||



## <a name="set-up-a-continuous-export"></a>Configurar uma exportação contínua

As etapas a seguir são necessárias se você estiver configurando uma exportação contínua para Log Analytics espaço de trabalho ou hubs de eventos do Azure.

1. Na barra lateral da central de segurança, selecione **preços & configurações**.

1. Selecione a assinatura específica para a qual você deseja configurar a exportação de dados.
    
1. Na barra lateral da página de configurações dessa assinatura, selecione **exportação contínua**.

    [ ![ Opções de exportação na central de segurança do Azure](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) aqui você vê as opções de exportação. Há uma guia para cada destino de exportação disponível. 

1. Selecione o tipo de dados que você deseja exportar e escolha um dos filtros em cada tipo (por exemplo, exportar somente alertas de severidade alta).

1. Opcionalmente, se sua seleção incluir uma dessas quatro recomendações, você poderá incluir as descobertas de avaliação de vulnerabilidade junto com elas:

    - As descobertas de avaliação de vulnerabilidade em seus bancos de dados SQL devem ser corrigidas
    - As descobertas de avaliação de vulnerabilidade em seus SQL Servers em computadores devem ser corrigidas (visualização)
    - As vulnerabilidades nas imagens do Registro de Contêiner do Azure devem ser corrigidas (da plataforma Qualys)
    - Vulnerabilidades em suas máquinas virtuais devem ser corrigidas

    Para incluir as conclusões com essas recomendações, habilite a opção **incluir conclusões de segurança** .

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Incluir a alternância de conclusões de segurança na configuração de exportação contínua" :::


1. Na área "destino de exportação", escolha onde você deseja que os dados sejam salvos. Os dados podem ser salvos em um destino em uma assinatura diferente (por exemplo, em uma instância central de Hub de eventos ou em um espaço de trabalho central Log Analytics).

1. Clique em **Salvar**.


## <a name="set-up-continuous-export-via-the-rest-api"></a>Configurar a exportação contínua por meio da API REST

O recurso de exportação contínua pode ser configurado e gerenciado por meio da [API de automação](https://docs.microsoft.com/rest/api/securitycenter/automations)da central de segurança do Azure. Use esta API para criar ou atualizar automaçãos para exportar para qualquer um dos seguintes destinos possíveis:

- Hub de Eventos do Azure
- Workspace do Log Analytics
- Aplicativos Lógicos do Azure 

A API fornece funcionalidade adicional não disponível no portal do Azure, por exemplo:

* **Volume maior** -a API permite que você crie várias configurações de exportação em uma única assinatura. A página de **exportação contínua** na interface do usuário do portal da central de segurança dá suporte a apenas uma configuração de exportação por assinatura.

* **Recursos adicionais** – a API oferece parâmetros adicionais que não são mostrados na interface do usuário. Por exemplo, você pode adicionar marcas ao recurso de automação, bem como definir a exportação com base em um conjunto mais amplo de propriedades de alerta e recomendação do que aquelas oferecidas na página de **exportação contínua** na interface do usuário do portal da central de segurança.

* **Escopo mais focalizado** -a API fornece um nível mais granular para o escopo de suas configurações de exportação. Ao definir uma exportação com a API, você pode fazer isso no nível do grupo de recursos. Se você estiver usando a página **exportação contínua** na interface do usuário do portal da central de segurança, precisará defini-la no nível da assinatura.

    > [!TIP]
    > Se você tiver configurado várias configurações de exportação usando a API ou se tiver usado parâmetros somente de API, esses recursos adicionais não serão exibidos na interface do usuário da central de segurança. Em vez disso, haverá uma faixa informando que existem outras configurações.

Saiba mais sobre a API automations na [documentação da API REST](https://docs.microsoft.com/rest/api/securitycenter/automations).



## <a name="configure-siem-integration-via-azure-event-hubs"></a>Configurar a integração do SIEM por meio dos hubs de eventos do Azure

Os hubs de eventos do Azure são uma ótima solução para consumir programaticamente quaisquer dados de streaming. Para alertas e recomendações da central de segurança do Azure, é a maneira preferida de integrar-se a um SIEM de terceiros.

> [!NOTE]
> O método mais eficaz para transmitir dados de monitoramento para ferramentas externas na maioria dos casos é usar os hubs de eventos do Azure. [Este artigo](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) fornece uma breve descrição de como você pode transmitir dados de monitoramento de fontes diferentes para um hub de eventos e links para orientações detalhadas.

> [!NOTE]
> Se você exportou anteriormente alertas da central de segurança para um SIEM usando o log de atividades do Azure, o procedimento a seguir substituirá essa metodologia.

Para exibir os esquemas de eventos dos tipos de dados exportados, visite os [esquemas de eventos do hub de eventos](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Para integrar com um SIEM 

Depois de configurar a exportação contínua dos dados escolhidos da central de segurança para os hubs de eventos do Azure, você pode configurar o conector apropriado para o SIEM:

* **Azure Sentinel** -use o [conector de dados](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) de alertas nativos da central de segurança do Azure oferecido lá.
* **Splunk** -use o [complemento Azure monitor para Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** -usar [uma fonte de log configurada manualmente](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** – usar [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

Além disso, se você quiser mover os dados exportados continuamente automaticamente do hub de eventos configurado para o Azure Data Explorer, use as instruções em [ingerir dados do hub de eventos para o data Explorer do Azure](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub).



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Exportação contínua para um espaço de trabalho Log Analytics

Se você quiser analisar os dados da central de segurança do Azure dentro de um espaço de trabalho Log Analytics ou usar alertas do Azure junto com a central de segurança, configure a exportação contínua para seu espaço de trabalho Log Analytics.

Para exportar para um espaço de trabalho do Log Analytics, você deve ter as soluções de Log Analytics da central de segurança habilitadas no seu espaço de trabalho. Se você estiver usando a portal do Azure, a solução de camada gratuita da central de segurança será habilitada automaticamente quando você habilitar a exportação contínua. No entanto, se você estiver configurando suas configurações de exportação contínua de forma programática, deverá selecionar manualmente o tipo de preço gratuito ou Standard para o espaço de trabalho necessário em **configurações de preço &**.  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabelas e esquemas

Alertas de segurança e recomendações são armazenados nas tabelas *SecurityAlert* e *SecurityRecommendations* , respectivamente. O nome da solução de Log Analytics que contém essas tabelas depende se você está na camada gratuita ou Standard (consulte [preços](security-center-pricing.md)): segurança (' segurança e auditoria ') ou SecurityCenterFree.

![A tabela * SecurityAlert * no Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Para exibir os esquemas de eventos dos tipos de dados exportados, visite os [esquemas de tabela de log Analytics](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Exibir alertas de segurança e recomendações exportadas no Azure Monitor

Em alguns casos, você pode optar por exibir os alertas de segurança exportados e/ou as recomendações em [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

O Azure Monitor fornece uma experiência de alerta unificada para uma variedade de alertas do Azure, incluindo log de diagnóstico, alertas de métrica e alertas personalizados com base em consultas de espaço de trabalho Log Analytics.

Para exibir alertas e recomendações da central de segurança no Azure Monitor, configure uma regra de alerta com base em consultas Log Analytics (alerta de log):

1. Na página **alertas** do Azure monitor, selecione **nova regra de alerta**.

    ![Página de alertas do Azure Monitor](./media/continuous-export/azure-monitor-alerts.png)

1. Na página Criar regra, configure sua nova regra (da mesma maneira que você configurou uma [regra de alerta de log em Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * Para **recurso**, selecione o espaço de trabalho log Analytics para o qual você exportou alertas de segurança e recomendações.

    * Para **condição**, selecione **pesquisa de logs personalizada**. Na página que aparece, configure a consulta, o período de lookback e o período de frequência. Na consulta de pesquisa, você pode digitar *SecurityAlert* ou *SecurityRecommendation* para consultar os tipos de dados que a central de segurança exporta continuamente para quando você habilita a exportação contínua para log Analytics recurso. 
    
    * Opcionalmente, configure o [grupo de ações](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) que você gostaria de disparar. Os grupos de ações podem disparar envio de email, tíquetes de ITSM, WebHooks e muito mais.
    ![Azure Monitor regra de alerta](./media/continuous-export/azure-monitor-alert-rule.png)

Agora, você verá novos alertas ou recomendações da central de segurança do Azure (dependendo da sua configuração) em alertas de Azure Monitor, com o disparo automático de um grupo de ação (se fornecido).

## <a name="manual-one-time-export-of-security-alerts"></a>Exportação única por vez manual de alertas de segurança

Para baixar um relatório CSV para alertas ou recomendações, abra a página **alertas de segurança** ou **recomendações** e selecione o botão **baixar relatório CSV** .

[![Baixar dados de alertas como um arquivo CSV](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Esses relatórios contêm alertas e recomendações para recursos das assinaturas atualmente selecionadas.



## <a name="faq---continuous-export"></a>Perguntas frequentes-exportação contínua

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Quais são os custos envolvidos na exportação de dados?

Não há nenhum custo para habilitar uma exportação contínua. Os custos podem ser incorridos para ingestão e retenção de dados em seu espaço de trabalho Log Analytics, dependendo de sua configuração. 

Saiba mais sobre os [preços do espaço de trabalho do log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Saiba mais sobre os [preços do hub de eventos do Azure](https://azure.microsoft.com/pricing/details/event-hubs/).


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar as exportações contínuas de suas recomendações e alertas. Você também aprendeu a baixar seus dados de alertas como um arquivo CSV. 

Para obter material relacionado, consulte a seguinte documentação: 

- [Documentação dos Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/)
- [Documentação do Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Documentação do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- [Esquemas de automação de fluxo de trabalho e tipos de dados de exportação contínua](https://aka.ms/ASCAutomationSchemas)
