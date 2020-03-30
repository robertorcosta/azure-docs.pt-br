---
title: 'Tutorial: Migrar sql server para instância gerenciada SQL'
titleSuffix: Azure Database Migration Service
description: Saiba como migrar do SQL Server local para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 416be7de4b3cef4fb6e1bcfd09d934937f8c96d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297720"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Tutorial: Migre o SQL Server para uma instância gerenciada do Azure SQL Database offline usando DMS

Use o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do SQL Server local para uma [Instância Gerenciada do Banco de Dados SQL do Azure](../sql-database/sql-database-managed-instance.md). Para obter métodos adicionais que possam exigir algum esforço manual, consulte a migração da [instância do SQL Server do artigo para a instância gerenciada do Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

Neste tutorial, você migrará o banco de dados **Adventureworks2012** de uma instância local do SQL Server para uma Instância Gerenciada do Banco de Dados SQL usando o Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> - Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> - Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> - Executar a migração.
> - Monitorar a migração.
> - Baixe um relatório de migração.

> [!IMPORTANT]
> Para migrações offline do SQL Server para uma Instância Gerenciada do Banco de Dados SQL, o Serviço de Migração de Banco de Dados do Azure pode criar os arquivos de backup para você. Como alternativa, você pode fornecer o backup de banco de dados completo mais recente no compartilhamento de rede SMB que o serviço usará para migrar os bancos de dados. Não acrescente vários backups em uma única mídia de backup; faça cada backup em um arquivo de backup separado. Observe que você pode usar backups compactados também, a fim de reduzir a probabilidade de ocorrência de possíveis problemas com a migração de backups grandes.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline do SQL Server para uma Instância Gerenciada do Banco de Dados SQL. Para uma migração online, confira [Migrar o SQL Server para uma instância gerenciada do Banco de Dados SQL do Azure online usando DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

- Crie uma rede virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade local a local aos seus servidores de origem no local usando [expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) [Saiba mais sobre as topologias de rede para migrações da instância gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual e, especialmente, os artigos de início rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com peering de rede para a Microsoft, adicione os [seguintes pontos finais de](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) serviço à sub-rede na qual o serviço será provisionado:
    > - Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > - Ponto de extremidade de armazenamento
    > - Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.

- Certifique-se de que as regras do Grupo de Segurança de Rede de Rede de rede virtual não bloqueiem as seguintes portas de comunicação de entrada para o Serviço de Migração de Banco de Dados Do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego DE Rede Virtual NSG, consulte o artigo [Filtrar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)o tráfego da rede com grupos de segurança da rede .
- Configure o [Firewall do Windows para acesso ao mecanismo de banco de dados de fonte](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que, por padrão, é a porta TCP 1433.
- Se você estiver executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez precise habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio dos firewalls, de modo que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada no servidor de origem.
- Se você estiver usando um dispositivo de firewall na frente dos bancos de dados de origem, talvez precise adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração, bem como arquivos por meio da porta SMB 445.
- Crie uma instância gerenciada do Azure SQL Database seguindo os detalhes do artigo [Criar uma instância gerenciada do Azure SQL Database no portal Azure](https://aka.ms/sqldbmi).
- Verificar se os logons usados para conectar o SQL Server de origem e a instância gerenciada de destino são membros da função de servidor sysadmin.

    >[!NOTE]
    >Por padrão, o Azure Database Migration Service só suporta logins SQL migratórios. No entanto, você pode habilitar a capacidade de migrar logins do Windows por:
    >
    >- Garantir que a instância gerenciada do Banco de Dados SQL de destino tenha acesso à leitura AAD, que pode ser configurado através do portal Azure por um usuário com a função **administrador da empresa**ou um administrador **global.**
    >- Configurando a instância do Serviço de Migração do Banco de Dados do Azure para habilitar as migrações de login do usuário/grupo do Windows, que é configurada através do portal Azure, na página Configuração. Depois de habilitar essa configuração, reinicie o serviço para que as alterações entrem em vigor.
    >
    > Depois de reiniciar o serviço, os logins de usuário/grupo do Windows aparecem na lista de logins disponíveis para migração. Para quaisquer logins de usuário/grupo do Windows que você migrar, você é solicitado a fornecer o nome de domínio associado. As contas de usuário do serviço (conta com nome de domínio NT AUTHORITY) e contas de usuário virtuais (nome da conta com nome de domínio NT SERVICE) não são suportadas.

- Crie um compartilhamento de rede que pode ser usado pelo Serviço de Migração de Banco de Dados do Azure para fazer backup do banco de dados de origem.
- Certifique-se de que a conta de serviço que executa a instância do SQL Server de origem tem privilégios de gravação no compartilhamento de rede que você criou e que a conta de computador do servidor de origem tem acesso de leitura/gravação para o mesmo compartilhamento.
- Anote um usuário do Windows (e a senha) que tem privilégios de controle total no compartilhamento de rede criado anteriormente. O Azure Database Migration Service representa a credencial do usuário para carregar os arquivos de backup no contêiner Azure Storage para operação de restauração.
- Crie um contêiner de blobs e recupere seu URI SAS usando as etapas descritas no artigo [Gerenciar os recursos do Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) e lembre-se de selecionar todas as permissões (Leitura, Gravação, Exclusão, Lista) na janela da política ao criar o URI SAS. Esse detalhe fornece ao Serviço de Migração de Banco de Dados do Azure o acesso ao contêiner da conta de armazenamento para carregar os arquivos de backup usados para migrar bancos de dados para a Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

    ![Mostrar assinaturas do portal](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Procure migração e, em seguida, à direita do **Microsoft.DataMigration,** selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

1. No portal Azure, selecione + **Crie um recurso,** procure o **Serviço de Migração de Banco de Dados do Azure**e selecione o Serviço de Migração de Banco de **Dados do Azure** na lista de desígência.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione o local no qual você deseja criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Azure Database Migration Service acesso ao SQL Server de origem e à instância gerenciada do Banco de Dados SQL do Azure.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual usando o portal Azure](https://aka.ms/DMSVnet).

    Para obter detalhes adicionais, confira o artigo [Topologias de rede para migrações da instância gerenciada do BD SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi).

6. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que uma instância do serviço é criada, localize-a no portal do Azure, abra-a e, em seguida, crie um novo projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Na tela do **Serviço de Migração de Banco de Dados do Azure,** pesquise o nome da instância que você criou e selecione a instância.

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**; na caixa de texto **Tipo de servidor de destino**, selecione **Instância Gerenciada do Banco de Dados SQL do Azure** e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**.

   ![Criar projeto do DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selecione **Criar** para criar o cluster.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão do SQL Server de origem.

2. Caso não tenha instalado um certificado confiável no servidor, selecione a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões TLS que são criptografadas usando um certificado auto-assinado não fornecem forte segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no TLS usando certificados auto-assinados em um ambiente de produção ou em servidores conectados à internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Selecione **Salvar**.

4. Na tela **Selecionar bancos de dados de origem**, selecione o banco de dados **Adventureworks2012** para migração.

   ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Se você usa o SSIS (SQL Server Integration Services), o DMS não dá suporte, no momento, à migração do banco de dados de catálogo dos seus projetos/pacotes do SSIS (SSISDB) do SQL Server para a instância gerenciada do Banco de Dados SQL do Azure. No entanto, é possível provisionar o SSIS no ADF (Azure Data Factory) e reimplantar os projetos/pacotes do SSIS no SSISDB de destino hospedado pela instância gerenciada do Banco de Dados SQL do Azure. Para saber mais sobre como migrar pacotes SSIS, confira o artigo [Migrar pacotes do SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selecione **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **Detalhes de destino da migração**, especifique os detalhes de conexão do destino, que é a instância gerenciada do Banco de Dados SQL do Azure para a qual o banco de dados **AdventureWorks2012** está sendo migrado.

    Caso você ainda não tenha provisionado a Instância Gerenciada do Banco de Dados SQL, selecione o [link](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) para ajudar a provisionar a instância. É possível continuar assim mesmo com a criação do projeto e, em seguida, quando a instância gerenciada do Banco de Dados SQL do Azure estiver pronta, retorne a este projeto específico para executar a migração.

    ![Selecionar o destino](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Selecione **Salvar**.

## <a name="select-source-databases"></a>Selecionar bancos de dados de origem

1. Na tela **Selecionar bancos de dados de destino**, selecione o banco de dados de origem que você deseja migrar.

    ![Selecionar bancos de dados de origem](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Selecione **Salvar**.

## <a name="select-logins"></a>Selecionar logons

1. Na tela **Selecionar logons**, selecione os logons que deseja migrar.

    >[!NOTE]
    >Por padrão, o Azure Database Migration Service só suporta logins SQL migratórios. Para habilitar o suporte para migração de logins do Windows, consulte a seção **Pré-requisitos** deste tutorial.

    ![Selecionar logons](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Selecione **Salvar**.

## <a name="configure-migration-settings"></a>Definir as configurações de migração

1. Na tela **Definir as configurações de migração**, forneça os seguintes detalhes:

    | | |
    |--------|---------|
    |**Escolher opção de backup de origem** | Escolha a opção **Fornecerei os arquivos de backup mais recentes** quando você já tiver arquivos de backup completos disponíveis para uso pelo DMS para a migração de banco de dados. Escolha a opção **Permitirei que o Serviço de Migração de Banco de Dados do Azure crie arquivos de backup** quando desejar que o DMS faça o backup completo do banco de dados de origem primeiro e use-o para a migração. |
    |**Compartilhamento do local da rede** | O compartilhamento de rede SMB local no qual o Serviço de Migração de Banco de Dados do Azure pode fazer os backups do banco de dados de origem. A conta de serviço que executa a instância do SQL Server de origem deve ter privilégios de gravação nesse compartilhamento de rede. Forneça um FQDN ou endereços IP do servidor no compartilhamento de rede, por exemplo, “\\\servername.domainname.com\backupfolder' ou '\\\IP address\backupfolder”.|
    |**Nome de usuário** | Certifique-se de que o usuário do Windows possui privilégios de controle total no compartilhamento de rede fornecido acima. O Azure Database Migration Service se passará pela credencial do usuário para carregar os arquivos de backup no contêiner Azure Storage para operação de restauração. Se os bancos de dados habilitados para TDE forem selecionados para migração, o usuário do Windows acima deve ser a conta de administrador interno e [Controle de Conta de Usuário](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) deve ser desabilitado para o Serviço de Migração de Banco de Dados carregar e excluir os arquivos de certificados). |
    |**Senha** | Senha do usuário. |
    |**Configurações da conta de armazenamento** | O SAS URI que fornece o Azure Database Migration Service com acesso ao contêiner da sua conta de armazenamento para o qual o serviço carrega os arquivos de backup e que é usado para migrar bancos de dados para a instância gerenciada do Azure SQL Database. [Saiba como obter o SAS URI para o recipiente blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Configurações de TDE** | Se você estiver migrando bancos de dados de origem com a criptografia TDE (Transparent Data Encryption) habilitada, será necessário ter privilégios de gravação na instância gerenciada do Banco de Dados SQL do Azure de destino.  Selecione a assinatura na qual foi provisionada a instância gerenciada do Banco de Dados SQL do Azure no menu suspenso.  Selecione a **Instância Gerenciada do Banco de Dados SQL do Azure** de destino no menu suspenso. |

    ![Definir as configurações de migração](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Selecione **Salvar**.

## <a name="review-the-migration-summary"></a>Análise do resumo da migração

1. Na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

2. Expanda a seção **Opção de validação** para exibir a tela **Escolher a opção de validação**, especifique se é preciso validar os bancos de dados migrados para correção do esquema e, em seguida, selecione **Salvar**.

3. Analise e verifique os detalhes associados ao projeto de migração.

    ![Resumo do projeto de migração](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Selecione **Salvar**.

## <a name="run-the-migration"></a>Execute a migração

- Selecione **Executar migração**.

  A janela de atividade de migração aparece e o status da atividade está **Pendente**.

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela da atividade de migração, selecione **Atualizar** para atualizar a exibição.

   ![Atividade de migração em andamento](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    É possível expandir ainda mais as categorias de logon e banco de dados para monitorar o status da migração dos respectivos objetos de servidor.

   ![Atividade de migração em andamento](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Após a conclusão da migração, selecione **Baixar relatório** para obter um relatório que lista os detalhes associados ao processo de migração.

3. Verifique se o banco de dados de destino está no ambiente da instância gerenciada do Banco de Dados SQL do Azure de destino.

## <a name="next-steps"></a>Próximas etapas

- Para obter um tutorial mostrando como migrar um banco de dados para uma instância gerenciada usando o comando T-SQL RESTORE, consulte [Restaurar um backup em uma instância gerenciada usando o comando 'restaurar ''Restaurar'.](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)
- Para obter informações sobre instância gerenciada, consulte [O que é uma instância gerenciada](../sql-database/sql-database-managed-instance.md).
- Para obter informações sobre como conectar aplicativos a uma instância gerenciada, consulte [Aplicativos Connect](../sql-database/sql-database-managed-instance-connect-app.md).
