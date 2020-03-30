---
title: 'Tutorial: Migre o RDS SQL Server on-line para o Banco de Dados SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a executar uma migração on-line do RDS SQL Server para o Banco de Dados SQL do Azure ou instância gerenciada usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 8d538deca610fd9981d401d28b6bea1c31c6d4c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298865"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>Tutorial: Migre o RDS SQL Server para o Azure SQL Database ou uma instância gerenciada do Azure SQL On-line usando DMS
É possível usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do SQL Server do RDS para o [Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/) ou uma [instância gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) com tempo de inatividade mínimo. Neste tutorial, você migra o banco de dados **Adventureworks2012** restaurado para uma instância do SQL Server do RDS do SQL Server 2012 (ou posterior) para o Banco de Dados SQL do Azure ou uma instância gerenciada do Banco de Dados SQL do Azure, usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar uma instância do Banco de Dados SQL do Azure ou uma instância gerenciada do Banco de Dados SQL do Azure. 
> * Migre o esquema de exemplo usando o Assistente de Migração de Dados.
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.
> * Baixe um relatório de migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium. Para saber mais, confira a página de [preços](https://azure.microsoft.com/pricing/details/database-migration/) do Serviço de Migração de Banco de Dados do Azure.

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do SQL Server do RDS para o Banco de Dados SQL do Azure ou uma instância gerenciada do Banco de Dados SQL do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará:

* Criar um [banco de dados do SQL Server do RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* Crie uma instância do Banco de Dados SQL do Azure seguindo os detalhes do artigo [Criar um Banco de Dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).

    > [!NOTE]
    > Se você estiver migrando para uma instância gerenciada do Banco de Dados SQL do Azure, siga os detalhes do artigo [Criar uma instância gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) e, em seguida, crie um banco de dados vazio nomeado **AdventureWorks2012**. 
 
* Fazer download e instalar o [AMD](https://www.microsoft.com/download/details.aspx?id=53595) (Assistente de Migração de Dados) v3.3 ou posterior.
* Crie uma rede virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados Do Azure usando o modelo de implantação do Azure Resource Manager. Se você estiver migrando para uma instância gerenciada do Azure SQL Database, certifique-se de criar a instância DMS na mesma rede virtual usada para a instância gerenciada do Azure SQL Database, mas em uma sub-rede diferente.  Alternativamente, se você usar uma rede virtual diferente para DMS, você precisa criar uma rede virtual de peering entre as duas redes virtuais. Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual e, especialmente, os artigos de início rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com peering de rede para a Microsoft, adicione os [seguintes pontos finais de](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a Internet. 

* Certifique-se de que as regras do Grupo de Segurança de Rede de Rede de rede virtual não bloqueiem as seguintes portas de comunicação de entrada para o Serviço de Migração de Banco de Dados Do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego DE Rede Virtual NSG, consulte o artigo [Filtrar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)o tráfego da rede com grupos de segurança da rede .
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que por padrão é porta TCP 1433.
* Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível de servidor para o servidor do Banco de Dados SQL do Azure para permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça a faixa de sub-rede da rede virtual usada para o Serviço de Migração de Banco de Dados do Azure.
* Verifique se as credenciais usadas para se conectar à instância do SQL Server do RDS de origem estão associadas a uma conta que é membro da função de servidor “Processadmin” e um membro das funções de banco de dados “db_owner” em todos os bancos de dados que devem ser migrados.
* Verifique se as credenciais usadas para conectar a instância do Banco de Dados SQL do Azure de destino têm a permissão CONTROL DATABASE nos bancos de dados SQL de destino do Azure e um membro da função sysadmin, se estiver migrando para uma instância gerenciada do Banco de Dados SQL do Azure.
* A versão do SQL Server de origem do RDS deve ser o SQL Server 2012 e posterior. Para determinar a versão que a instância do SQL Server está executando, consulte o artigo [Como determinar a versão, a edição e o nível de atualização do SQL Server e de seus componentes](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Habilite o CDA (Captura de Dados de Alterações) no banco de dados do SQL Server do RDS e em todas as tabelas do usuário selecionadas para migração.
    > [!NOTE]
    > É possível usar o script abaixo para habilitar o CDA em um banco de dados do SQL Server do RDS.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > É possível usar o script abaixo para habilitar o CDA em todas as tabelas.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* Desabilite gatilhos de banco de dados no Banco de Dados SQL do Azure de destino.
    > [!NOTE]
    > Você pode encontrar os gatilhos de banco de dados no Banco de Dados SQL do Azure de destino usando a seguinte consulta:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Para obter mais informações, consulte o artigo [DESABILITAR GATILHO (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo
Use o AMD para migrar o esquema para o Banco de Dados SQL do Azure.

> [!NOTE]
> Antes de criar um projeto de migração no AMD, verifique se você já possui um banco de dados SQL do Azure conforme mencionado nos pré-requisitos. Para fins deste tutorial, o nome do Banco de Dados SQL do Azure é considerado **AdventureWorks2012**, mas é possível renomeá-lo como desejar.

Para migrar o **AdventureWorks2012** esquema de banco de dados SQL do Azure, execute as seguintes etapas:

1. No Assistente de Migração de Dados, selecione o ícone de Novo (+) e, em seguida, em **Tipo de projeto**, selecione **Migração**.
2. Especifique um nome de projeto, no **tipo de servidor de origem** caixa de texto, selecione **do SQL Server**e, em seguida, no **tipo de servidor de destino** caixa de texto, selecione **Banco de Dados SQL do Azure**.
3. Em **escopo migração**, selecione **somente esquema**.

    Depois de executar as etapas anteriores, a interface do AMD deve aparecer como mostrado no gráfico abaixo:

    ![Novo projeto do assistente de migração de dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Selecione **Criar** para criar o cluster.
5. No AMD, especifique os detalhes de conexão de origem para o SQL Server, selecione **Conectar**e selecione o banco de dados **AdventureWorks2012**.

    ![Detalhes de Conexão de fonte de Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Selecione **Avançar**; em **Conectar ao servidor de destino**, especifique os detalhes de conexão de destino para o Banco de Dados SQL do Azure, selecione **Conectar**e selecione o banco de dados **AdventureWorksAzure** que você tinha provisionado previamente no Banco de Dados SQL do Azure.

    ![Detalhes de Conexão de destino Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Selecione **próximo** para ir para o **selecionar objetos** tela, em que você pode especificar os objetos de esquema no **AdventureWorks2012** banco de dados que precisam ser implantados no Banco de Dados SQL do Azure.

    Por padrão, todos os discos são selecionados.

    ![Gerar Scripts SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Selecione **script SQL gerar** para criar scripts SQL e, em seguida, examine os scripts de erros.

    ![Script de esquema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Selecione **implantar esquema** para implantar o esquema de banco de dados SQL do Azure e, em seguida, depois que o esquema é implantado, verifique o servidor de destino para todas as anomalias.

    ![Implantar o esquema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Selecione a assinatura na qual você deseja criar a instância do Serviço de Migração do Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Procure migração e, em seguida, à direita do **Microsoft.DataMigration,** selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Escolher uma instância

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione o local no qual você deseja criar a instância do Serviço de Migração de Banco de Dados do Azure. 

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso ao SQL Server de origem e à instância de banco de dados SQL do Azure.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual usando o portal Azure](https://aka.ms/DMSVnet).

6. Selecione um tipo de preço; para esta migração online, selecione o tipo de preço Premium.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

     ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

      ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Na tela dos **Serviços de Migração de Banco de Dados do Azure**, procure o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou e, em seguida, selecione a instância.

     ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. Na tela **Novo projeto de migração**, especifique um nome para o projeto; na caixa de texto **Tipo de servidor de origem**, selecione **AWS RDS para SQL Server** e, na caixa de texto **Tipo de servidor de destino**, selecione **Banco de Dados SQL do Azure**.

    > [!NOTE]
    > Para o tipo de servidor de Destino, selecione **Banco de Dados SQL do Azure** para migrar para um banco de dados singleton do Banco de Dados SQL do Azure e para uma instância gerenciada do Banco de Dados SQL do Azure.

5. Na seção **Escolher tipo de atividade,** selecione Migração de **dados on-line**.

    > [!IMPORTANT]
    > Verifique se você selecionou **Migração de dados online**; não há suporte para migrações offline para esse cenário.

    ![Criar o Serviço de migração de banco de dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

6. Selecione **Salvar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    ![Criar e executar a atividade do Serviço de Migração de Banco de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão da instância do SQL Server de origem.

    Certifique-se de usar um Nome de Domínio Totalmente Qualificado (FQDN) para o nome da instância do SQL Server de origem.

2. Se você não tiver instalado um certificado confiável no servidor de origem, marque a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões TLS que são criptografadas usando um certificado auto-assinado não fornecem forte segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no TLS usando certificados auto-assinados em um ambiente de produção ou em servidores conectados à internet.

   ![Detalhes da origem](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Selecione **Salvar**e, na tela **Detalhes de destino da migração**, especifique os detalhes de conexão para o destino do Servidor de Banco de Dados SQL do Azure, que é o Banco de Dados SQL do Azure pré-provisionado no qual o esquema do **AdventureWorks2012 ** foi implantado usando o AMD.

    ![Selecionar o destino](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contém o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure seleciona o banco de dados de destino por padrão.

    ![Mapear para bancos de dados de destino](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Selecione **Salvar**, na tela **Selecionar tabelas**, expanda a lista de tabela e revise a lista de campos afetados.

    O Serviço de Migração de Banco de Dados do Azure seleciona automaticamente todas as tabelas de origem que existem na instância do Banco de Dados SQL do Azure de destino. Se desejar migrar novamente as tabelas que já contêm dados, será necessário selecionar as tabelas nesta tela de forma explícita.

    ![Selecionar tabelas](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Selecione **Salvar** após definir as seguintes **Configurações avançadas de migração online**.

    | Configuração | Descrição |
    | ------------- | ------------- |
    | **Número máximo de tabelas a ser carregado em paralelo** | Especifica o número de tabelas que o DMS executa em paralelo durante a migração. O valor padrão é 5, mas pode ser definido como um valor ideal para atender às necessidades de migração específicas com base em quaisquer migrações de POC. |
    | **Quando a tabela de origem é truncada** | Especifica se o DMS trunca a tabela de destino durante a migração. Essa configuração poderá ser útil se uma ou mais tabelas forem truncadas como parte do processo de migração. |
    | **Definir configurações para dados LOB (objetos grandes)** | Especifica se o DMS migra dados LOB ilimitados ou limita os dados LOB migrados a um tamanho específico.  Quando houver um limite nos dados LOB migrados, todos os dados LOB além desse limite serão truncados. Para migrações de produção, é recomendável selecionar **Permitir tamanho de LOB ilimitado** para impedir a perda de dados. Ao especificar essa opção para permitir o tamanho ilimitado de LOB, marque a caixa de seleção **Migrar dados LOB em um único bloco quando o tamanho do LOB for menor que (KB) especificado** para melhorar o desempenho. |

    ![Definir configurações avançadas de migração online](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Resumo do Aplicativo](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

    A janela de atividade de migração é exibida e o **Status** da atividade é **Inicialização**.

    ![Status da atividade: inicializando](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Em execução**.

2. Clique em um banco de dados específico para obter o status de migração das operações **Carregamento de dados completo** e **Sincronização de dados incremental**.

    ![Status da atividade: em andamento](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do carregamento completo inicial, os bancos de dados são marcados como **Pronto para substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

    ![Iniciar substituição](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. Pare todas as transações de entrada para o banco de dados de origem; aguarde até que o contador **Alterações pendentes** contador mostre **0**.
3. Selecione **Confirmar**e selecione **Aplicar**.
4. Quando o status de migração de banco de dados mostrar **Concluído**, conecte seus aplicativos ao novo Banco de Dados SQL do Azure de destino.

    ![Status da atividade: concluído](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre problemas conhecidos e limitações na realização de migrações online para o Banco de Dados SQL do Azure, confira o artigo [Problemas conhecidos e soluções alternativas nas migrações online de Banco de Dados SQL do Azure](known-issues-azure-sql-online.md).
* Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para saber mais sobre o Banco de Dados SQL do Azure, confira o artigo [O que é o serviço Banco de Dados SQL do Azure?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
* Para obter mais informações sobre instâncias gerenciadas do Banco de Dados SQL do Azure, consulte a página [Instância Gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index).
