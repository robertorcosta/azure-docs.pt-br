---
title: Monitorar a Sincronização de Dados SQL com logs do Azure Monitor
description: Saiba como monitorar o Azure SQL Data Sync usando registros do Monitor do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 0ed0bd3544fff89c8230267e3d6d8826c5ae3c7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74114609"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Monitorar a Sincronização de Dados SQL com logs do Azure Monitor 

Para verificar o log de atividades da Sincronização de Dados SQL e detectar erros e avisos, anteriormente você precisava verificar a Sincronização de Dados SQL manualmente no portal do Azure ou usar o PowerShell ou a API REST. Siga as etapas deste artigo para configurar uma solução personalizada que melhora a experiência de monitoramento da Sincronização de Dados. Personalize essa solução de acordo com seu cenário.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> O Azure SQL Data Sync **não** suporta a instância gerenciada do banco de dados Azure SQL no momento.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Painel de Monitoramento para todos os Grupos de Sincronização 

Você não precisa mais examinar os logs de cada Grupo de Sincronização individualmente para procurar problemas. Você pode monitorar todos os seus Grupos sync de qualquer uma de suas assinaturas em um só lugar usando uma exibição personalizada do Monitor Azure. Essa exibição apresenta as informações importantes para os clientes da Sincronização de Dados SQL.

