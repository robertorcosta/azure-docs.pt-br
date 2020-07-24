---
title: 'Tutorial: migrar o BD do Azure para PostgreSQL para o BD do Azure para PostgreSQL online por meio do portal do Azure'
titleSuffix: Azure Database Migration Service
description: Saiba como executar uma migração online de um BD do Azure para PostgreSQL para outro banco de dados do Azure para PostgreSQL usando o serviço de migração de banco de dados do Azure por meio do portal do Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: 6a5415e12a5a063790077eeefdc9ea4d1487d68b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096081"
---
# <a name="tutorial-migrate-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server-or-hyperscale-citus-online-using-dms-via-the-azure-portal"></a>Tutorial: migrar o BD do Azure para PostgreSQL-servidor único para BD do Azure para PostgreSQL-servidor único ou Citus (hiperescala) online usando DMS por meio do portal do Azure

Você pode usar o serviço de migração de banco de dados do Azure para migrar os bancos de dados de uma instância de [servidor único para PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) [(Citus) no banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) com tempo de inatividade mínimo. Neste tutorial, você migra o banco de dados de exemplo de **aluguel de DVD** de um banco de dados do Azure para PostgreSQL V10 para hiperscale (Citus) no banco de dados do Azure para PostgreSQL usando a atividade de migração online no serviço de migração de banco de dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Migre o esquema de exemplo usando o utilitário pg_dump.
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração no serviço de migração de banco de dados do Azure.
> * Executar a migração.
> * Monitorar a migração.
> * Execute a transferência de migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium. Criptografamos o disco para evitar roubo de dados durante o processo de migração

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

> [!IMPORTANT]
> A migração do banco de dados do Azure para PostgreSQL tem suporte para PostgreSQL versão 10 e posterior. Você também pode usar este tutorial para migrar de uma instância do banco de dados do Azure para PostgreSQL para outra instância do banco de dados do Azure para PostgreSQL ou de Citus (hiperescala).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Verifique o [status dos cenários de migração com suporte pelo serviço de migração de banco de dados do Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status) para combinações de migração e versão com suporte. 
* Uma instância existente do [banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/) versão 10 e posterior com o banco de dados de **aluguel de DVD** . O serviço de migração de banco de dados do Azure não oferece suporte à migração do BD do Azure para PostgreSQL 9,5 ou 9,6.

    Observe também que a versão do banco de dados do Azure para PostgreSQL de destino deve ser igual ou posterior à versão do PostgreSQL local. Por exemplo, o PostgreSQL 10 pode migrar para o banco de dados do Azure para PostgreSQL 10 ou 11, mas não para o banco de dados do Azure para PostgreSQL 9,6.

* [Crie um servidor de banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) ou [crie um servidor de banco de dados do Azure para PostgreSQL-Citus (hiperescala)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal) como o servidor de banco de dados de destino para o qual migrar os dados.
* Crie um Rede Virtual do Microsoft Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager. Para obter mais informações sobre como criar uma rede virtual, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.

* Verifique se as regras do NSG (grupo de segurança de rede) para sua rede virtual não bloqueiam as seguintes portas de comunicação de entrada para o serviço de migração de banco de dados do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG de rede virtual, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível de servidor para a origem do banco de dados do Azure para PostgreSQL para permitir que o serviço de migração de banco de dados do Azure acesse os bancos de os. Forneça o intervalo de sub-rede da rede virtual usada para o serviço de migração de banco de dados do Azure.
* Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível do servidor para o banco de dados do Azure para PostgreSQL para permitir que o serviço de migração de banco de dados do Azure acesse os bancos de dados de destino. Forneça o intervalo de sub-rede da rede virtual usada para o serviço de migração de banco de dados do Azure.
* Defina os seguintes parâmetros de servidor na instância do banco de dados do Azure para PostgreSQL que está sendo usada como fonte:

  * max_replication_slots = [número de slots]; é recomendável configurar como **cinco slots**
  * max_wal_senders =[número de tarefas simultâneas] – O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas; é recomendável definir como **10 tarefas**

> [!NOTE]
> Os parâmetros de servidor acima são estáticos e exigirão uma reinicialização da instância do banco de dados do Azure para PostgreSQL para que eles entrem em vigor. Para obter mais informações sobre como alternar parâmetros de servidor, consulte [configurar parâmetros do servidor do banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal).

> [!IMPORTANT]
> Todas as tabelas no banco de dados existente precisam de uma chave primária para garantir que as alterações possam ser sincronizadas com o banco de dados de destino.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Para concluir todos os objetos de banco de dados, como procedimentos armazenados, índices e esquemas de tabela, é necessário extrair o esquema do banco de dados de origem e aplicar ao banco de dados.

