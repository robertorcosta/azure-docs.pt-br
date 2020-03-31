---
title: Reimplantar pacotes SSIS para banco de dados único SQL
titleSuffix: Azure Database Migration Service
description: Saiba como migrar ou reimplantar pacotes e projetos de integração de servidores SQL para o banco de dados único do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure e o Assistente de Migração de Dados.
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
ms.openlocfilehash: 90a39b8fe3604a05f1d35a875ae4e34491b47d72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648522"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Reimplante pacotes SSIS no Banco de Dados SQL do Azure com o Serviço de Migração de Banco de Dados do Azure

Se você usar o SSIS (SSIS) SQL Server Integration Services (SSIS) e quiser migrar seus projetos/pacotes ssisis da fonte SSISDB hospedada pelo SQL Server para o SSISDB de destino hospedado pelo Banco de Dados SQL do Azure, você pode reimplantá-los usando o Assistente de Implantação de Serviços de Integração. Você pode iniciar o Assistente de dentro do SSMS (SQL Server Management Studio).

Se a versão do SSIS que você usa for anterior a 2012, antes de reimplantar os projetos/pacotes do SSIS no modelo de implantação de projeto, primeiro será necessário convertê-los usando o 	Assistente de Conversão de Projetos do Integration Services, que também pode ser iniciado pelo SSMS. Para obter mais informações, consulte o artigo [Converter projetos para o modelo de implantação de projeto](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> O DMS (Azure Database Migration Service, serviço de migração de banco de dados do Azure) atualmente não suporta a migração de um SSISDB de origem para um servidor de banco de dados SQL do Azure, mas você pode reimplantar seus projetos/pacotes do SSIS usando o seguinte processo.

Neste artigo, você aprenderá como:
> [!div class="checklist"]
>
> * Avaliar projetos/pacotes do SSIS de origem.
> * Migrar projetos/pacotes do SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisa:

* SSMS versão 17.2 ou posterior.
* Uma instância de servidor de banco de dados de destino para hospedar o SSISDB. Se você ainda não tiver um, crie um servidor de banco de dados SQL do Azure (sem banco de dados) usando o portal Azure navegando até o [formulário](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (somente servidor lógico).
* O SSIS deve ser provisionado na Azure Data Factory (ADF) contendo o Tempo de Execução de Integração Azure-SSIS (IR) com o Destino SSISDB hospedado pela instância do servidor Azure SQL Database (conforme descrito no artigo [Provision the Azure-SSIS Integration Runtime in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Avaliar projetos/pacotes do SSIS de origem

Embora a avaliação do SSISDB de origem ainda não esteja integrada ao DMA (Database Migration Assistant, assistente de migração de banco de dados) ou ao Serviço de Migração de Banco de Dados (DMS) do Azure, seus projetos/pacotes do SSIS serão avaliados/validados à medida que forem reimplantados no SSISDB de destino hospedado em um servidor de banco de dados SQL do Azure.

## <a name="migrate-ssis-projectspackages"></a>Migrar projetos/pacotes do SSIS

Para migrar projetos/pacotes SSIS para o servidor Azure SQL Database, execute as seguintes etapas.

1. Abra o SSMS e, em seguida, selecione **Opções** para exibir a caixa de diálogo **Conectar ao Servidor**.

2. Na guia **Login,** especifique as informações necessárias para se conectar ao servidor Azure SQL Database que hospedará o SSISDB de destino.

    ![Guia Logon do SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Na guia **Propriedades da Conexão**, na caixa de texto **Conectar ao banco de dados**, selecione ou insira **SSISDB** e, em seguida, selecione **Conectar**.

    ![Guia Propriedades da Conexão do SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. No Pesquisador de Objetos do SSMS, expanda o nó **Catálogo do Integration Services**, expanda **SSISDB** e, se não houver nenhuma pasta existente, clique com botão direito em **SSISDB** e crie uma nova pasta.

5. Em **SSISDB**, expanda qualquer pasta, clique com botão direito em **Projetos** e, em seguida, selecione **Implantar Projeto**.

    ![Nó SSISDB do SSIS expandido](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. No Assistente de Implantação do Integration Services, na página **Introdução**, examine as informações e, em seguida, selecione **Avançar**.

    ![Página Introdução do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Na página **Selecionar Origem**, especifique o projeto do SSIS existente que você deseja implantar.

    Se o SSMS também está conectado ao SQL Server que hospeda o SSISDB de origem, selecione **Catálogo do Integration Services** e, em seguida, insira o nome do servidor e o caminho do projeto no catálogo para implantar o projeto diretamente.

    Como alternativa, selecione **Arquivo de implantação do projeto** e, em seguida, especifique o caminho para um arquivo de implantação do projeto existente (.ispac) para implantar o projeto.

    ![Página Selecionar Origem do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Selecione **Avançar**.
9. Na página **Selecionar Destino**, especifique o destino para o projeto.

    a. Na caixa de texto nome do servidor, digite o nome do servidor do Banco de Dados Azure SQL totalmente qualificado (<server_name>.database.windows.net).

    b. Forneça as informações de autenticação e, em seguida, selecione **Conectar**.

    ![Página Selecionar Destino do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Selecione **Procurar** para especificar a pasta de destino no SSISDB e, em seguida, selecione **Next**.

    > [!NOTE]
    > O botão **Avançar** será habilitado somente após você ter selecionado **Conectar**.

10. Na página **Validar**, exiba quaisquer erros/avisos e, se necessário, modifique os pacotes adequadamente.

    ![Página Validar do Assistente de Implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selecione **Avançar**.

12. Na página **Examinar**, examine as configurações de implantação.

    > [!NOTE]
    > Você pode alterar suas configurações selecionando **Anterior** ou selecionando qualquer um dos links de passo no painel esquerdo.

13. Selecione **Implantar** para iniciar o processo de implantação.

14. Após a conclusão do processo de implantação, você pode exibir a página de resultados, que exibe o êxito ou a falha de cada ação de implantação.
    a. Se qualquer ação falhou, na coluna **Resultado**, selecione **Falhou** para exibir uma explicação do erro.
    b. Opcionalmente, selecione **Salvar Relatório** para salvar os resultados em um arquivo XML.

15. Selecione **Fechar** para sair do Assistente de Implantação do Integration Services.

Se a implantação do seu projeto teve êxito sem falhas, é possível selecionar todos os pacotes contidos nele para executar no Azure-SSIS IR.

## <a name="next-steps"></a>Próximas etapas

* Verificar as diretrizes de migração no [Guia de Migração de Banco de Dados da Microsoft](https://datamigration.microsoft.com/).
