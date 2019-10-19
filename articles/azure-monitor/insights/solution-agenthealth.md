---
title: Integridade do Agente solução no Azure Monitor | Microsoft Docs
description: Este artigo destina-se a ajudá-lo a entender como usar essa solução para monitorar a integridade dos seus agentes relatando diretamente para Log Analytics ou System Center Operations Manager.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 03/19/2017
ms.openlocfilehash: 5a48bbff89f0d6a0be9adf2ad242dbca41eec6db
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555328"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Integridade do Agente solução no Azure Monitor
A solução Integridade do Agente no Azure ajuda você a entender, para todos os agentes que se reportam diretamente ao espaço de trabalho Log Analytics no Azure Monitor ou um grupo de gerenciamento System Center Operations Manager conectado a Azure Monitor, que não respondem e enviando dados operacionais.  Você também pode controlar Quantos agentes são implantados, onde eles são distribuídos geograficamente e executar outras consultas para manter o reconhecimento da distribuição de agentes implantados no Azure, em outros ambientes de nuvem ou localmente.    

## <a name="prerequisites"></a>Pré-requisitos
Antes de implantar essa solução, confirme se você tem suporte para os [agentes do Windows](../../log-analytics/log-analytics-windows-agent.md) que se reportam ao espaço de trabalho log Analytics ou relatórios para um grupo de [Gerenciamento de Operations Manager](../../azure-monitor/platform/om-agents.md) integrado ao seu espaço de trabalho.

## <a name="solution-components"></a>Componentes da solução
Essa solução consiste nos seguintes recursos que são adicionados ao seu espaço de trabalho e agentes conectados diretamente ou Operations Manager grupo de gerenciamento conectado.

### <a name="management-packs"></a>Pacotes de gerenciamento
Se o grupo de gerenciamento do System Center Operations Manager estiver conectado a um espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no Operations Manager.  Esses pacotes de gerenciamento também são instalados em computadores com Windows conectados diretamente após a adição dessa solução. Não há nada para configurar ou gerenciar com esses pacotes de gerenciamento.

* Pacote de inteligência de canal do Microsoft System Center Advisor HealthAssessment Direct (Microsoft. IntelligencePacks. HealthAssessmentDirect)
* Pacote de inteligência do Microsoft System Center Advisor HealthAssessment Server Channel (Microsoft. IntelligencePacks. HealthAssessmentViaServer).  

