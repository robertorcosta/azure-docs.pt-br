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
ms.openlocfilehash: f7a1eccd76313c5b3bc74a5b5ebdbcd202ca6841
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435760"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança na Central de Segurança do Azure
Este documento ajuda você a gerenciar soluções de segurança já conectadas à Central de Segurança do Azure e a adicionar novas.

> [!NOTE]
> Um subconjunto de soluções de segurança foi aposentado em 31 de julho de 2019. Para obter mais informações e serviços alternativos, consulte [os recursos do Centro de Aposentadoria da Segurança (julho de 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança integradas do Azure
A Central de Segurança facilita a criação de soluções de segurança integradas no Azure. Os benefícios incluem:

- **Implantação simplificada**: a Central de Segurança oferece provisionamento simplificado das soluções integradas de parceiros. Para soluções como antimalware e avaliação de vulnerabilidades, o Security Center pode provisionar o agente em suas máquinas virtuais. Para dispositivos de firewall, o Security Center pode cuidar de grande parte da configuração de rede necessária.
- **Detecções integradas**: Os eventos de segurança das soluções de parceiros são coletados, agregados e exibidos automaticamente como parte dos alertas e incidentes do Security Center. Esses eventos também são combinados com detecções de outras fontes para fornecer funcionalidades de detecção avançada de ameaças.
- **Unificação de gerenciamento e monitoramento de integridade**: os clientes podem usar eventos de integridade integrados para monitorar todas as soluções de parceiro em um relance. O gerenciamento básico está disponível com acesso fácil à configuração avançada usando a solução de parceiro.

Atualmente, as soluções integradas de segurança incluem avaliação de vulnerabilidades por [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e firewall de aplicativos Web do Microsoft Application Gateway.

> [!NOTE]
> O Security Center não instala o agente Log Analytics em dispositivos virtuais parceiros porque a maioria dos fornecedores de segurança proíbe agentes externos que executam em seus aparelhos.
>
>

## <a name="how-security-solutions-are-integrated"></a>Como as soluções de segurança são integradas
As soluções de segurança do Azure implantadas da Central de Segurança serão conectadas automaticamente. Você também pode conectar outras fontes de dados de segurança, incluindo computadores em execução no local ou em outras nuvens.

![Integração de soluções de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerenciar soluções de segurança integrada do Azure e outras fontes de dados

1. Entre no [portal do Azure](https://azure.microsoft.com/features/azure-portal/).

2. No menu **Microsoft Azure**, selecione **Central de Segurança**. **Central de Segurança - Visão geral** é aberto.

3. No menu da Central de Segurança, selecione **Soluções de segurança**.

   ![Visão geral da Central de Segurança](./media/security-center-partner-integration/overview.png)

Em **soluções de Segurança,** você pode ver a saúde das soluções integradas de segurança do Azure e executar tarefas básicas de gerenciamento.

### <a name="connected-solutions"></a>Soluções conectadas

A seção **soluções conectadas** inclui soluções de segurança atualmente conectadas ao Security Center. Também mostra o estado de saúde de cada solução.  

![Soluções conectadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

O status de uma solução de parceiro pode ser:

* Saudável (verde) - sem problemas de saúde.
* Insalubre (vermelho) - há um problema de saúde que requer atenção imediata.
* Problemas de integridade (laranja) - a solução interrompeu o envio de relatórios sobre sua integridade.
* Não informado (cinza) - a solução ainda não informou nada e nenhum dado de saúde está disponível. O status de uma solução pode não ser informado se foi conectado recentemente e ainda está sendo implantado.

> [!NOTE]
> Se os dados de status de integridade não estiverem disponíveis, a Central de Segurança mostrará a data e a hora do último evento recebido para indicar se a solução está reportando ou não. Se não houver dados de saúde disponíveis e nenhum alerta for recebido nos últimos 14 dias, o Security Center indica que a solução é insalubre ou não está relatando.
>
>

1. Selecione **VIEW** para obter informações adicionais e opções como:

   - **Console da solução**. Abre a experiência de gerenciamento para esta solução.
   - **Vincular VM**. Abre a página Aplicativos de Link. Nela, você pode conectar recursos à solução de parceiro.
   - **Excluir solução**.
   - **Configurar**.

   ![Detalhes da solução de parceiro](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Soluções descobertas

O Security Center descobre automaticamente as soluções de segurança em execução no Azure, mas não está conectada ao Security Center e exibe as soluções na seção **Soluções Descobertas.** Essas soluções incluem soluções Azure, como [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), e soluções de parceiros.

> [!NOTE]
> A camada Padrão da Central de Segurança é necessária no nível de assinatura para o recurso de descoberta de soluções. Consulte [Pricing](security-center-pricing.md) para saber mais sobre os níveis de preços.
>
>

Selecione **CONNECT** em uma solução para se integrar ao Security Center e seja notificado sobre alertas de segurança.

### <a name="add-data-sources"></a>Adicionar fontes de dados

A seção **Adicionar fontes de dados** inclui outras fontes de dados disponíveis que podem ser conectadas. Para obter instruções sobre como adicionar dados de qualquer uma dessas fontes, clique em **ADICIONAR**.

![Fontes de dados](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exportar dados para um SIEM

> [!NOTE]
> Para obter detalhes de um método mais simples (atualmente em pré-visualização) para exportar dados para um SIEM, consulte Alertas e recomendações de [segurança de exportação (Preview)](continuous-export.md). O novo método não usa o Activity Log como um intermediário e permite a exportação direta do Security Center para hubs de eventos (e, em seguida, para o seu SIEM), ele também suporta a exportação de Recomendações de Segurança.


Você pode configurar seus SIEMs ou outras ferramentas de monitoramento para receber eventos do Azure Security Center.

Todos os eventos do Azure Security Center são publicados no registro de [atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)do Azure Monitor . O Azure Monitor usa [um pipeline consolidado](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) para transmitir os dados para um Event Hub, onde ele pode ser puxado para dentro de sua ferramenta de monitoramento.

As seções a seguir descrevem como você pode configurar os dados para serem transmitidos para um hub de eventos. As etapas pressupõem que você já tem a Central de Segurança do Azure configurado na sua assinatura do Azure.

### <a name="high-level-overview"></a>Visão geral de alto nível

![Visão geral de alto nível](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quais são os dados de segurança do Azure expostos para SIEM?

Nesta versão, exporemos os [alertas de segurança.](../security-center/security-center-managing-and-responding-alerts.md) Em versões futuras, enriqueceremos o conjunto de dados com as recomendações de segurança.

### <a name="how-to-set-up-the-pipeline"></a>Como configurar o gasoduto

#### <a name="create-an-event-hub"></a>Criar um Hub de Evento

Antes de começar, [crie um namespace do Event Hubs](../event-hubs/event-hubs-create.md) - o destino de todos os seus dados de monitoramento.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmissão do Log de Atividades do Azure para os Hubs de Eventos

Consulte o registro de atividades de fluxo de artigo a seguir [no Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Instalar um conector do SIEM parceiro 

Rotear dados de monitoramento para um Hub de Eventos com o Azure Monitor permite fácil integração com as ferramentas de monitoramento e o SIEM de parceiro.

Veja o artigo a seguir para a lista de [SIEMs suportados](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

### <a name="example-for-querying-data"></a>Exemplo de consulta de dados 

Aqui estão algumas consultas do Splunk que você pode usar para puxar dados de alerta:

| **Descrição da consulta:** | **Consulta** |
|----|----|
| Todos os Alertas| index=main Microsoft.Security/locations/alerts|
| Resumir contagem de operações por seu nome| index=main sourcetype="amal:security" \| tabela operationName \| estatísticas contam por operationName|
| Obter informações de alertas: hora, nome, estado, ID e assinatura | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a integrar as soluções de parceiro à Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte artigo:

* [Monitoramento da integridade de segurança na Central de Segurança](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.