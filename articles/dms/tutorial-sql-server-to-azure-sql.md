---
title: 'Tutorial: Migrar o SQL Server offline para um banco de dados individual do SQL'
titleSuffix: Azure Database Migration Service
description: Saiba como migrar do SQL Server para o Banco de Dados SQL do Azure offline usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 1027b4f37160281bcf298e57e890b73b472526a4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308751"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>Tutorial: Migrar SQL Server para Banco de Dados SQL do Azure offline usando DMS

Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do SQL Server para o [Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/). Neste artigo, você migra o banco de dados **Adventureworks2012** restaurado em uma instância local do SQL Server 2016 (ou posterior) para um banco de dados individual ou um banco de dados em pool no Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> - Avalie seu banco de dados local usando o Assistente de Migração de Dados.
> - Migre o esquema de exemplo usando o Assistente de Migração de Dados.
> - Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> - Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> - Executar a migração.
> - Monitorar a migração.
> - Baixe um relatório de migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline do SQL Server para um banco de dados no Banco de Dados SQL do Azure. Para uma migração online, confira [Migrar o SQL Server para o Banco de Dados SQL do Azure online usando DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

- Baixar e instalar o [SQL Server 2016 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads).
- Habilitar o protocolo TCP/IP, que está desabilitado por padrão durante a instalação do SQL Server Express, seguindo as instruções no artigo [Habilitar ou desabilitar um protocolo de rede de servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Crie um banco de dados no Banco de Dados SQL do Azure seguindo os detalhes do artigo [Criar um banco de dados no Banco de Dados SQL do Azure usando o portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Se você usar o SSIS (SQL Server Integration Services) e desejar migrar o banco de dados de catálogo de seus projetos/pacotes SSIS (SSISDB) do SQL Server para o Banco de Dados SQL do Azure, o SSISDB de destino será criado e gerenciado automaticamente em seu nome quando você provisionar o SSIS no ADF (Azure Data Factory). Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).
  
- Fazer download e instalar o [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
- Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](https://docs.microsoft.com/azure/virtual-network/) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com emparelhamento de rede com a Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > - Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > - Ponto de extremidade de armazenamento
    > - Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.
    >
    >Se você não tiver conectividade site a site entre a rede local e o Azure ou se houver uma largura de banda de conectividade site a site limitada, considere usar o Serviço de Migração de Banco de Dados do Azure no modo híbrido (versão prévia). O modo híbrido beneficia-se de um trabalho de migração local junto com uma instância do Serviço de Migração de Banco de Dados do Azure em execução na nuvem. Para criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido, confira o artigo [Criar uma instância do Serviço de Migração de Banco de Dados do Azure no modo híbrido usando o portal do Azure](https://aka.ms/dms-hybrid-create).

- Verifique se as regras do Grupo de Segurança de Rede da rede virtual não bloqueiam as seguintes portas de comunicação de entrada do Serviço de Migração de Banco de Dados do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego do NSG na rede virtual do Azure, confira o artigo [Filtrar o tráfego de rede com os grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que, por padrão, é a porta TCP 1433.
- Se você estiver executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez precise habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio dos firewalls, de modo que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada no servidor de origem.
- Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
- Crie uma [regra de firewall de IP](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível do servidor para o Banco de Dados SQL do Azure a fim de permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça o intervalo de sub-redes da rede virtual usado para o Serviço de Migração de Banco de Dados do Azure.
- Garantir que as credenciais usadas para se conectar à instância de origem do SQL Server tenham as permissões [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Verifique se as credenciais usadas para se conectar à instância do Banco de Dados SQL do Azure de destino têm a permissão CONTROL DATABASE sobre os bancos de dados de destino.

## <a name="assess-your-on-premises-database"></a>Adicionar um Banco de Dados Local

Para migrar os dados de uma instância do SQL Server para um banco de dados individual ou em pool no Banco de Dados SQL do Azure, examine o banco de dados do SQL Server em busca de problemas de bloqueio que possam impedir a migração. Usando o Assistente de Migração de Dados v3.3 ou posterior, siga as etapas descritas no artigo [Realizando uma avaliação de migração do SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) para concluir a avaliação de banco de dados local. Segue um resumo das etapas necessárias:

1. No Assistente de Migração de Dados, selecione o ícone de novo (+) e, em seguida, selecione o **avaliação** tipo de projeto.
2. Especifique um nome de projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**, na caixa de texto**Tipo de servidor de destino**, selecione **Banco de Dados SQL do Azure** e, em seguida, selecione **Criar** para criar o projeto.

    Quando você estiver avaliando o banco de dados do SQL Server de origem migrando para um banco de dados individual ou em pool no Banco de Dados SQL do Azure, poderá escolher um ou ambos os seguintes tipos de relatórios de avaliação:

   - Determinar compatibilidade do banco de dados
   - Verificação de paridade de recursos

    Ambos os tipos de relatório são selecionados por padrão.

3. No Assistente de Migração de Dados, sobre o **opções** tela, selecione **próximo**.
4. No **selecione fontes** tela, o **conectar a um servidor** caixa de diálogo, forneça os detalhes de conexão ao SQL Server e, em seguida, selecione **conectar**.
5. Na caixa de diálogo **Adicionar origens**, selecione **AdventureWorks2012**, selecione **Adicionar** e, em seguida, selecione **Iniciar Avaliação**.

    > [!NOTE]
    > Se você usa o SSIS, no momento o AMD não dá à avaliação do SSISDB de origem. No entanto, os projetos/pacotes SSIS serão avaliados/validados conforme forem reimplantados no SSISDB de destino hospedado pelo Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Quando a avaliação for concluída, os resultados exibem conforme mostrado no gráfico a seguir:

    ![Gerenciar a migração de dados](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Para os bancos de dados do Banco de Dados SQL do Azure, as avaliações identificam problemas de paridade de recursos e de bloqueio de migração para implantar em um banco de dados individual ou em pool.

    - A **categoria de paridade de recursos do SQL Server** fornece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e etapas atenuantes para ajudá-lo a planejar o esforço em seus projetos de migração.
    - A categoria **Problemas de compatibilidade** identifica recursos não compatíveis ou parcialmente compatíveis que refletem os problemas de compatibilidade capazes de bloquear a migração dos bancos de dados do SQL Server para o Banco de Dados SQL do Azure. Recomendações também são fornecidas para ajudá-lo a resolver esses problemas.

6. Examine os resultados de avaliação para problemas de bloqueio de migração e paridade recurso selecionando as opções específicas.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Depois que você estiver familiarizado com a avaliação e satisfeito de que o banco de dados selecionado é um candidato viável para a migração para um banco de dados individual ou em pool no Banco de Dados SQL do Azure, use o AMD para migrar o esquema para o Banco de Dados SQL do Azure.

> [!NOTE]
> Antes de criar um projeto de migração no Assistente de Migração de Dados, verifique se você já provisionou um banco de dados no Azure, conforme mencionado nos pré-requisitos. Para fins deste tutorial, o nome do Banco de Dados SQL do Azure é considerado **AdventureWorksAzure**, mas você poderá renomeá-lo como desejar.

> [!IMPORTANT]
> Se você usa o SSIS, o AMD não dá suporte no momento à migração do SSISDB de origem, mas você pode reimplantar seus projetos/pacotes SSIS no SSISDB de destino hospedado pelo Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Para migrar o esquema **AdventureWorks2012** para um banco de dados individual ou em pool do Banco de Dados SQL do Azure, siga estas etapas:

1. No Assistente de Migração de Dados, selecione o ícone de Novo (+) e, em seguida, em **Tipo de projeto**, selecione **Migração**.
2. Especifique um nome de projeto, no **tipo de servidor de origem** caixa de texto, selecione **do SQL Server**e, em seguida, no **tipo de servidor de destino** caixa de texto, selecione **Banco de Dados SQL do Azure**.
3. Em **escopo migração**, selecione **somente esquema**.

    Depois de executar as etapas anteriores, a interface do Assistente de Migração de Dados deve aparecer como mostrado no gráfico a seguir:

    ![Novo projeto do assistente de migração de dados](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Selecione **Criar** para criar o cluster.
5. No Assistente de migração de dados, especifique os detalhes de conexão de origem para o SQL Server, selecione **conectar**e, em seguida, selecione o **AdventureWorks2012** banco de dados.

    ![Detalhes de Conexão de fonte de Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Selecione **Avançar**; em **Conectar ao servidor de destino**, especifique os detalhes de conexão de destino para o Banco de Dados SQL do Azure, selecione **Conectar**e selecione o banco de dados **AdventureWorksAzure** que você tinha provisionado previamente no Banco de Dados SQL do Azure.

    ![Detalhes de Conexão de destino Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Selecione **próximo** para ir para o **selecionar objetos** tela, em que você pode especificar os objetos de esquema no **AdventureWorks2012** banco de dados que precisam ser implantados no Banco de Dados SQL do Azure.

    Por padrão, todos os discos são selecionados.

    ![Gerar Scripts SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Selecione **script SQL gerar** para criar scripts SQL e, em seguida, examine os scripts de erros.

    ![Script de esquema](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Selecione **implantar esquema** para implantar o esquema de banco de dados SQL do Azure e, em seguida, depois que o esquema é implantado, verifique o servidor de destino para todas as anomalias.

    ![Implantar o esquema](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure. Pesquise e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Pesquise por migração, depois selecione **Registrar** para **Microsoft.DataMigration**.

    ![Registrar provedor de recursos](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Escolher uma instância

1. No menu do portal do Azure ou na **Home page**, selecione **Criar um recurso**. Pesquise e selecione o **Serviço de Migração de Banco de Dados do Azure**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione a localização na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao SQL Server de origem e à instância do Banco de Dados SQL do Azure de destino.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. No menu do portal do Azure, selecione **Todos os serviços**. Pesquise e selecione os **Serviços de Migração de Banco de Dados do Azure**.

     ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Na tela dos **Serviços de Migração de Banco de Dados do Azure**, selecione a instância do Serviço de Migração de Banco de Dados do Azure que você criou.

3. Selecione **Novo projeto de migração**.

     ![Localizar a instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**; na caixa de texto **Tipo de servidor de destino**, selecione **Banco de Dados SQL do Azure** e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**.

    ![Criar o Serviço de migração de banco de dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão da instância do SQL Server de origem.

    Certifique-se de usar um Nome de Domínio Totalmente Qualificado (FQDN) para o nome da instância do SQL Server de origem. Você também pode usar o endereço IP para situações em que a resolução de nome do DNS não é possível.

2. Se você não tiver instalado um certificado confiável no servidor de origem, selecione a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões TLS que são criptografadas usando um certificado autoassinado não oferecem alta segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no TLS usando certificados autoassinados em um ambiente de produção, nem em servidores conectados à Internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Se você usa o SSIS, o DMS não dá suporte no momento à migração do SSISDB de origem, mas você pode reimplantar seus projetos/pacotes SSIS no SSISDB de destino hospedado pelo Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Selecione **Salvar** e, na tela **Detalhes do destino de migração**, especifique os detalhes da conexão com o Banco de Dados SQL do Azure de destino, que é o Banco de Dados SQL do Azure pré-provisionado para o qual o esquema do **AdventureWorks2012** foi implantado usando o Assistente de Migração de Dados.

    ![Selecionar o destino](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    ![Mapear para bancos de dados de destino](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Selecione **Salvar**, na tela **Selecionar tabelas**, expanda a lista de tabela e revise a lista de campos afetados.

    O Serviço de Migração de Banco de Dados do Azure seleciona automaticamente todas as tabelas de origem que existem na instância do Banco de Dados SQL do Azure de destino. Se quiser migrar novamente as tabelas que já contêm dados, você precisará selecionar as tabelas nesta folha de forma explícita.

    ![Selecionar tabelas](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Selecione **salvar**, no **resumo de migração** tela, o **nome da atividade** texto, especifique um nome para a atividade de migração.

5. Expanda a seção **Opção de validação** para exibir a tela **Escolher a opção de validação** e especifique se é preciso validar os bancos de dados migrados para **Comparação do esquema**, a **Consistência dos dados** e a **Exatidão da consulta**.

    ![Escolha a opção de validação](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. Selecione **salvar**, revise o resumo para garantir que os detalhes de origem e destino correspondam especificado anteriormente.

    ![Resumo do Aplicativo](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>Execute a migração

- Selecione **Executar migração**.

    A janela de atividade de migração aparece e o **Status** da atividade está **Pendente**.

    ![Status da Atividade](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Concluído**.

    ![Status da atividade concluído](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Após a conclusão da migração, selecione **Baixar relatório** para obter um relatório que lista os detalhes associados ao processo de migração.

3. Verifique os bancos de dados de destino no Banco de Dados SQL do Azure de destino.

### <a name="additional-resources"></a>Recursos adicionais

- Laboratório prático da [migração do SQL usando o Serviço de Migração de Banco de Dados do Azure](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).
- Para obter informações sobre problemas conhecidos e limitações na realização de migrações online para o Banco de Dados SQL do Azure, confira o artigo [Problemas conhecidos e soluções alternativas nas migrações online de Banco de Dados SQL do Azure](known-issues-azure-sql-online.md).
- Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, confira o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
- Para saber mais sobre o Banco de Dados SQL do Azure, confira o artigo [O que é o serviço Banco de Dados SQL do Azure?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
