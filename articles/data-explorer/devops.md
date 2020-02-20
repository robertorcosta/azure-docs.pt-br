---
title: Tarefa do Azure DevOps para Azure Data Explorer
description: Neste tópico, você aprende a criar um pipeline de liberação e a implantar
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472036"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Tarefa do Azure DevOps para Azure Data Explorer

O [Azure DevOps Services](https://azure.microsoft.com/services/devops/) fornece ferramentas de colaboração de desenvolvimento, como pipelines de alto desempenho, repositórios git privados gratuitos, quadros Kanban configuráveis e recursos abrangentes de teste automatizado e contínuo. [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/) é um recurso de DevOps do Azure que permite que você gerencie CI/CD para implantar seu código com pipelines de alto desempenho que funcionam com qualquer linguagem, plataforma e nuvem.
Os [comandos de administração data Explorer do Azure](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) são a tarefa Azure pipelines que permite que você crie pipelines de versão e implante suas alterações de banco de dados em seus bancos de dados do Azure data Explorer. Ele está disponível gratuitamente no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Este documento descreve um exemplo simples sobre o uso da tarefa **Azure data Explorer – comandos de administração** para implantar as alterações de esquema em seu banco de dados. Para pipelines de CI/CD completos, consulte a [documentação do Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Prerequisites

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Configuração do cluster Data Explorer do Azure:
    * Um [cluster de data Explorer do Azure e um banco de dados](/azure/data-explorer/create-cluster-database-portal)
    * Crie um aplicativo Azure Active Directory (Azure AD) [provisionando um aplicativo do Azure ad](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Conceda acesso ao seu Aplicativo Azure AD no banco de dados de Data Explorer do Azure [Gerenciando permissões do banco de dados data Explorer do Azure](/azure/data-explorer/manage-database-permissions).
* Configuração do Azure DevOps:
    * [Inscreva-se em uma organização gratuita](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Criar uma organização](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Criar um projeto no Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Código com git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Criar pastas

Crie as seguintes pastas de exemplo (*funções*, *políticas*, *tabelas*) no repositório git. Copie os arquivos [aqui](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) para as respectivas pastas, conforme mostrado abaixo e confirme as alterações. Os arquivos de exemplo são fornecidos para executar o fluxo de trabalho a seguir.

![Criar pastas](media/devops/create-folders.png)

> [!TIP]
> Ao criar seu próprio fluxo de trabalho, é recomendável tornar seu código idempotente. Por exemplo, use [a tabela. Create-Merge](/azure/kusto/management/create-table-command#create-merge-table) , em vez de [. CREATE TABLE](/azure/kusto/management/create-table-command), e use a função [. Create-ou-ALTER](/azure/kusto/management/create-alter-function) em vez da função [. Create](/azure/kusto/management/create-function) .

## <a name="create-a-release-pipeline"></a>Criar um pipeline de lançamento

1. Entre na sua [organização do DevOps do Azure](https://dev.azure.com/).
1. Selecione **pipelines** > **versões** do menu à esquerda e selecione **novo pipeline**.

    ![Novo pipeline](media/devops/new-pipeline.png)

1. A janela **novo pipeline de lançamento** é aberta. Na guia **pipelines** , no painel **selecionar um modelo** , selecione **trabalho vazio**.

     ![Selecione um modelo](media/devops/select-template.png)

1. Selecione o botão de **estágio** . No painel **estágio** , adicione o **nome do estágio**. Selecione **salvar** para salvar o pipeline.

    ![Nomear o estágio](media/devops/stage-name.png)

1. Selecione **o botão Adicionar um artefato** . No painel **Adicionar um artefato** , selecione o repositório onde o código existe, preencha as informações relevantes e clique em **Adicionar**. Selecione **salvar** para salvar o pipeline.

    ![Adicionar um artefato](media/devops/add-artifact.png)

1. Na guia **variáveis** , selecione **+ Adicionar** para criar uma variável para a **URL do ponto de extremidade** que será usada na tarefa. Escreva o **nome** e o **valor** do ponto de extremidade. Selecione **salvar** para salvar o pipeline. 

    ![Criar variável](media/devops/create-variable.png)

    Para localizar seu Endpoint_URL, a página Visão geral do **cluster de data Explorer do Azure** no portal do Azure contém o URI do cluster de data Explorer do Azure. Construa o URI no seguinte formato `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Por exemplo, https:\//kustodocs.westus.Kusto.Windows.net? DatabaseName = SampleDB

    ![URI do cluster de Data Explorer do Azure](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Criar tarefas para implantar

1. Na guia **pipeline** , clique em **um trabalho, 0 tarefa** para adicionar tarefas. 

    ![Adicionar tarefas](media/devops/add-task.png)

1. Crie três tarefas para implantar **tabelas**, **funções**e **políticas**, nesta ordem. 

1. Na guia **tarefas** , selecione **+** por **trabalho do Agent**. Pesquise **Azure Data Explorer**. No **Marketplace**, instale a extensão **do Azure data Explorer – comandos de administração** . Em seguida, selecione **Adicionar** no **comando executar data Explorer do Azure**.

     ![Adicionar comandos de administrador](media/devops/add-admin-commands.png)

1. Clique no **comando Kusto** à esquerda e atualize a tarefa com as seguintes informações:
    * **Nome de exibição**: o nome da tarefa
    * **Caminho do arquivo**: na **tarefa tabelas** , especifique */Tables/* . CSL, já que os arquivos de criação de tabela estão na pasta da *tabela* .
    * **URL do ponto de extremidade**: Insira a variável de `EndPoint URL`criada na etapa anterior.
    * Selecione **usar ponto de extremidade de serviço** e selecione **+ novo**.

    ![Tarefa de comando Atualizar Kusto](media/devops/kusto-command-task.png)

1. Preencha as seguintes informações na janela **Adicionar conexão de serviço do Azure data Explorer** :

    |Configuração  |Valor sugerido  |
    |---------|---------|
    |**Nome da conexão**     |    Insira um nome para identificar este ponto de extremidade de serviço     |
    |**URL do cluster**    |    O valor pode ser encontrado na seção visão geral do cluster de Data Explorer do Azure no portal do Azure | 
    |**ID da entidade de serviço**    |    Insira a ID do aplicativo do AAD (criada como pré-requisito)     |
    |**Chave do aplicativo da entidade de serviço**     |    Insira a chave do aplicativo do AAD (criada como pré-requisito)    |
    |**ID do locatário do AAD**    |      Insira seu locatário do AAD (como microsoft.com, contoso.com...)    |

    Marque **a caixa de seleção permitir que todos os pipelines usem esta conexão** . Selecione **OK**.

    ![Adicionar conexão de serviço](media/devops/add-service-connection.png)

1. Repita as etapas 1-5 outras duas vezes para implantar arquivos das pastas *funções* e *políticas* . Clique em **Salvar**. Na guia **tarefas** , consulte as três tarefas criadas: **implantar tabelas**, **implantar funções**e **implantar políticas**.

    ![Implantar todas as pastas](media/devops/deploy-all-folders.png)

1. Selecione **+ versão** > **criar versão** para criar uma versão.

    ![Criar uma versão](media/devops/create-release.png)

1. Na guia **logs** , verifique se o status da implantação foi bem-sucedido.

    ![A implantação foi bem-sucedida](media/devops/deployment-successful.png)

Agora você concluiu a criação de um pipeline de lançamento para a implantação de três tarefas para pré-produção.