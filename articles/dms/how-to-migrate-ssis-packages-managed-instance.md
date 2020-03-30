---
title: Migrar pacotes SSIS para instância gerenciada SQL
titleSuffix: Azure Database Migration Service
description: Saiba como migrar pacotes e projetos ssis de integração de servidores sql para uma instância gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure ou o Assistente de Migração de Dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 97a466ab033a42016c0d82465d1f98e2dcae8080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297174"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Migrar pacotes do SQL Server Integration Services para uma instância gerenciada do Banco de Dados SQL do Azure
Se você usar o SSIS (SSIS) SQL Server Integration Services (SSIS) e quiser migrar seus projetos/pacotes ssisis da fonte SSISDB hospedada pelo SQL Server para o SSISDB de destino hospedado por uma instância gerenciada do Banco de Dados SQL do Azure, você pode usar o Azure Database Migration Service.

Se a versão do SSIS que você usa for anterior a 2012 ou você usar tipos de armazenamento de pacotes não-SSISDB, antes de migrar seus projetos/pacotes SSIS, você precisa convertê-los usando o Assistente de Conversão de Projetos de Serviços de Integração, que também pode ser lançado a partir de SSMS. Para obter mais informações, consulte o artigo [Converter projetos para o modelo de implantação de projeto](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> O Azure Database Migration Service (DMS) atualmente não suporta o Azure SQL Database como destino de migração de destino. Para reimplantar projetos/pacotes SSIS no Banco de Dados SQL do Azure, consulte o artigo [Reimplantar pacotes de serviços de integração de servidores SQL no Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Neste artigo, você aprenderá como:
> [!div class="checklist"]
>
> * Avaliar projetos/pacotes do SSIS de origem.
> * Migrar projetos/pacotes do SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisa:

* Para criar uma rede virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade local a local aos seus servidores de origem no local usando [expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações, consulte o artigo [Topologies de rede para migrações gerenciadas de instâncias gerenciadas do Azure SQL Database usando o Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual e, especialmente, os artigos de início rápido com detalhes passo a passo.
* Para garantir que as regras do Grupo de Segurança de Rede de Rede de rede virtual não bloqueiem as seguintes portas de comunicação de entrada para o Serviço de Migração de Banco de Dados Do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego DE Rede Virtual NSG, consulte o artigo [Filtrar](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)o tráfego da rede com grupos de segurança da rede .
* Para configurar o [firewall do Windows para acesso ao mecanismo de banco de dados de origem](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Para abrir o Firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o SQL Server de origem, que por padrão é a porta TCP 1433.
* Caso esteja executando várias instâncias nomeadas do SQL Server usando portas dinâmicas, talvez seja preciso habilitar o serviço do SQL Browser e permitir o acesso à porta UDP 1434 por meio de seus firewalls para que o Serviço de Migração de Banco de Dados do Azure possa se conectar a uma instância nomeada em seu servidor de origem.
* Se estiver usando um dispositivo de firewall na frente dos bancos de dados de origem, talvez seja preciso adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração, além de arquivos por meio da porta 445 do SMB.
* Uma instância gerenciada do Azure SQL Para hospedar o SSISDB. Se você precisar criar um, siga os detalhes no artigo [Criar uma instância gerenciada do banco de dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Para garantir que os logins usados para conectar o SQL Server de origem e a instância gerenciada de destino sejam membros da função servidor sysadmin.
* Para verificar se o SSIS está provisionado na Azure Data Factory (ADF) contendo o Tempo de Execução de Integração Azure-SSIS (IR) com o Destino SSISDB hospedado por uma instância gerenciada do Azure SQL Database (conforme descrito no artigo [Crie o tempo de execução de integração Azure-SSIS na Fábrica de Dados Do Azure).](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

## <a name="assess-source-ssis-projectspackages"></a>Avaliar projetos/pacotes do SSIS de origem

Embora a avaliação do SSISDB de origem ainda não esteja integrada ao DMA (Database Migration Assistant, assistente de migração de banco de dados), seus projetos/pacotes do SSIS serão avaliados/validados à medida que forem reimplantados para o SSISDB de destino hospedado em uma instância gerenciada do Banco de Dados SQL do Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

    ![Mostrar assinaturas do portal](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Procure migração e, em seguida, à direita do **Microsoft.DataMigration,** selecione **Registrar**.

    ![Registrar provedor de recursos](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

1. No portal Azure, selecione + **Crie um recurso,** procure o **Serviço de Migração de Banco de Dados do Azure**e selecione o Serviço de Migração de Banco de **Dados do Azure** na lista de desígência.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione o local no qual você deseja criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Azure Database Migration Service acesso ao SQL Server de origem e à instância gerenciada do Banco de Dados SQL do Azure.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual usando o portal Azure](https://aka.ms/DMSVnet).

    Para obter mais detalhes, consulte o artigo [Topoologias de rede para migrações gerenciadas de instâncias gerenciadas do Azure SQL DB usando o Serviço de Migração de Banco de Dados do Azure](https://aka.ms/dmsnetworkformi).

6. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que uma instância do serviço é criada, localize-a no portal do Azure, abra-a e, em seguida, crie um novo projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

    ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Na tela do **Serviço de Migração de Banco de Dados do Azure,** pesquise o nome da instância que você criou e selecione a instância.

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **do projeto Nova migração,** especifique um nome para o projeto, na caixa de **texto tipo servidor Fonte,** selecione **SQL Server**, na caixa de texto tipo servidor **alvo,** selecione **Azure SQL Database Managed Instance**e, em seguida, para Escolher tipo de **atividade,** selecione migração de **pacote SSIS**.

   ![Criar projeto do DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Selecione **Criar** para criar o cluster.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Detalhe de origem de migração**, especifique os detalhes da conexão do SQL Server de origem.

2. Caso não tenha instalado um certificado confiável no servidor, selecione a caixa de seleção **Certificado de servidor confiável**.

    Quando não houver um certificado confiável instalado, o SQL Server gerará um certificado autoassinado quando a instância for iniciada. Esse certificado é usado para criptografar as credenciais das conexões de cliente.

    > [!CAUTION]
    > As conexões TLS que são criptografadas usando um certificado auto-assinado não fornecem forte segurança. Elas são suscetíveis a ataques “man-in-the-middle”. Você não deve confiar no TLS usando certificados auto-assinados em um ambiente de produção ou em servidores conectados à internet.

   ![Detalhes da origem](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Selecione **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **de detalhes do destino Migração,** especifique os detalhes da conexão para o destino.

     ![Detalhes do destino](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Selecione **Salvar**.

## <a name="review-the-migration-summary"></a>Análise do resumo da migração

1. Na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração.

2. Para a **opção de sobregravação de projetos ssis e ambientes,** especifique se deve substituir ou ignorar projetos e ambientes SSIS existentes.

    ![Resumo do projeto de migração](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Analise e verifique os detalhes associados ao projeto de migração.

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

## <a name="next-steps"></a>Próximas etapas

* Verificar as diretrizes de migração no [Guia de Migração de Banco de Dados da Microsoft](https://datamigration.microsoft.com/).
