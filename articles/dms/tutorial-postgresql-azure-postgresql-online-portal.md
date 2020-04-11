---
title: 'Tutorial: Migrar PostgreSQL para Azure DB para PostgreSQL on-line através do portal Azure'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line do PostgreSQL no local para o Banco de Dados Do Azure para PostgreSQL usando o Azure Database Migration Service através do portal Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: e01cc1c07d720c4743a03b5001e640f8b851dd5c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113994"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Tutorial: Migrar PostgreSQL para Azure DB para PostgreSQL on-line usando DMS através do portal Azure

Você pode usar o Azure Database Migration Service para migrar os bancos de dados de uma instância postgresql no local para [o Banco de Dados Do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/) com tempo mínimo de inatividade para o aplicativo. Neste tutorial, você vai migrar o banco de dados de exemplo **DVD Rental** de uma instância local do PostgreSQL 9.6 para o Banco de Dados do Azure para PostgreSQL usando uma atividade de migração online no Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Migre o esquema de amostra usando o utilitário pg_dump.
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração no Azure Database Migration Service.
> * Executar a migração.
> * Monitorar a migração.
> * Realize o corte de migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium. Criptografamos disco para evitar roubo de dados durante o processo de migração

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Baixe e [instale postgresql edição da comunidade](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 ou 10. A versão de origem do PostgreSQL Server deve ser 9.4, 9.5, 9.6, 10 ou 11. Para obter mais informações, consulte o artigo [Versões com suporte do Banco de Dados do PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Observe também que o banco de dados Azure de destino para a versão PostgreSQL deve ser igual ou posterior à versão postgreSQL no local. Por exemplo, o PostgreSQL 9.6 pode migrar para o Banco de Dados Azure para PostgreSQL 9.6, 10 ou 11, mas não para o Banco de Dados Azure para PostgreSQL 9.5.

* [Crie um banco de dados Azure para servidor PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) ou [Crie um banco de dados Azure para servidor PostgreSQL - Hyperscale (Citus).](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)
* Crie uma rede virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade local a local aos seus servidores de origem no local usando [expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual e, especialmente, os artigos de início rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com peering de rede para a Microsoft, adicione os [seguintes pontos finais de](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.

* Certifique-se de que as regras do Network Security Group (NSG) para sua rede virtual não bloqueiem as seguintes portas de comunicação de entrada para o Serviço de Migração de Banco de Dados Do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego DE Rede Virtual NSG, consulte o artigo [Filtrar](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)o tráfego da rede com grupos de segurança da rede .
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o servidor PostgreSQL de origem, que por padrão é a porta TCP 5432.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
* Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível de servidor para o Banco de Dados do Azure para PostgreSQL a fim de permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça a faixa de sub-rede da rede virtual usada para o Serviço de Migração de Banco de Dados do Azure.
* Habilite a replicação lógica no arquivo postgresql.config e defina os seguintes parâmetros:

  * wal_level = **lógico**
  * max_replication_slots = [número de slots]; é recomendável configurar como **cinco slots**
  * max_wal_senders =[número de tarefas simultâneas] – O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas; é recomendável definir como **10 tarefas**

> [!IMPORTANT]
> Todas as tabelas do banco de dados existentes precisam de uma chave primária para garantir que as alterações possam ser sincronizadas com o banco de dados de destino.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Para concluir todos os objetos de banco de dados, como procedimentos armazenados, índices e esquemas de tabela, é necessário extrair o esquema do banco de dados de origem e aplicar ao banco de dados.

1. Use o comando pg_dump -s para criar um arquivo de despejo de esquema para um banco de dados.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, para criar um arquivo de despejo de esquema para o banco de dados **de aluguel de dvd:**

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Para obter mais informações sobre como usar o utilitário pg_dump, confira os exemplos do tutorial [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Crie um banco de dados vazio no ambiente de destino, que é o Banco de Dados do Azure para PostgreSQL.

    Para obter detalhes sobre como conectar e criar um banco de dados, consulte o artigo [Criar um banco de dados Azure para servidor PostgreSQL no portal Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) ou Criar um banco de dados [Azure para servidor PostgreSQL - Hyperscale (Citus) no portal Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Uma instância do Banco de Dados Azure para PostgreSQL - Hyperscale (Citus) tem apenas um único banco de dados: **citus**.

3. Importe o esquema para o banco de dados de destino criado restaurando o arquivo de despejo do esquema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Por exemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Para extrair o script de chave estrangeira de queda e adicioná-lo no destino (Banco de Dados Azure para PostgreSQL), em PgAdmin ou em psql, execute o script a seguir.

   > [!IMPORTANT]
   > As teclas estrangeiras em seu esquema farão com que a carga inicial e a sincronização contínua da migração falhem.

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

6. Para desativar os gatilhos no banco de dados de destino, execute o script abaixo.

   > [!IMPORTANT]
   > Os gatilhos (inserir ou atualizar) nos dados reforçam a integridade dos dados no destino antes que os dados sejam replicados a partir da fonte. Como resultado, é recomendável desativar os gatilhos em todas as tabelas **no destino** durante a migração e, em seguida, reativar os gatilhos após a migração ser concluída.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Procure migração e, em seguida, à direita do **Microsoft.DataMigration,** selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Na tela **Criar serviço de migração,** especifique um nome, a assinatura, um grupo de recursos novo ou existente e a localização do serviço.

4. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso ao servidor PostgreSQL de origem e ao banco de dados Azure de destino para a instância PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual usando o portal Azure](https://aka.ms/DMSVnet).

5. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecione **'Revisar + criar** para criar o serviço'.

   A criação do serviço será concluída dentro de cerca de 10 a 15 minutos.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

      ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Na tela Serviços de migração de banco de dados do **Azure,** procure o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou, selecione a instância e selecione + **Novo Projeto de Migração**.

3. Na tela **do projeto Nova de migração,** especifique um nome para o projeto, na caixa de texto tipo servidor **Fonte,** selecione **PostgresSQL**, na caixa de texto **tipo servidor de destino,** selecione **Banco de dados Azure para PostgreSQL**.

4. Na seção **Escolher tipo de atividade,** selecione Migração de **dados on-line**.

    ![Criar projeto do Serviço de Migração de Banco de Dados do Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

5. Selecione **Salvar,** observe os requisitos para usar com sucesso o Azure Database Migration Service para migrar dados e, em seguida, selecione **Criar e executar atividades**.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Adicionar detalhes de origem,** especifique os detalhes da conexão para a instância PostgreSQL de origem.

    ![Tela Adicionar Detalhes da Origem](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **de detalhes do Destino,** especifique os detalhes de conexão para o servidor Citus (Target Hyperscale), que é a instância pré-provisionada de Hyperscale (Citus) para a qual o esquema **de Aluguel de DVD** foi implantado usando pg_dump.

    ![Tela de detalhes do destino](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    ![Mapa para a tela de bancos de dados de destino](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selecione **Salvar**e, em seguida, na tela **Configurações de Migração,** aceite os valores padrão.

    ![Tela de configurações de migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Tela de resumo da migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

    A janela de atividade de migração é exibida e o **Status** da atividade deve ser atualizado para mostrar como **backup em andamento**.

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Concluído**.

     ![Monitorar o processo de migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Quando a migração estiver concluída, em **Nome do Banco de Dados,** selecione um banco de dados específico para chegar ao status de migração para operações **completas de carga de dados** e **sincronização incremental de dados.**

   > [!NOTE]
   > **Carregamento de dados completo** mostra o status de migração da carga inicial, enquanto **Sincronização de dados incremental** mostra o status da CDA (captura de dados de alterações).

     ![Detalhes completos da carga de dados](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Detalhes incrementais de sincronização de dados](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do carregamento completo inicial, os bancos de dados são marcados como **Pronto para substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

2. Aguarde até que o contador **de alterações pendentes** mostre **0** para garantir que todas as transações recebidas no banco de dados de origem sejam interrompidas, selecione a caixa de seleção **Confirmar** e, em seguida, **selecione Aplicar**.

    ![Tela de corte completa](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando o status de migração do banco de dados **for concluído,** conecte seus aplicativos à nova instância de destino do Banco de Dados Azure para PostgreSQL.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre problemas conhecidos e limitações na realização de migrações online para o Banco de Dados do Azure para PostgreSQL, confira o artigo [Problemas conhecidos e soluções alternativas nas migrações online de Banco de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre o Banco de Dados do Azure para PostgreSQL, consulte o artigo [O que é o Banco de Dados do Azure para PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
