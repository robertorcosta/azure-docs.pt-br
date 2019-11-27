---
title: Integrar soluções de segurança na Central de Segurança do Azure | Microsoft Docs
description: Saiba como a Central de Segurança do Azure se integra aos parceiros para aprimorar a segurança geral dos recursos do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 0479918ad2afa99dcd9f18d3ba81f94a3e262e9e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479377"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança na Central de Segurança do Azure
Este documento ajuda você a gerenciar soluções de segurança já conectadas à Central de Segurança do Azure e a adicionar novas.

> [!NOTE]
> Um subconjunto de soluções de segurança foi desativado em 31 de julho de 2019. Para obter mais informações e serviços alternativos, consulte [aposentadoria dos recursos da central de segurança (julho de 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança integradas do Azure
A Central de Segurança facilita a criação de soluções de segurança integradas no Azure. Os benefícios incluem:

- **Implantação simplificada**: a Central de Segurança oferece provisionamento simplificado das soluções integradas de parceiros. Para soluções como antimalware e avaliação de vulnerabilidade, a central de segurança pode provisionar o agente em suas máquinas virtuais. Para dispositivos de firewall, a central de segurança pode cuidar de grande parte da configuração de rede necessária.
- **Detecções Integradas**: os eventos de segurança das soluções de parceiro são automaticamente coletados, agregados e exibidos como parte dos incidentes e alertas da Central de Segurança. Esses eventos também são combinados com detecções de outras fontes para fornecer funcionalidades de detecção avançada de ameaças.
- **Unificação de gerenciamento e monitoramento de integridade**: os clientes podem usar eventos de integridade integrados para monitorar todas as soluções de parceiro em um relance. O gerenciamento básico está disponível com acesso fácil à configuração avançada usando a solução de parceiro.

Atualmente, as soluções de segurança integradas incluem a avaliação de vulnerabilidades de [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e o Firewall do aplicativo Web do Microsoft Application Gateway.

> [!NOTE]
> A central de segurança não instala o Microsoft Monitoring Agent em soluções de virtualização de parceiros, pois a maioria dos fornecedores de segurança proíbe agentes externos em execução em seus dispositivos.
>
>

## <a name="how-security-solutions-are-integrated"></a>Como as soluções de segurança são integradas
As soluções de segurança do Azure implantadas da Central de Segurança serão conectadas automaticamente. Você também pode conectar outras fontes de dados de segurança, incluindo computadores executados localmente ou em outras nuvens.

![Integração de soluções de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerenciar soluções de segurança integrada do Azure e outras fontes de dados

1. Entre no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).

2. No menu **Microsoft Azure**, selecione **Central de Segurança**. A **Central de Segurança – Visão geral** é aberta.

3. No menu da Central de Segurança, selecione **Soluções de segurança**.

   ![Visão geral da Central de Segurança](./media/security-center-partner-integration/overview.png)

Em **soluções de segurança**, você pode ver a integridade das soluções de segurança integradas do Azure e executar tarefas básicas de gerenciamento.

### <a name="connected-solutions"></a>Soluções conectadas

A seção **soluções conectadas** inclui soluções de segurança que estão atualmente conectadas à central de segurança. Ele também mostra o status de integridade de cada solução.  

![Soluções conectadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

O status de uma solução de parceiro pode ser:

* Íntegro (verde)-sem problemas de integridade.
* Não íntegro (vermelho) – há um problema de integridade que requer atenção imediata.
* Problemas de integridade (laranja) - a solução interrompeu o envio de relatórios sobre sua integridade.
* Não relatado (cinza)-a solução ainda não relatou nada e nenhum dado de integridade está disponível. O status de uma solução poderá não ser relatado se ele tiver sido conectado recentemente e ainda estiver sendo implantado.

> [!NOTE]
> Se os dados de status de integridade não estiverem disponíveis, a Central de Segurança mostrará a data e a hora do último evento recebido para indicar se a solução está reportando ou não. Se nenhum dado de integridade estiver disponível e nenhum alerta for recebido nos últimos 14 dias, a central de segurança indicará que a solução não está íntegra ou não está relatando.
>
>

1. Selecione **Exibir** para obter informações adicionais e opções, como:

   - **Console da solução**. Abre a experiência de gerenciamento para esta solução.
   - **Vincular VM**. Abre a página vincular aplicativos. Nela, você pode conectar recursos à solução de parceiro.
   - **Excluir solução**.
   - **Configurar**.

   ![Detalhes da solução de parceiro](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Soluções descobertas

A central de segurança descobre automaticamente as soluções de segurança em execução no Azure, mas não está conectada à central de segurança e exibe as soluções na seção **soluções descobertas** . Essas soluções incluem soluções do Azure, como [Azure ad Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)e soluções de parceiros.

> [!NOTE]
> A camada Padrão da Central de Segurança é necessária no nível de assinatura para o recurso de descoberta de soluções. Consulte [preços](security-center-pricing.md) para saber mais sobre os tipos de preço.
>
>

Selecione **conectar** em uma solução para integrar com a central de segurança e ser notificado sobre alertas de segurança.

### <a name="add-data-sources"></a>Adicionar fontes de dados

A seção **Adicionar fontes de dados** inclui outras fontes de dados disponíveis que podem ser conectadas. Para obter instruções sobre como adicionar dados de qualquer uma dessas fontes, clique em **ADICIONAR**.

![Fontes de dados](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exportar dados para um SIEM

> [!NOTE]
> Para obter detalhes de um método mais simples (atualmente em visualização) para exportar dados para um SIEM, consulte [Exportar alertas de segurança e recomendações (versão prévia)](continuous-export.md). O novo método não usa o log de atividades como um intermediador e permite a exportação direta da central de segurança para os hubs de eventos (e, em seguida, para o SIEM), também dá suporte à exportação de recomendações de segurança.


Você pode configurar seus SIEMs ou outras ferramentas de monitoramento para receber eventos da central de segurança do Azure.

Todos os eventos da central de segurança do Azure são publicados no [log de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)do Azure Azure monitor. O Azure Monitor usa [um pipeline consolidado](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) para transmitir os dados para um hub de eventos, no qual ele pode ser obtido em sua ferramenta de monitoramento.

As seções a seguir descrevem como você pode configurar os dados para serem transmitidos para um hub de eventos. As etapas pressupõem que você já tem a Central de Segurança do Azure configurado na sua assinatura do Azure.

### <a name="high-level-overview"></a>Visão geral de alto nível

![Visão geral de alto nível](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quais são os dados de segurança do Azure expostos para SIEM?

Nesta versão, expõemos os [alertas de segurança.](../security-center/security-center-managing-and-responding-alerts.md) Em versões futuras, enriqueceremos o conjunto de dados com as recomendações de segurança.

### <a name="how-to-set-up-the-pipeline"></a>Como configurar o pipeline

#### <a name="create-an-event-hub"></a>Criar um Hub de Evento

Antes de começar, [crie um namespace de hubs de eventos](../event-hubs/event-hubs-create.md) -o destino de todos os seus dados de monitoramento.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmissão do Log de Atividades do Azure para os Hubs de Eventos

Consulte o artigo [fluxo de atividades do Stream a seguir para os hubs de eventos](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Instalar um conector do SIEM parceiro 

Rotear dados de monitoramento para um Hub de Eventos com o Azure Monitor permite fácil integração com as ferramentas de monitoramento e o SIEM de parceiro.

Consulte o seguinte artigo para obter a lista de [Siems com suporte](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>Exemplo de consulta de dados 

Aqui estão algumas consultas Splunk que você pode usar para efetuar pull de dados de alerta:

| **Descrição da consulta:** | **Consulta** |
|----|----|
| Todos os Alertas| index=main Microsoft.Security/locations/alerts|
| Resumir contagem de operações por seu nome| index=main sourcetype="amal:security" \| tabela operationName \| estatísticas contam por operationName|
| Obter informações de alertas: hora, nome, estado, ID e assinatura | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a integrar as soluções de parceiro à Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Monitoramento da integridade de segurança na Central de Segurança](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Obtenha respostas para perguntas frequentes sobre como usar a Central de Segurança.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
