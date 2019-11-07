---
title: Perguntas Frequentes do Log Analytics | Microsoft Docs
description: Respostas para perguntas frequentes sobre o serviço de análise de logs de Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/01/2019
ms.openlocfilehash: 9eb921fc8ea19486db0fc3311764931f09e11464
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579302"
---
# <a name="log-analytics-faq"></a>Perguntas frequentes do Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Esta FAQ da Microsoft é uma lista de perguntas frequentes sobre o Azure Monitor Log Analytics espaço de trabalho. Se você tiver alguma pergunta adicional sobre o Log Analytics, vá para o [fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e poste suas perguntas. Quando uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.


## <a name="new-logs-experience"></a>Nova experiência de Logs

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>P: Qual é a diferença entre a nova experiência de Logs e o Log Analytics?

R: Elas são iguais. [O Log Analytics está sendo integrado como um recurso no Azure Monitor](../../azure-monitor/azure-monitor-rebrand.md) a fim de fornecer uma experiência de monitoramento mais unificada. A nova experiência de Logs no Azure Monitor é exatamente igual às consultas do Log Analytics que muitos clientes já estão usando.

### <a name="q-can-i-still-use-log-search"></a>P: Ainda posso usar a Pesquisa de Log? 

R: A Pesquisa de Log ainda está disponível no portal do OMS e no portal do Azure sob o nome **Logs (clássico)** . O portal do OMS será oficialmente desativado em 15 de janeiro de 2019. A experiência clássica de Logs no portal do Azure será desativada gradualmente e substituída pela nova experiência de Logs. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>P. Ainda posso usar o portal avançado do Analytics? 
A nova experiência de logs no portal do Azure é baseada no Advanced Analytics Portal, mas ainda pode ser acessada fora do portal do Azure. O roteiro para desativar esse portal externo será anunciado em breve.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>P. Por que não consigo ver os botões Gerenciador de Consultas e Salvar na nova experiência de Logs?

Os botões **Gerenciador de Consulta**, **Salvar** e **Definir Alerta** não estão disponíveis ao explorar Logs no contexto de um recurso específico. Para criar alertas, salvar ou carregar uma consulta, os Logs devem receber o escopo de um workspace. Para abrir os Logs no contexto do workspace, selecione **Todos os serviços** > **Monitor** > **Logs**. O último workspace usado é selecionado, mas você pode selecionar qualquer outro workspace. Confira [Exibir e analisar dados no Log Analytics](../log-query/portals.md) para saber mais.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>P. Como faço para extrair Campos Personalizados na nova experiência de Logs? 

R: No momento, há suporte para a extração de Campos Personalizados na experiência clássica de Logs. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>P. Onde posso encontrar a exibição Lista nos novos Logs? 

R: A exibição Lista não está disponível nos novos Logs. Há uma seta à esquerda de cada registro na tabela de resultados. Clique nessa seta para abrir os detalhes de um registro específico. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>P. Depois de executar uma consulta, uma lista de filtros sugeridos está disponível. Como posso ver filtros? 

R: clique em ' filtros ' no painel esquerdo para ver uma visualização da implementação de novos filtros. Agora, isso tem base em seu conjunto completo de resultados, em vez de ficar limitado a 10.000 registros da interface do usuário. Atualmente, essa é uma lista dos filtros mais populares e dos 10 valores mais comuns para cada filtro. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>P. Por que estou recebendo o erro: "Registre o provedor de recursos 'Microsoft.Insights' para esta assinatura para habilitar essa consulta" nos Logs, após realizar o drilling da VM? 

