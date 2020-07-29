---
title: Monitorar a Sincronização de Dados SQL com logs do Azure Monitor
description: Saiba como monitorar Sincronização de Dados SQL usando logs de Azure Monitor.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 307e501743d01b94cfca3692cc09c05cc90ed3ce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84343227"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorar a Sincronização de Dados SQL com logs do Azure Monitor 

Para verificar o log de atividades da Sincronização de Dados SQL e detectar erros e avisos, anteriormente você precisava verificar a Sincronização de Dados SQL manualmente no portal do Azure ou usar o PowerShell ou a API REST. Siga as etapas deste artigo para configurar uma solução personalizada que melhora a experiência de monitoramento da Sincronização de Dados. Personalize essa solução de acordo com seu cenário.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL no Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> O Sincronização de Dados SQL **não dá suporte ao** Azure SQL instância gerenciada no momento.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Painel de monitoramento para todos os seus grupos de sincronização 

Você não precisa mais examinar os logs de cada grupo de sincronização individualmente para procurar por problemas. Você pode monitorar todos os seus grupos de sincronização de qualquer uma de suas assinaturas em um único lugar usando uma exibição de Azure Monitor personalizada. Essa exibição apresenta as informações importantes para os clientes da Sincronização de Dados SQL.

![Painel de monitoramento da Sincronização de Dados](./media/sql-data-sync-monitor-sync/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notificações automatizadas por email

Você não precisa mais verificar o log manualmente no Portal do Azure ou por meio do PowerShell ou da API REST. Com [os logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), você pode criar alertas que vão diretamente para os endereços de email das pessoas que precisam vê-los quando ocorrer um erro.

![Notificações por email da Sincronização de Dados](./media/sql-data-sync-monitor-sync/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Como configurar esses recursos de monitoramento? 

Implemente uma solução de monitoramento de logs de Azure Monitor personalizada para Sincronização de Dados SQL em menos de uma hora, fazendo o seguinte:

É necessário configurar três componentes:

-   Um runbook do PowerShell para alimentar Sincronização de Dados SQL dados de log para Azure Monitor logs.

-   Um alerta de Azure Monitor para notificações por email.

-   Uma exibição Azure Monitor para monitoramento.

### <a name="samples-to-download"></a>Amostras para download

Baixe os dois seguintes exemplos:

-   [Runbook do PowerShell do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Exibição de Azure Monitor de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você configurou o seguinte:

-   Uma conta da Automação do Azure

-   Workspace do Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook do PowerShell para obter o Log da Sincronização de Dados SQL 

Use um runbook do PowerShell hospedado na automação do Azure para efetuar pull do Sincronização de Dados SQL dados de log e enviá-los para Azure Monitor logs. Um script de exemplo é incluído. Como pré-requisito, você precisa ter uma conta da Automação do Azure. Em seguida, você precisa criar um runbook e agendá-lo para execução. 

### <a name="create-a-runbook"></a>Criar um runbook

Para obter mais informações sobre como criar um runbook, consulte [Meu primeiro runbook do PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Em sua conta da Automação do Azure, selecione a guia **Runbooks** em Automação de Processo.

2.  Selecione **Adicionar um runbook** no canto superior esquerdo da página Runbooks.

3.  Selecione **Importar um runbook existente**.

4.  Em **Arquivo de runbook**, use o determinado arquivo `DataSyncLogPowerShellRunbook`. Defina o **Tipo de runbook** como `PowerShell`. Forneça um nome para o runbook.

5.  Selecione **Criar**. Agora você tem um runbook.

6.  Em sua Conta da Automação do Azure, selecione a guia **Variáveis** em Recursos Compartilhados.

7.  Selecione **Adicionar uma variável** na página Variáveis. Crie uma variável para armazenar o último tempo de execução para o runbook. Caso você tenha vários runbooks, precisará de uma variável para cada runbook.

8.  Defina o nome da variável como `DataSyncLogLastUpdatedTime` e defina seu Tipo como DateTime.

9.  Selecione o runbook e clique no botão Editar na parte superior da página.

10. Faça as alterações necessárias em sua conta e na configuração da Sincronização de Dados SQL. (Consulte o script de exemplo para obter informações mais detalhadas.)

    1.  Informações do Azure.

    2.  informações do grupo de sincronização.

    3.  Azure Monitor registra as informações. Localizar essas informações em portal do Azure | Configurações | Fontes conectadas. Para obter mais informações sobre como enviar dados para logs de Azure Monitor, consulte [enviar dados para logs de Azure monitor com a API do coletor de dados http (versão prévia)](../../azure-monitor/platform/data-collector-api.md).

11. Execute o runbook no painel Teste. Verifique se ele foi bem-sucedido.

    Caso você encontre erros, verifique se você tem o último módulo do PowerShell instalado. Você pode instalar o módulo mais recente do PowerShell nos **Galeria de Módulos** na sua Conta de Automação.

12. Clique em **publicar**

### <a name="schedule-the-runbook"></a>Agendar o runbook

Para agendar o runbook:

1.  No runbook, selecione a guia **Agendamentos** em Recursos.

2.  Selecione **Adicionar um Agendamento** na página Agendamentos.

3.  Selecione **vincular um agendamento ao seu runbook**.

4.  Selecione **criar um novo agendamento.**

5.  Defina **Recorrência** como Recorrente e defina o intervalo desejado. Use o mesmo intervalo aqui, no script e em logs de Azure Monitor.

6.  Selecione **Criar**.

### <a name="check-the-automation"></a>Verificar a automação

Para monitorar se a automação está funcionando conforme esperado, em **Visão Geral** de sua conta de automação, localize a exibição **Estatísticas de Trabalho** em **Monitoramento**. Fixe isto no painel para facilitar a visualização. As execuções com êxito do runbook são mostradas como “Concluídas” e as execuções com falha são mostradas como “Com Falha."

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Criar um alerta do leitor de Azure Monitor para notificações por email

Para criar um alerta que usa logs de Azure Monitor, faça o seguinte. Como pré-requisito, você precisa ter Azure Monitor logs vinculados a um espaço de trabalho Log Analytics.

1.  No portal do Azure, selecione **Pesquisa de Logs**.

2.  Crie uma consulta para selecionar os erros e avisos por grupo de sincronização dentro do intervalo selecionado. Por exemplo:

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Depois de executar a consulta, selecione o sino que indica **Alerta**.

4.  Em **Gerar alerta com base em**, selecione **Medição Métrica**.

    1.  Defina o Valor de Agregação como **Maior que**.

    2.  Após **Maior que**, insira o limite a ser decorrido antes que você receba notificações. Erros transitórios são esperados na sincronização de dados. Para reduzir o ruído, defina o limite como 5.

5.  Em **ações**, defina **notificação por email** como "Sim". Insira os destinatários de email desejados.

6.  Clique em **Save** (Salvar). Agora, os destinatários especificados recebem notificações por email quando ocorrem erros.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Criar uma exibição de Azure Monitor para monitoramento

Esta etapa cria uma exibição Azure Monitor para monitorar visualmente todos os grupos de sincronização especificados. A exibição inclui vários componentes:

-   Um bloco de visão geral, que mostra quantos erros, êxitos e avisos existem em todos os grupos de sincronização.

-   Um bloco para todos os grupos de sincronização, que mostra a contagem de erros e avisos por grupo de sincronização. Grupos sem problemas não são exibidos neste bloco.

-   Um bloco para cada grupo de sincronização, que mostra o número de erros, êxitos e avisos e as mensagens de erro recentes.

Para configurar o Azure Monitor exibição, faça o seguinte:

1.  Na home page do espaço de trabalho Log Analytics, selecione o mais à esquerda para abrir o **Designer de exibição**.

2.  Selecione **Importação** na barra superior do designer de exibição. Em seguida, selecione o arquivo de exemplo “DataSyncLogOMSView”.

3.  A exibição de exemplo serve para gerenciar dois grupos de sincronização. Edite essa exibição de acordo com seu cenário. Clique em **Editar** e faça as seguintes alterações:

    1.  Crie novos objetos de “Rosca e Lista” por meio da Galeria, conforme necessário.

    2.  Em cada bloco, atualize as consultas com suas informações.

        1.  Em cada bloco, altere o intervalo de TimeStamp_t conforme desejado.

        2.  Nos blocos de cada grupo de sincronização, atualize os nomes dos grupos de sincronização.

    3.  Em cada bloco, atualize o título, conforme necessário.

4.  Clique em **Salvar** e a exibição estará pronta.

## <a name="cost-of-this-solution"></a>Custo desta solução

Na maioria dos casos, essa solução é gratuita.

**Automação do Azure:** pode haver um custo com a conta da Automação do Azure, dependendo do uso. Os primeiros 500 minutos do tempo de execução do trabalho por mês são gratuitos. Na maioria dos casos, espera-se que essa solução use menos de 500 minutos por mês. Para evitar encargos, agende o runbook para que ele seja executado em um intervalo de duas horas ou mais. Para obter mais informações, consulte [Preços da Automação](https://azure.microsoft.com/pricing/details/automation/).

**Logs de Azure monitor:** Pode haver um custo associado a Azure Monitor logs, dependendo do seu uso. A camada gratuita inclui 500 MB de dados ingeridos por dia. Na maioria dos casos, espera-se que essa solução ingira menos de 500 MB por dia. Para diminuir o uso, use a filtragem somente falha incluída no runbook. Se você estiver usando mais de 500 MB por dia, atualize para a camada paga para evitar o risco de a análise parar quando a limitação for atingida. Para obter mais informações, consulte [preços de Azure monitor logs](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Exemplos de código

Baixe os exemplos de código descritos neste artigo nos seguintes locais:

-   [Runbook do PowerShell do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Exibição de Azure Monitor de sincronização de dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Sincronização de Dados SQL, veja:

-   Visão geral – [Sincronize dados em vários bancos de dados locais e na nuvem com a Sincronização de Dados SQL no Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar sincronização de dados
    - No portal- [tutorial: configurar o sincronização de dados SQL para sincronizar dados entre o Azure SQL Database e SQL Server](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Usar o PowerShell para sincronizar entre vários bancos de dados no banco de dados SQL do Azure](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Usar o PowerShell para sincronizar entre um banco de dados no banco de dados SQL do Azure e um banco de dados em uma instância do SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync Agent – [Data Sync Agent para Sincronização de Dados SQL no Azure](sql-data-sync-agent-overview.md)
-   Melhores práticas – [Melhores práticas para a Sincronização de Dados SQL no Azure](sql-data-sync-best-practices.md)
-   Solucionar problemas – [Solucionar problemas com a Sincronização de Dados SQL no Azure](sql-data-sync-troubleshoot.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL – [automatizar a replicação de alterações de esquema no sincronização de dados SQL no Azure](sql-data-sync-update-sync-schema.md)
    -   Com o PowerShell - [usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)

Para saber mais sobre o Banco de Dados SQL, veja:

-   [Visão geral do Banco de Dados SQL](../../sql-database/sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