1. Use o comando pg_dump -s para criar um arquivo de despejo de esquema para um banco de dados.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, para criar um arquivo de despejo de esquema para o banco de dados **dvdrental** :

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Para obter mais informações sobre como usar o utilitário pg_dump, confira os exemplos do tutorial [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Crie um banco de dados vazio no ambiente de destino, que é o Banco de Dados do Azure para PostgreSQL.

    Para obter detalhes sobre como se conectar e criar um banco de dados, consulte o artigo [criar um banco de dados do Azure para o servidor PostgreSQL no portal do Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) ou [criar um banco de dados do Azure para PostgreSQL-Citus (servidor de hiperescala) no portal do Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Uma instância do banco de dados do Azure para PostgreSQL-Citus (hiperescala) tem apenas um banco de dados: **Citus**.

3. Importe o esquema para o banco de dados de destino criado restaurando o arquivo de despejo do esquema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Por exemplo:

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Para extrair o script drop Foreign Key e adicioná-lo ao destino (banco de dados do Azure para PostgreSQL), em PgAdmin ou em psql, execute o script a seguir.

   > [!IMPORTANT]
   > As chaves estrangeiras em seu esquema farão com que a carga inicial e a sincronização contínua da migração falhem.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Execute a remoção de chave estrangeira (que é a segunda coluna) no resultado da consulta.

6. Para desabilitar gatilhos no banco de dados de destino, execute o script abaixo.

   > [!IMPORTANT]
   > Os gatilhos (Insert ou Update) nos dados impõem a integridade dos dados no destino à frente dos dados que estão sendo replicados da origem. Como resultado, é recomendável que você desabilite os gatilhos em todas as tabelas **no destino durante a** migração e, em seguida, habilite novamente os gatilhos após a conclusão da migração.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Na tela **Criar serviço de migração** , especifique um nome, a assinatura, um grupo de recursos novo ou existente e o local para o serviço.

4. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece o serviço de migração de banco de dados do Azure com acesso ao servidor PostgreSQL de origem e à instância de destino do banco de dados do Azure para PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

5. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecione **examinar + criar** para criar o serviço.

   A criação do serviço será concluída em cerca de 10 a 15 minutos.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

      ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Na tela **serviços de migração de banco de dados do Azure** , procure o nome da instância do serviço de migração de banco de dados do Azure que você criou, selecione a instância e, em seguida, selecione + **novo projeto de migração**.

3. Na tela **novo projeto de migração** , especifique um nome para o projeto, na caixa de texto **tipo de servidor de origem** , selecione **PostgreSQL**, na caixa de texto tipo de **servidor de destino** , selecione **banco de dados do Azure para PostgreSQL**.
    > [!NOTE]
    > Escolha **PostgreSQL** no **tipo de servidor de origem** , embora o servidor de origem seja uma instância do **banco de dados do Azure para PostgreSQL** .  

4. Na seção **escolher tipo de atividade** , selecione **migração de dados online**.

    ![Criar projeto de serviço de migração de banco de dados do Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

5. Selecione **salvar**, observe os requisitos para usar o serviço de migração de banco de dados do Azure para migrar com êxito e, em seguida, selecione **criar e executar atividade**.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **adicionar detalhes de origem** , especifique os detalhes de conexão para a instância PostgreSQL de origem.

    ![Tela Adicionar Detalhes da Origem](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Você pode encontrar detalhes como "nome do servidor", "porta do servidor", "nome do banco de dados", etc. no portal do **banco de dados do Azure para PostgreSQL** .

2. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **detalhes de destino** , especifique os detalhes de conexão para o servidor de hiperescala de destino (Citus), que é a instância pré-configurada de hiperescala (Citus) na qual o esquema de **locações de DVD** foi implantado usando pg_dump.

    ![Tela de detalhes do destino](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > Você pode migrar de uma instância do banco de dados do Azure para PostgreSQL para outro banco de dados do Azure para PostgreSQL instância de servidor único ou para um servidor de hiperescala (Citus).

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    ![Tela mapear para bancos de dados de destino](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selecione **salvar**e, em seguida, na tela **configurações de migração** , aceite os valores padrão.

    ![Tela de configurações de migração](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Tela de resumo da migração](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

    A janela atividade de migração é exibida e o **status** da atividade deve ser atualizado para ser exibido como **backup em andamento**.

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Concluído**.

     ![Monitorar processo de migração](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Quando a migração for concluída, em **nome do banco**de dados, selecione um banco de dado específico para obter o status de migração para operações de sincronização de dados completas e de **carregamento de dados** **incremental** .

   > [!NOTE]
   > **Carregamento de dados completo** mostra o status de migração da carga inicial, enquanto **Sincronização de dados incremental** mostra o status da CDA (captura de dados de alterações).

     ![Detalhes completos do carregamento de dados](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Detalhes de sincronização de dados incremental](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do carregamento completo inicial, os bancos de dados são marcados como **Pronto para substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

2. Aguarde até que o contador de **alterações pendentes** mostre **0** para garantir que todas as transações de entrada para o banco de dados de origem sejam interrompidas, marque a caixa de seleção **confirmar** e, em seguida, selecione **aplicar**.

    ![Tela concluir a transferência](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando o status de migração do banco de dados mostrar **concluído**, conecte seus aplicativos à nova instância de destino do banco de dados do Azure para PostgreSQL.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre problemas conhecidos e limitações na realização de migrações online para o Banco de Dados do Azure para PostgreSQL, confira o artigo [Problemas conhecidos e soluções alternativas nas migrações online de Banco de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre o Banco de Dados do Azure para PostgreSQL, consulte o artigo [O que é o Banco de Dados do Azure para PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