R: Por padrão, vários provedores de recursos são automaticamente registrados; no entanto, talvez seja necessário registrar manualmente alguns provedores de recursos. Isso configura sua assinatura para trabalhar com o provedor de recursos. O escopo de registro é sempre a assinatura. Para saber mais, veja [Provedores e tipos de recursos](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>P. Por que não estou obtendo uma mensagem de erro de acesso ao acessar os Logs de uma página da VM? 

R: Para exibir os Logs da VM, você precisará receber permissão de leitura para os workspaces que armazena os logs da VM. Nesses casos, o administrador deve conceder a você permissões no Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>P. Por que consigo acessar meu workspace no portal do OMS, mas recebo o erro "Você não tem acesso" no portal do Azure?  

R: Para acessar um workspace no Azure, você deve ter recebido permissões no Azure. Há alguns casos em que talvez você não tenha as permissões de acesso apropriadas. Nesses casos, seu administrador deve conceder a você as permissões no Azure. Confira [Portal do OMS migrando para o Azure](oms-portal-transition.md) para saber mais.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>P. Por que não consigo ver a entrada do Designer de Exibição nos Logs?

R: O Designer de Exibição só está disponível nos Logs para os usuários que receberam permissões de Colaborador ou superior.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>P. Ainda posso usar o portal da Análise fora do Azure?

a. Sim, a página Logs no Azure e o portal do Advanced Analytics são baseados no mesmo código. O Log Analytics está sendo integrado como um recurso no Azure Monitor a fim de fornecer uma experiência de monitoramento mais unificada. Você ainda pode acessar o portal de análise usando a URL: https:\/\/Portal. loganalytics. Io/inscrições/{SubscriptionId}/resourcegroups/{resourceGroupName}/espaços de trabalho/{WorkspaceName}.



## <a name="general"></a>Geral

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>P. Como posso ver minhas exibições e soluções no portal do Azure? 

R: A lista de exibições e soluções instaladas estão disponíveis no portal do Azure. Clique em **Todos os serviços**. Na lista de recursos, selecione **Monitor**, depois clique em **...Mais**. O último workspace usado é selecionado, mas você pode selecionar qualquer outro workspace. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>P. Por que não consigo criar workspaces na região Centro-oeste dos EUA? 

R: Essa região está no limite de capacidade temporário. Esse limite está planejado para ser resolvido até o final de setembro de 2019.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>P. O Log Analytics usa o mesmo agente da Central de Segurança do Azure?

R: No início de junho de 2017, a Central de Segurança do Azure começou a usar o Microsoft Monitoring Agent para coletar e armazenar dados. Para saber mais, veja [Perguntas frequentes sobre a migração da Plataforma Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>P. Quais verificações são executadas pelas soluções AD e Avaliação do SQL?

R: A consulta a seguir mostra uma descrição de todas as verificações executadas no momento:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Os resultados podem então ser exportados para o Excel para análise adicional.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>P. Por que vejo algo diferente do OMS no console do System Center Operations Manager?

R: Dependendo do Pacote Cumulativo de Atualizações do Operations Manager em que você estiver, poderá ver um nó para *System Center Advisor*, *Insights Operacionais* ou *Log Analytics*.

A atualização de cadeia de caracteres de texto para *OMS* está incluída em um pacote de gerenciamento, que deve ser importado manualmente. Para ver o texto e a funcionalidade atuais, siga as instruções no artigo mais recente do KB sobre o Pacote Cumulativo de Atualizações do System Center Operations Manager e atualize o console.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>P: Há uma versão local do Log Analytics?

R: não. O Log Analytics é um serviço de nuvem escalonável que processa e armazena grandes quantidades de dados. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>P. Como posso ser notificado quando a coleta de dados for interrompida?

R: Use as etapas descritas em [criar um novo alerta do log](../../azure-monitor/platform/alerts-metric.md) para ser notificado quando a coleta de dados for interrompida.

Ao criar o alerta para quando a coleta de dados for interrompida, defina:

- **Definir condição de alerta** especifica seu espaço de trabalho do Log Analytics como o destino do recurso.
- **Critérios de alerta** especificam o seguinte:
   - **Nome do sinal** seleciona **Pesquisa de registro personalizada**.
   - **Consulta de pesquisa** como `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Lógica de alerta** é **Baseada em** *número de resultados* e **Condição** é *maior* que um **Limite**  de *0*
   - **Período de tempo** de *30* minutos e **Frequência de alerta** para cada *10* minutos
- **Definir os detalhes do alerta** especifica o seguinte:
   - **Nome** como *Coleta de dados interrompida*
   - A **Gravidade** como *Aviso*

Especifique um [Grupo de Ações](../../azure-monitor/platform/action-groups.md) existente ou crie um novo para que, quando o alerta do log corresponder aos critérios, você seja notificado se tiver uma pulsação ausente por mais de 15 minutos.

## <a name="configuration"></a>Configuração

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>P. Posso alterar o nome do contêiner de blob/tabela usado para ler do WAD (Diagnóstico do Azure)?

a. Não, não é possível ler de contêineres nem de tabelas arbitrárias no armazenamento do Azure no momento.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>P. Quais endereços IP é o serviço Log Analytics usa? Como fazer para garantir que meu firewall permita apenas tráfego para o serviços Log Analytics?

a. O serviço Log Analytics é baseado no Azure. Os endereços IP do Log Analytics estão nos [Intervalos IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Conforme as implantações de serviço são feitas, os endereços IP reais do serviço de Log Analytics mudam. Os nomes DNS a permitir através do firewall são documentados em [requisitos da rede](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>P. Eu uso o ExpressRoute para me conectar ao Azure. O meu tráfego do Log Analytics usará minha conexão ExpressRoute?

a. Os diferentes tipos de tráfego de ExpressRoute estão descritos na [Documentação de ExpressRoute](../../expressroute/expressroute-faqs.md#supported-services).

O tráfego para o Log Analytics usa o circuito de ExpressRoute de emparelhamento público.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>P. Há alguma maneira simples e fácil de mover um espaço de trabalho existente do Log Analytics para outra assinatura do Azure/espaço de trabalho do Log Analytics?

a. O cmdlet `Move-AzResource` permite que você mova um espaço de trabalho do Log Analytics, além de uma conta de automação de uma assinatura do Azure para outra. Para obter mais informações, consulte [move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Essa alteração também pode ser feita no portal do Azure.

Você não pode mover dados de um espaço de trabalho do Log Analytics para outro, ou alterar a região na qual os dados do Log Analytics estão armazenados.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>P: Como fazer para adicionar o Log Analytics para o System Center Operations Manager?

R: Atualizar para o pacote cumulativo de atualizações mais recente e importar pacotes de gerenciamento permitem conectar o Operations Manager ao Log Analytics.

>[!NOTE]
>A conexão do Operations Manager ao Log Analytics só está disponível para o System Center Operations Manager 2012 SP1 e posterior.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>P: como posso confirmar que um agente consiga se comunicar com o Log Analytics?

R: para garantir que o agente possa se comunicar com o espaço de trabalho Log Analytics, acesse: painel de controle, configurações de & de segurança **Microsoft Monitoring Agent**.

Sob a guia **Azure Log Analytics (OMS)** , procure por uma marca de seleção verde. Um ícone de marca de seleção verde confirma que o agente é capaz de se comunicar com o serviço do Azure.

Um ícone de aviso amarelo significa que o agente está tendo problemas de comunicação com o Log Analytics. Uma motivo comum é que o serviço Microsoft Monitoring Agent foi interrompido. Use o gerenciador de controle de serviço para reiniciar o serviço.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>P: Como fazer para impedir que um agente de se comunicar com o Log Analytics?

R: em System Center Operations Manager, remova o computador da lista Log Analytics computadores gerenciados. O Operations Manager atualiza a configuração do agente para não relatar mais para o Log Analytics. Para agentes conectados diretamente ao Log Analytics, você pode impedi-los de comunicar-se por meio de: Painel de Controle, Segurança e Configurações, **Microsoft Monitoring Agent**.
Em **Azure Log Analytics (OMS)** , remova todos os workspaces listados.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>P: Por que estou recebendo um erro ao tentar mover meu workspace de uma assinatura do Azure para outra?

R: Para mover um workspace para uma assinatura ou grupo de recursos diferente, você deve primeiro desvincular a conta de Automação do Azure no workspace. Desvincular uma conta de automação requer a remoção dessas soluções se elas estiverem instaladas no workspace: o Gerenciamento de Atualizações, o Controle de Alteração ou o Iniciar/Parar de VMs durante as horas de folga são removidos. Depois que essas soluções forem removidas, desmarque a conta de Automação do Azure selecionando **Workspaces vinculados** no painel esquerdo no recurso de conta de automação e clique em **Desvincular o workspace** na faixa de opções.
 > As soluções removidas precisam ser reinstaladas no workspace, e o link de Automação do Azure para o workspace precisa ser atualizado após a movimentação.

Verifique se você tem permissão em ambas as assinaturas do Azure.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>P: Por que estou recebendo um erro quando tento atualizar um SavedSearch?

R: Você precisa adicionar 'etag' no corpo da API ou nas propriedades do modelo do Azure Resource Manager:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Dados do agente
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>P. Que quantidade de dados posso enviar por meio do agente para o Log Analytics? Existe uma quantidade máxima de dados por cliente?
a. Não há nenhum limite para a quantidade de dados que é carregada, ela se baseia na opção de preço que você seleciona-reserva de capacidade ou pré-pago. Um espaço de trabalho Log Analytics foi projetado para escalar verticalmente para lidar com o volume proveniente de um cliente, mesmo que ele seja de terabytes por dia. Para obter mais informações, consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/monitor/).

O agente do Log Analytics foi projetado para garantir que ele tenha uma superfície pequena. O volume de dados varia de acordo com as soluções que você habilita. Você pode encontrar informações detalhadas sobre o volume de dados e ver a divisão por solução na página [Uso](../../azure-monitor/platform/data-usage.md).

Para obter mais informações, você pode ler um [blog do cliente](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) mostrando seus resultados depois de avaliar a utilização de recursos (superfície) do agente de log Analytics.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>P. Quanta largura de banda de rede é usada pelo MMA (Agente de Gerenciamento da Microsoft) ao enviar dados para o Log Analytics?

a. A largura de banda é uma função na quantidade de dados enviados. Dados são compactados conforme eles são enviados pela rede.

### <a name="q-how-much-data-is-sent-per-agent"></a>P. Qual a quantidade de dados enviada por agente?

a. A quantidade de dados enviados por agente depende:

* Das soluções que você habilitou
* Do número de logs e contadores de desempenho sendo coletados
* Do volume de dados nos logs

O uso geral é mostrado na página [Uso](../../azure-monitor/platform/data-usage.md) .

Para computadores capazes de executar o agente WireData, use a seguinte consulta para ver quantos dados estão sendo enviados:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Próximas etapas

Comece a [usar o Azure monitor](../../azure-monitor/overview.md) para saber mais sobre log Analytics e entrar em funcionamento em minutos.