Para obter mais informações sobre como os pacotes de gerenciamento da solução são atualizados, veja [Conectar o Operations Manager ao Log Analytics](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Configuração
Adicione a solução Integridade do Agente ao seu espaço de trabalho do Log Analytics usando o processo descrito em [Adicionar soluções](solutions.md). Não há nenhuma configuração adicional necessária.


## <a name="data-collection"></a>Coleta de dados
### <a name="supported-agents"></a>Agentes com suporte
A tabela a seguir descreve as fontes conectadas que têm suporte nesta solução.

| Fonte conectada | Com suporte | Descrição |
| --- | --- | --- |
| Agentes do Windows | Sim | Os eventos de pulsação são coletados de agentes diretos do Windows.|
| System Center Operations Manager grupo de gerenciamento | Sim | Os eventos de pulsação são coletados dos agentes subordinados ao grupo de gerenciamento a cada 60 segundos e, em seguida, encaminhados para o Azure Monitor. Uma conexão direta de agentes de Operations Manager para Azure Monitor não é necessária. Os dados do evento de pulsação são encaminhados do grupo de gerenciamento para o espaço de trabalho Log Analytics.|

## <a name="using-the-solution"></a>Usando a solução
Quando você adicionar a solução ao seu espaço de trabalho do Log Analytics, o bloco **integridade do agente** será adicionado ao seu painel. Esse bloco mostra o número total de agentes e o número de agentes sem resposta nas últimas 24 horas.<br><br> bloco de solução de integridade ![Agent no painel ](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Clique no bloco **integridade do agente** para abrir o painel de **integridade do agente** .  O painel inclui as colunas na tabela a seguir. Cada coluna lista os dez principais eventos por contagem que correspondem aos critérios da coluna para o intervalo de tempo especificado. Você pode executar uma pesquisa de logs que fornece a lista inteira selecionando **Ver todos** na parte inferior direita de cada coluna ou clicando no cabeçalho da coluna.

| Column | Descrição |
|--------|-------------|
| Contagem de agentes ao longo do tempo | Uma tendência de sua contagem de agentes durante um período de sete dias para agentes do Linux e do Windows.|
| Contagem de agentes sem resposta | Uma lista de agentes que não enviaram uma pulsação nas últimas 24 horas.|
| Distribuição por tipo de so | Uma partição de quantos agentes do Windows e do Linux você tem em seu ambiente.|
| Distribuição por versão do agente | Uma partição das diferentes versões de agente instaladas em seu ambiente e uma contagem de cada uma delas.|
| Distribuição por categoria de agente | Uma partição das diferentes categorias de agentes que estão enviando eventos de pulsação: agentes diretos, agentes do OpsMgr ou o servidor de gerenciamento do OpsMgr.|
| Distribuição por grupo de gerenciamento | Uma partição dos diferentes grupos de gerenciamento de Operations Manager em seu ambiente.|
| Localização geográfica de agentes | Uma partição dos diferentes países/regiões em que você tem agentes e uma contagem total do número de agentes que foram instalados em cada país/região.|
| Contagem de gateways instalados | O número de servidores que têm o gateway de Log Analytics instalado e uma lista desses servidores.|

![Exemplo de painel de solução Integridade do Agente](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Monitor registros de log
A solução cria um tipo de registro no espaço de trabalho Log Analytics.  

### <a name="heartbeat-records"></a>Registros de pulsação
Um registro com um tipo de **pulsação** é criado.  Esses registros têm as propriedades descritas na tabela a seguir.  

| Propriedade | Descrição |
| --- | --- |
| `Type` | *Batida*|
| `Category` | O valor é *agente direto*, *agente do SCOM*ou servidor de *Gerenciamento do SCOM*.|
| `Computer` | Nome do computador.|
| `OSType` | Sistema operacional Windows ou Linux.|
| `OSMajorVersion` | Versão principal do sistema operacional.|
| `OSMinorVersion` | Versão secundária do sistema operacional.|
| `Version` | Agente de Log Analytics ou versão do agente de Operations Manager.|
| `SCAgentChannel` | O valor é *direto* e/ou *SCManagementServer*.|
| `IsGatewayInstalled` | Se Log Analytics Gateway estiver instalado, o valor será *true*, caso contrário, o valor será *false*.|
| `ComputerIP` | Endereço IP do computador.|
| `RemoteIPCountry` | Localização geográfica onde o computador é implantado.|
| `ManagementGroupName` | Nome do grupo de gerenciamento de Operations Manager.|
| `SourceComputerId` | ID exclusiva do computador.|
| `RemoteIPLongitude` | Longitude da localização geográfica do computador.|
| `RemoteIPLatitude` | Latitude da localização geográfica do computador.|

Cada agente que se reporta a um servidor de gerenciamento de Operations Manager enviará duas pulsações, e o valor da propriedade SCAgentChannel incluirá **Direct** e **SCManagementServer** dependendo de quais fontes de dados e soluções de monitoramento você tem habilitado em sua assinatura. Se você se lembrar, os dados das soluções serão enviados diretamente de um servidor de gerenciamento de Operations Manager para Azure Monitor, ou devido ao volume de dados coletados no agente, serão enviados diretamente do agente para Azure Monitor. Para eventos de pulsação que têm o valor **SCManagementServer**, o valor de ComputerIP é o endereço IP do servidor de gerenciamento, já que os dados são realmente carregados por ele.  Para pulsações em que SCAgentChannel é definido como **Direct**, é o endereço IP público do agente.  

## <a name="sample-log-searches"></a>Pesquisas de log de exemplo
A tabela a seguir fornece pesquisas de log de exemplo para os registros coletados por essa solução.

| Consulta | Descrição |
|:---|:---|
| Computador &#124; de pulsação distinta |Número total de agentes |
| Pulsação &#124; resume LastCall = Max (TimeGenerated) por computador &#124; em que o LastCall < atrás (24h) |Contagem de agentes sem resposta nas últimas 24 horas |
| Pulsação &#124; resume LastCall = Max (TimeGenerated) por computador &#124; em que o LastCall < atrás (15 m) |Contagem de agentes sem resposta nos últimos 15 minutos |
| Pulsação &#124; em que o timegenerated > atrás (24h) e o computador &#124; em (((pulsação em que &#124; o TimeGenerated > &#124; atrás (24h) computador distinto)) resume LastCall = Max (TimeGenerated) por computador |Computadores online (nas últimas 24 horas) |
| Pulsação &#124; em que o timegenerated > atrás (24h) e computador! em &#124; ((a pulsação em que o &#124; TimeGenerated > atrás &#124; (30 m) computador distinto)) resume LastCall = Max (TimeGenerated) pelo computador |Total de agentes offline nos últimos 30 minutos (para as últimas 24 horas) |
| Pulsação &#124; resume AggregatedValue = DContar (computador) por OSType |Obter uma tendência do número de agentes ao longo do tempo por OSType|
| Pulsação &#124; resume AggregatedValue = DContar (computador) por OSType |Distribuição por tipo de so |
| Pulsação &#124; resume AggregatedValue = DContar (computador) por versão |Distribuição por versão do agente |
| Pulsação &#124; resume AggregatedValue = Count () por categoria |Distribuição por categoria de agente |
| Pulsação &#124; resume AggregatedValue = DContar (computador) por ManagementGroupName | Distribuição por grupo de gerenciamento |
| Pulsação &#124; resume AggregatedValue = DContar (computador) por RemoteIPCountry |Localização geográfica de agentes |
| Pulsação &#124; em que IFF (IsNotNull (Toint (IsGatewayInstalled)), IsGatewayInstalled = = true, IsGatewayInstalled = = "true") = &#124; = computador verdadeiro distinto |Número de gateways de Log Analytics instalados |




## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [alertas no Azure monitor](../platform/alerts-overview.md) para obter detalhes sobre como gerar alertas de consultas de log. 