![Painel de monitoramento da Sincronização de Dados](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notificações automatizadas por email

Você não precisa mais verificar o log manualmente no Portal do Azure ou por meio do PowerShell ou da API REST. Com [os logs do Monitor do Azure,](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)você pode criar alertas que vão diretamente para os endereços de e-mail das pessoas que precisam vê-los quando ocorre um erro.

![Notificações por email da Sincronização de Dados](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>Como configurar esses recursos de monitoramento? 

Implemente uma solução personalizada de logs de logs do Azure Monitor para sincronização de dados SQL em menos de uma hora, fazendo as seguintes coisas:

É necessário configurar três componentes:

-   Um runbook do PowerShell para alimentar os dados de sincronização de dados SQL para logs do Monitor do Azure.

-   Um alerta do Monitor Do Azure para notificações de e-mail.

-   Uma exibição do Monitor Do Azure para monitoramento.

### <a name="samples-to-download"></a>Amostras para download

Baixe os dois seguintes exemplos:

-   [Runbook do PowerShell do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Visualização do Monitor do Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você configurou o seguinte:

-   Uma conta da Automação do Azure

-   Espaço de trabalho do Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook do PowerShell para obter o Log da Sincronização de Dados SQL 

Use um runbook do PowerShell hospedado no Azure Automation para extrair os dados de log do SQL Data Sync e enviá-los para logs do Monitor do Azure. Um script de exemplo é incluído. Como pré-requisito, você precisa ter uma conta da Automação do Azure. Em seguida, você precisa criar um runbook e agendá-lo para execução. 

### <a name="create-a-runbook"></a>Criar um runbook

Para obter mais informações sobre como criar um runbook, consulte [Meu primeiro runbook do PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  Em sua conta da Automação do Azure, selecione a guia **Runbooks** em Automação de Processo.

2.  Selecione **Adicionar um Runbook** no canto superior esquerdo da página Runbooks.

3.  Selecione **Importar um runbook existente**.

4.  Em **Arquivo de runbook**, use o determinado arquivo `DataSyncLogPowerShellRunbook`. Defina o **Tipo de runbook** como `PowerShell`. Forneça um nome para o runbook.

5.  Selecione **Criar**. Agora você tem um runbook.

6.  Em sua Conta da Automação do Azure, selecione a guia **Variáveis** em Recursos Compartilhados.

7.  Selecione **Adicionar uma variável** na página Variáveis. Crie uma variável para armazenar o último tempo de execução para o runbook. Caso você tenha vários runbooks, precisará de uma variável para cada runbook.

8.  Defina o nome da variável como `DataSyncLogLastUpdatedTime` e defina seu Tipo como DateTime.

9.  Selecione o runbook e clique no botão Editar na parte superior da página.

10. Faça as alterações necessárias em sua conta e na configuração da Sincronização de Dados SQL. (Consulte o script de exemplo para obter informações mais detalhadas.)

    1.  Informações do Azure.

    2.  Informações do Grupo de Sincronização.

    3.  O Azure Monitor registra informações. Encontre essas informações em portal do Azure | Configurações | Fontes Conectadas. Para obter mais informações sobre o envio de dados para os logs do Azure Monitor, consulte [Enviar dados para os logs do Azure Monitor com a API (visualização) http data collector](../azure-monitor/platform/data-collector-api.md).

11. Execute o runbook no painel Teste. Verifique se ele foi bem-sucedido.

    Caso você encontre erros, verifique se você tem o último módulo do PowerShell instalado. Você pode instalar o módulo mais recente do PowerShell nos **Galeria de Módulos** na sua Conta de Automação.

12. Clique **em Publicar**

### <a name="schedule-the-runbook"></a>Agendar o runbook

Para agendar o runbook:

1.  No runbook, selecione a guia **Agendamentos** em Recursos.

2.  Selecione **Adicionar um Agendamento** na página Agendamentos.

3.  Selecione **Vincular um cronograma ao seu manual**.

4.  Selecione **Criar um novo cronograma.**

5.  Defina **Recorrência** como Recorrente e defina o intervalo desejado. Use o mesmo intervalo aqui, no script e nos registros do Monitor do Azure.

6.  Selecione **Criar**.

### <a name="check-the-automation"></a>Verificar a automação

Para monitorar se a automação está funcionando conforme esperado, em **Visão Geral** de sua conta de automação, localize a exibição **Estatísticas de Trabalho** em **Monitoramento**. Fixe isto no painel para facilitar a visualização. As execuções com êxito do runbook são mostradas como “Concluídas” e as execuções com falha são mostradas como “Com Falha."

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Crie um alerta de leitor do Monitor do Azure para notificações de e-mail

Para criar um alerta que use logs do Monitor do Azure, faça as seguintes coisas. Como pré-requisito, você precisa ter registros do Monitor Do Azure vinculados a um espaço de trabalho do Log Analytics.

1.  No portal do Azure, selecione **Pesquisa de Logs**.

2.  Crie uma consulta para selecionar os erros e avisos por grupo de sincronização dentro do intervalo selecionado. Por exemplo: 

    `DataSyncLog_CL | where LogLevel_s != "Success" | summarize AggregatedValue = count() by bin(TimeGenerated,60m),SyncGroupName_s`

3.  Depois de executar a consulta, selecione o sino que indica **Alerta**.

4.  Em **Gerar alerta com base em**, selecione **Medição Métrica**.

    1.  Defina o Valor de Agregação como **Maior que**.

    2.  Após **Maior que**, insira o limite a ser decorrido antes que você receba notificações. Erros transitórios são esperados no Data Sync. Para reduzir o ruído, defina o limiar para 5.

5.  Em **Ações,** defina a **notificação de e-mail** como "Sim". Insira os destinatários de email desejados.

6.  Clique em **Salvar**. Agora, os destinatários especificados recebem notificações por email quando ocorrem erros.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Crie uma exibição do Monitor do Azure para monitoramento

Esta etapa cria uma exibição do Monitor Do Azure para monitorar visualmente todos os grupos de sincronização especificados. A exibição inclui vários componentes:

-   Um bloco de visão geral, que mostra quantos erros, êxitos e avisos existem em todos os grupos de sincronização.

-   Um bloco para todos os grupos de sincronização, que mostra a contagem de erros e avisos por grupo de sincronização. Grupos sem problemas não são exibidos neste bloco.

-   Um bloco para cada Grupo de Sincronização, que mostra o número de erros, êxitos e avisos, bem como as mensagens de erro recentes.

Para configurar a exibição do Monitor do Azure, faça as seguintes coisas:

1.  Na página inicial do espaço de trabalho do Log Analytics, selecione o plus à esquerda para abrir o **designer de exibição**.

2.  Selecione **Importação** na barra superior do designer de exibição. Em seguida, selecione o arquivo de exemplo “DataSyncLogOMSView”.

3.  A exibição de exemplo serve para gerenciar dois grupos de sincronização. Edite essa exibição de acordo com seu cenário. Clique em **Editar** e faça as seguintes alterações:

    1.  Crie novos objetos de “Rosca e Lista” por meio da Galeria, conforme necessário.

    2.  Em cada bloco, atualize as consultas com suas informações.

        1.  Em cada bloco, altere o intervalo de TimeStamp_t conforme desejado.

        2.  Nos blocos de cada Grupo de Sincronização, atualize os nomes do Grupo de Sincronização.

    3.  Em cada bloco, atualize o título, conforme necessário.

4.  Clique em **Salvar** e a exibição estará pronta.

## <a name="cost-of-this-solution"></a>Custo desta solução

Na maioria dos casos, essa solução é gratuita.

**Automação do Azure:** pode haver um custo com a conta da Automação do Azure, dependendo do uso. Os primeiros 500 minutos do tempo de execução do trabalho por mês são gratuitos. Na maioria dos casos, espera-se que essa solução use menos de 500 minutos por mês. Para evitar encargos, agende o runbook para que ele seja executado em um intervalo de duas horas ou mais. Para obter mais informações, consulte [Preços da Automação](https://azure.microsoft.com/pricing/details/automation/).

**Registros do Monitor do Azure:** Pode haver um custo associado aos registros do Monitor Do Azure, dependendo do seu uso. A camada gratuita inclui 500 MB de dados ingeridos por dia. Na maioria dos casos, espera-se que essa solução ingira menos de 500 MB por dia. Para diminuir o uso, use a filtragem somente falha incluída no runbook. Caso você esteja usando mais de 500 MB por dia, faça atualização para a camada paga para evitar o risco da interrupção da análise quando o limite for atingido. Para obter mais informações, consulte [os preços dos registros do Azure Monitor](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Exemplos de código

Baixe os exemplos de código descritos neste artigo nos seguintes locais:

-   [Runbook do PowerShell do Log da Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Visualização do Monitor do Sincronização de Dados](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Sincronização de Dados SQL, veja:

-   Visão geral - [Sincronize dados em vários bancos de dados locais e na nuvem com o Azure SQL Data Sync](sql-database-sync-data.md)
-   Configurar sincronização de dados
    - No portal - [Tutorial: configure o SQL Data Sync para sincronizar dados entre o Banco de Dados SQL do Azure e o SQL Server local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Use o PowerShell para sincronizar entre vários bancos de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de Sincronização de Dados - [Agente de Sincronização de Dados para Sincronização de Dados SQL do Azure](sql-database-data-sync-agent.md)
-   Práticas recomendadas - [Práticas recomendadas para a Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)
-   Solucionar problemas - [Solucionar problemas com o SQL Data Sync do Azure](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com Transact-SQL - [Automatize a replicação de alterações de esquema no Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Com o PowerShell - [usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para saber mais sobre o Banco de Dados SQL, veja:

-   [Visão geral do banco de dados SQL](sql-database-technical-overview.md)
-   [Gerenciamento do ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
