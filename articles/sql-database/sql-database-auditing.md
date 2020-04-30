---
title: Auditoria do SQL do Azure
description: Use a auditoria de banco de dados SQL do Azure para rastrear eventos de banco de dados para um log de auditoria.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 57c4b22dfe6ef6cf44be64a4b5c042403f64ccf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096649"
---
# <a name="azure-sql-auditing"></a>Auditoria do SQL do Azure

A auditoria do [banco de dados SQL](sql-database-technical-overview.md) do Azure e [do Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rastreia eventos de banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure, log Analytics espaço de trabalho ou hubs de eventos. 

A auditoria também:

- Ajuda você a manter a conformidade regulatória, entender a atividade do banco de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações para os negócios ou suspeitas de violações de segurança.

- Permite e facilita a adesão aos padrões de conformidade, embora não garanta a conformidade. Para obter mais informações sobre os programas do Azure que dão suporte à conformidade com padrões, consulte a [central de confiabilidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

> [!NOTE] 
> Este tópico aplica-se ao banco de dados SQL do Azure e aos bancos de dados do Azure Synapse Analytics. Para simplificar, o banco de dados SQL é usado ao fazer referência ao banco de dados SQL do Azure e ao Azure Synapse Analytics.

## <a name="overview"></a><a id="overview"></a>Visão geral

É possível usar a auditoria do banco de dados SQL para:

- **Retenha** uma trilha de auditoria dos eventos selecionados. Definir categorias de ações de banco de dados a ser auditadas.
- **Relate** sobre a atividade do banco de dados. Você pode usar relatórios pré-configurados e um painel para se familiarizar rapidamente com a atividade e o relatório de eventos.
- **Analise** relatórios. Encontrar eventos suspeitos, atividades incomuns e tendências.

> [!IMPORTANT]
> - A auditoria do banco de dados SQL do Azure é otimizada para disponibilidade & desempenho. Durante uma atividade muito alta, o banco de dados SQL do Azure permite que as operações continuem e não registrem alguns eventos auditados.

### <a name="auditing-limitations"></a>Limitações de auditoria

- Atualmente, **não há suporte** para o **Armazenamento Premium**.
- O **namespace hierárquico** para **Azure data Lake Storage Gen2 conta** de armazenamento **não tem suporte**no momento.
- Não há suporte para habilitar a auditoria em um **SQL data warehouse do Azure** em pausa. Para habilitar a auditoria, retome o data warehouse.

#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definir a política de auditoria no nível do servidor versus no nível do banco de dados

Uma política de auditoria pode ser definida para um banco de dados específico ou como uma política padrão de servidor:

- Uma política de servidor se aplica a todos os bancos de dados existentes e recém-criados no servidor.

- Se a *auditoria do servidor estiver habilitada*, ela *sempre se aplicará ao banco de dados*. O banco de dados será auditado, independentemente das configurações de auditoria do banco de dados.

- Habilitar a auditoria no banco de dados ou data warehouse, além de habilitá-lo no servidor, *não substitui nem* altera as configurações da auditoria do servidor. Ambas as auditorias existirão lado a lado. Em outras palavras, o banco de dados é auditado duas vezes em paralelo: uma vez pela política de servidor e outra, pela política de banco de dados.

   > [!NOTE]
   > Você deve evitar habilitar a auditoria de servidor e a auditoria de blob de banco de dados juntos, a menos que:
    > - Você deseja usar uma conta de *armazenamento*diferente, *período de retenção* ou *espaço de trabalho de log Analytics* para um banco de dados específico.
    > - Você deseja auditar tipos de evento ou categorias de um banco de dados específico diferentes do restante dos bancos de dados no servidor. Por exemplo, talvez você tenha inserções de tabela que precisam ser auditadas somente em um banco de dados específico.
   >
   > Caso contrário, recomendamos que você habilite apenas a auditoria no nível do servidor e deixe a auditoria no nível do banco de dados desabilitada para todos os bancos de dados.

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Configurar a auditoria para o servidor

A política de auditoria padrão inclui todas as ações e o seguinte conjunto de grupos de ação, que farão a auditoria de todas as consultas e todos os procedimentos armazenados executados no banco de dados, bem como os logons com falha e bem-sucedidos:
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  
Você pode configurar a auditoria para diferentes tipos de ações e grupos de ação usando o PowerShell, conforme descrito na seção [Gerenciar a auditoria do Banco de Dados SQL usando o Azure PowerShell](#manage-auditing).

A auditoria de Banco de Dados SQL do Azure armazena 4000 caracteres de dados para campos de caracteres em um registro de auditoria. Quando os valores de **instrução** ou **data_sensitivity_information** retornados de uma ação auditável contêm mais de 4000 caracteres, os dados após os primeiros 4000 caracteres serão **truncados e não auditados**.
A seção a seguir descreve a configuração de auditoria usando o Portal do Azure.

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Navegue até **auditoria** sob o título de segurança no seu painel de servidor/banco de dados SQL.
3. Se preferir configurar uma política de auditoria de servidor, selecione o link **Exibir configurações do servidor** na página de auditoria do banco de dados. Depois, é possível exibir ou modificar as configurações de auditoria do servidor. As políticas de auditoria de servidor se aplicam a todos os bancos de dados existentes e recém-criados nesse servidor.

    ![Painel de navegação](./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png)

4. Se você preferir habilitar a auditoria no nível do banco de dados, alterne **Auditoria** para **LIGADO**. Se a auditoria do servidor estiver habilitada, a auditoria configurada para o banco de dados existirá lado a lado com a auditoria do servidor.

5. Você tem várias opções para configurar onde os logs de auditoria serão gravados. Você pode gravar logs em uma conta de armazenamento do Azure, em um espaço de trabalho Log Analytics para consumo por Azure Monitor logs (versão prévia) ou no Hub de eventos para consumo usando o Hub de eventos (visualização). Você pode configurar qualquer combinação dessas opções, e os logs de auditoria serão gravados em cada uma.
  
   ![opções de armazenamento](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Auditoria para destino de armazenamento

Para configurar a gravação de logs de auditoria para uma conta de armazenamento, selecione **Armazenamento** e abra **Detalhes do armazenamento**. Selecione a conta de armazenamento do Azure na qual os logs serão salvos e, em seguida, selecione o período de retenção. Em seguida, clique em **OK**. Os logs anteriores ao período de retenção são excluídos.

- O valor padrão do período de retenção é 0 (retenção ilimitada). Você pode alterar esse valor movendo o controle deslizante de **retenção (dias)** em **configurações de armazenamento** ao configurar a conta de armazenamento para auditoria.
  - Se você alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, observe que a retenção será aplicada somente aos logs gravados após a alteração do valor de retenção (os logs gravados durante o período em que a retenção foi definida como ilimitada serão preservados, mesmo após a retenção ser habilitada).

  ![do Azure](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="remarks"></a>Comentários

- Os logs de auditoria são gravados em **blobs de acréscimo** em um armazenamento de BLOBs do Azure em sua assinatura do Azure
- Para configurar um repositório de logs imutável para os eventos de auditoria no nível do servidor ou do banco de dados, siga as [instruções fornecidas pelo armazenamento do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (verifique se você selecionou **permitir acréscimos adicionais** ao configurar o armazenamento de blob imutável).
- Você pode gravar logs de auditoria em uma conta de armazenamento do Azure por trás de uma VNet ou um firewall. Para obter instruções específicas, consulte [gravar auditoria em uma conta de armazenamento por trás da VNet e do firewall](create-auditing-storage-account-vnet-firewall.md).
- Depois de definir as configurações de auditoria, você poderá ativar o novo recurso de detecção de ameaças e configurar emails para receber alertas de segurança. Ao usar a detecção de ameaças, você recebe alertas proativos sobre atividades anômalas do banco de dados que podem indicar possíveis ameaças à segurança. Para obter mais informações, consulte [Introdução à detecção de ameaças](sql-database-threat-detection-get-started.md).
- Para obter detalhes sobre o formato de log, a hierarquia da pasta de armazenamento e as convenções de nomenclatura, consulte a [Referência de formato do log de auditoria de blob](https://go.microsoft.com/fwlink/?linkid=829599).
- Ao usar a autenticação do AAD, falha logons registros serão *não* aparecem no log de auditoria do SQL. Para exibir logs de auditoria de logon com falha, você precisa visitar o [portal do Azure Active Directory]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), que registra os detalhes desses eventos.
- A auditoria em [réplicas somente leitura](sql-database-read-scale-out.md) é habilitada automaticamente. Para obter mais detalhes sobre a hierarquia das pastas de armazenamento, convenções de nomenclatura e formato de log, consulte o [formato de log de auditoria do banco de dados SQL](sql-database-audit-log-format.md). 

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Auditoria para Log Analytics destino
  
Para configurar a gravação de logs de auditoria em um espaço de trabalho do Log Analytics, selecione **Log Analytics (Visualizar)** e abra **detalhes do Log Analytics**. Selecione ou crie o espaço de trabalho do Log Analytics, onde os logs serão gravados e, em seguida, clique em **Ok**.
   
   ![LogAnalyticsworkspace](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Auditoria para destino do hub de eventos

> [!WARNING]
> A habilitação da auditoria em um servidor que tem um pool do SQL faz com que **o pool SQL seja retomado e pausado novamente,** o que pode incorrer em encargos de cobrança.
> Não é possível habilitar a auditoria em um pool SQL pausado. Para habilitá-lo, cancele a pausa do pool do SQL.

Para configurar a gravação de logs de auditoria para um hub de eventos, selecione **Hub de Eventos (versão prévia)** e abra **Detalhes do Hub de Eventos**. Selecione o hub de eventos no qual os logs serão gravados e, em seguida, clique em **OK**. Verifique se o hub de eventos está na mesma região que o banco de dados e o servidor.

   ![Eventhub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analise os logs e relatórios de auditoria

Se você optar por gravar logs de auditoria em logs de Azure Monitor:

- Use o [portal do Azure](https://portal.azure.com).  Abra o banco de dados relevante. Na parte superior da página **Auditoria** do banco de dados, clique em **Exibir logs de auditoria**.

    ![exibir logs de auditoria](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Em seguida, você tem duas maneiras de exibir os logs:
    
    Clicar em **log Analytics** na parte superior da página de **registros de auditoria** abrirá o modo de exibição logs no espaço de trabalho log Analytics, no qual você pode personalizar o intervalo de tempo e a consulta de pesquisa.
    
    ![abrir no espaço de trabalho Log Analytics](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Clicar em **exibir painel** na parte superior da página de **registros de auditoria** abrirá um painel exibindo informações de logs de auditoria, nos quais você pode fazer uma busca detalhada nos insights de segurança, acessar dados confidenciais e muito mais. Este painel foi projetado para ajudá-lo a obter informações de segurança para seus dados.
    Você também pode personalizar o intervalo de tempo e a consulta de pesquisa. 
    ![Exibir Log Analytics painel](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Painel do Log Analytics](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics informações de segurança](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Como alternativa, você também pode acessar os logs de auditoria na folha Log Analytics. Abra seu espaço de trabalho do Log Analytics e, na seção **Geral**, clique em **Logs**. Você pode começar com uma consulta simples, como: *pesquisar "SQLSecurityAuditEvents"* para exibir logs de auditoria.
    A partir daqui, você também pode usar [logs de Azure monitor](../log-analytics/log-analytics-log-search.md) para executar pesquisas avançadas em seus dados de log de auditoria. Os logs de Azure Monitor fornecem informações operacionais em tempo real usando pesquisa integrada e painéis personalizados para analisar rapidamente milhões de registros em todas as suas cargas de trabalho e servidores. Para obter informações úteis adicionais sobre os comandos e o idioma de pesquisa de logs do Azure Monitor, consulte [referência de pesquisa de Azure monitor logs](../log-analytics/log-analytics-log-search.md).

Se você tiver escolhido gravar logs de auditoria no Hub de Eventos:

- Para consumir dados de logs de auditoria do Hub de Eventos, você precisará configurar um fluxo para consumir eventos e gravá-las em um destino. Para obter mais informações, veja a [Documentação de Hubs de Eventos do Azure](../event-hubs/index.yml).
- Os logs de auditoria no Hub de Eventos são capturados no corpo dos eventos do [Apache Avro](https://avro.apache.org/) e armazenados usando a formatação JSON com codificação UTF-8. Para ler os logs de auditoria, você pode usar as [Ferramentas Avro](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) ou ferramentas similares que processam esse formato.

Se você optar por gravar logs de auditoria em uma conta de Armazenamento do Azure, poderá usar vários métodos para exibir os logs:

- Os logs de auditoria são agregados na conta escolhida durante a instalação. Você pode explorar os logs de auditoria usando uma ferramenta como o [Gerenciador de armazenamento do Azure](https://storageexplorer.com/). No Armazenamento do Azure, os logs de auditoria de blob são salvos como uma coleção de arquivos de blob em um contêiner chamado **sqldbauditlogs**. Para obter mais detalhes sobre a hierarquia das pastas de armazenamento, convenções de nomenclatura e formato de log, consulte o [formato de log de auditoria do banco de dados SQL](https://go.microsoft.com/fwlink/?linkid=829599).

- Use o [portal do Azure](https://portal.azure.com).  Abra o banco de dados relevante. Na parte superior da página **Auditoria** do banco de dados, clique em **Exibir logs de auditoria**.

    ![Painel de navegação](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

    **Registros de auditoria** é aberto, no qual você pode exibir os logs.

  - Exiba datas específicas clicando em **Filtro** na parte superior da página **Registros de auditoria**.
  - Você pode alternar entre os registros de auditoria que foram criados pela *política de auditoria de servidor* e o *política de auditoria de banco de dados* ativando/desativando **origem auditoria**.
  - Você pode exibir apenas os registros de auditoria relacionados de injeção de SQL clicando na caixa de seleção **Mostrar apenas registros das injeções de SQL de auditoria**.

       ![Painel de navegação]( ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png)

- Use a função do sistema **sys.fn_get_audit_file** (T-SQL) para retornar os dados do log de auditoria em um formato tabular. Para obter mais informações sobre como usar essa função, veja [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Use a opção **Mesclar Arquivos de Auditoria** no SQL Server Management Studio (a partir do SSMS 17):
    1. No menu do SSMS, selecione **arquivo** > **abrir** > **mesclar arquivos de auditoria**.

        ![Painel de navegação](./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png)
    2. A caixa de diálogo **Adicionar Arquivos de Auditoria** será aberta. Selecione uma das opções **Adicionar**, escolha se deseja mesclar arquivos de auditoria de um disco local ou importá-los do Armazenamento do Azure. Você deve fornecer os detalhes do Armazenamento do Azure e a chave de conta.

    3. Depois que todos os arquivos a serem mesclados forem adicionados, clique em **OK** para concluir a operação de mesclagem.

    4. O arquivo mesclado é aberto no SSMS, no qual você pode exibi-lo e analisá-lo, bem como exportá-lo para um arquivo XEL ou CSV ou para uma tabela.

- Use o Power BI. Você pode exibir e analisar dados do log de auditoria no Power BI. Para obter mais informações e para acessar um modelo para download, confira [Analisar dados de log de auditoria no Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Baixe arquivos de log do seu contêiner de blob do armazenamento do Azure por meio do portal ou usando uma ferramenta como o [Gerenciador de armazenamento do Azure](https://storageexplorer.com/).
  - Depois de baixar um arquivo de log localmente, clique duas vezes no arquivo para abrir, exibir e analisar os logs no SSMS.
  - Baixe também vários arquivos simultaneamente por meio do Gerenciador de Armazenamento do Azure. Para isso, clique com o botão direito do mouse em uma subpasta específica e selecione **Salvar como** para salvar em uma pasta local.

- Métodos adicionais:

  - Depois de baixar vários arquivos ou uma subpasta que contém arquivos de log, você pode mesclá-los localmente, conforme descrito nas instruções de Arquivos de Auditoria de Mesclagem do SSMS indicadas anteriormente.
  - Exiba os logs de auditoria de blob de forma programática:

    - [Consulte Arquivos de Eventos Estendidos usando o PowerShell](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/).

## <a name="production-practices"></a><a id="production-practices"></a>Práticas de produção

<!--The description in this section refers to preceding screen captures.-->

### <a name="auditing-geo-replicated-databases"></a>Auditoria de bancos de dados replicados geograficamente

Com bancos de dados com replicação geográfica, quando você habilitar a auditoria no banco de dados primário, o banco de dados secundário terá uma política de auditoria idêntica. Também é possível configurar a auditoria no banco de dados secundário, habilitando a auditoria no **servidor secundário**, independentemente do banco de dados primário.

- Nível do servidor (**recomendado**): ative a auditoria do **servidor primário** e do **servidor secundário** – os bancos de dados primário e secundário serão auditados independentemente, com base em sua respectiva política no nível do servidor.
- Nível de banco de dados: a auditoria de nível de banco de dados para bancos de dados secundários só pode ser configurada nas configurações de auditoria do banco de dados primário.
  - A auditoria precisa estar habilitada no *banco de dados primário*, não no servidor.
  - Depois que a auditoria estiver habilitada no banco de dados primário, ela também será habilitada no banco de dados secundário.

    >[!IMPORTANT]
    >Com a auditoria de nível de banco de dados, as configurações de armazenamento do banco de dados secundário serão idênticas às do banco de dados primário, causando um tráfego entre regiões. Recomendamos habilitar somente a auditoria no nível do servidor e deixar a auditoria no nível do banco de dados desabilitada para todos os bancos de dados.

### <a name="storage-key-regeneration"></a>Regeneração de chave de armazenamento

Em produção, você provavelmente atualizará suas chaves de armazenamento periodicamente. Ao gravar logs de auditoria no armazenamento do Azure, é necessário salvar novamente sua política de auditoria ao atualizar suas chaves. O processo é o seguinte:

1. Abra **Detalhes de Armazenamento**. Na caixa **Chave de Acesso de Armazenamento**, selecione **Secundária** e clique em **OK**. Em seguida, clique em **Salvar** na parte superior da página de configuração de auditoria.

    ![Painel de navegação](./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png)
2. Acesse a página de configuração de armazenamento e gere novamente a chave de acesso primária.

    ![Painel de navegação](./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png)
3. Volte para a página de configuração de auditoria, alterne a chave de acesso de armazenamento de secundária para primária e, depois, clique em **OK**. Em seguida, clique em **Salvar** na parte superior da página de configuração de auditoria.
4. Volte para a página de configuração de armazenamento e regenere a chave de acesso secundária (em preparação para o próximo ciclo de atualização da chave).

## <a name="manage-azure-sql-server-and-database-auditing"></a><a id="manage-auditing"></a>Gerenciar a auditoria de banco de dados e SQL Server do Azure

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

**Cmdlets do PowerShell (incluindo suporte para a cláusula WHERE da filtragem adicional)**:

- [Criar ou atualizar a política de auditoria do banco de dados (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Criar ou atualizar a política de auditoria do servidor (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Obter política de auditoria de banco de dados (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Obter política de auditoria de servidor (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Remover política de auditoria de banco de dados (remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Remover política de auditoria de servidor (remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Para obter um exemplo de script, confira [Configurar a auditoria e a detecção de ameaças usando o PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

### <a name="using-rest-api"></a>Usando a API REST

**API REST**:

- [Create or Update Database Auditing Policy](/rest/api/sql/database%20auditing%20settings/createorupdate) (Criar ou atualizar a política de auditoria de tabela do banco de dados)
- [Create or Update Server Auditing Policy](/rest/api/sql/server%20auditing%20settings/createorupdate) (Criar ou atualizar a política de auditoria de tabela do servidor)
- [Get Database Auditing Policy](/rest/api/sql/database%20auditing%20settings/get) (Obter a política de auditoria do banco de dados)
- [Get Server Auditing Policy](/rest/api/sql/server%20auditing%20settings/get) (Obter a política de auditoria do servidor)

Diretiva estendida com suporte em que a cláusula de filtragem adicional:

- [Criar ou atualizar a política de auditoria *estendida* do banco de dados](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Criar ou atualizar a política de auditoria *estendida* do servidor](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Obter política de auditoria *estendida* do banco de dados](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Obter política de auditoria *estendida* do servidor](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-resource-manager-templates"></a>Usando modelos do Gerenciador de Recursos do Azure

Você pode gerenciar a auditoria de banco de dados SQL do Azure usando os modelos do[ Azure Resource Manager](../azure-resource-manager/management/overview.md), de acordo com estes exemplos:

- [Implantar um SQL Server do Azure com auditoria habilitada para gravar logs de auditoria para a conta de armazenamento de Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Implantar um SQL Server do Azure comauditoria habilitada para gravar logs de auditoria no Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Implantar um SQL Server do Azure com auditoria habilitada para gravar logs de auditoria no Hubs de Eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Os exemplos vinculados estão em um repositório público externo e são fornecidos ' no estado em que se encontram ', sem garantia e não têm suporte em nenhum programa/serviço de suporte da Microsoft.
