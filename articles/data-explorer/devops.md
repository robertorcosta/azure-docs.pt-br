---
title: Tarefa Azure DevOps para O Azure Data Explorer
description: Neste tópico, você aprende a criar um pipeline de liberação e implantar
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472036"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Tarefa de devOps do Azure para o Azure Data Explorer

[O Azure DevOps Services](https://azure.microsoft.com/services/devops/) fornece ferramentas de colaboração de desenvolvimento, como pipelines de alto desempenho, repositórios gratuitos privados do Git, placas Kanban configuráveis e extensos recursos de teste automatizados e contínuos. [O Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) é um recurso do Azure DevOps que permite gerenciar CI/CD para implantar seu código com pipelines de alto desempenho que funcionam com qualquer idioma, plataforma e nuvem.
[Azure Data Explorer - Admin Commands](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) é a tarefa do Azure Pipelines que permite criar pipelines de liberação e implantar suas alterações no banco de dados do Azure Data Explorer. Está disponível gratuitamente no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Este documento descreve um exemplo simples sobre o uso da tarefa **Azure Data Explorer – Comandos de Administrador** para implantar suas alterações de esquema em seu banco de dados. Para obter pipelines completos de CI/CD, consulte a [documentação do Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Configuração do Azure Data Explorer Cluster:
    * Um [cluster e banco de dados do Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)
    * Crie o aplicativo Azure Active Directory (Azure AD) [provisionando um aplicativo Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Conceda acesso ao seu aplicativo Azure AD em seu banco de dados Do Azure Data Explorer [gerenciando as permissões do banco de dados do Azure Data Explorer](/azure/data-explorer/manage-database-permissions).
* Configuração do Azure DevOps:
    * [Inscreva-se em uma organização gratuita](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Criar uma organização](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Crie um projeto no Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Código com Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Criar pastas

Crie as seguintes pastas de exemplo *(Funções,* *Políticas,* *Tabelas)* no repositório git. Copie os arquivos [daqui](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) para as respectivas pastas como visto abaixo e cometa as alterações. Os arquivos de amostra são fornecidos para executar o seguinte fluxo de trabalho.

![Criar pastas](media/devops/create-folders.png)

> [!TIP]
> Ao criar seu próprio fluxo de trabalho, recomendamos tornar seu código idempotente. Por exemplo, use [a tabela .create-merge](/azure/kusto/management/create-table-command#create-merge-table) em vez de [.create table](/azure/kusto/management/create-table-command)e use a função [.create-or-alter](/azure/kusto/management/create-alter-function) em vez de [.create](/azure/kusto/management/create-function) function.

## <a name="create-a-release-pipeline"></a>Criar um pipeline de lançamento

1. Faça login na sua [organização Azure DevOps](https://dev.azure.com/).
1. Selecione **lançamentos** > de**pipelines** no menu à esquerda e selecione **Novo pipeline**.

    ![Novo pipeline](media/devops/new-pipeline.png)

1. A nova janela **do oleoduto de liberação** se abre. Na guia **Pipelines,** na **seleta Select a template** pane, selecione **Empty job**.

     ![Selecione um modelo](media/devops/select-template.png)

1. Selecione o botão **Estágio.** No **painel de palco,** adicione o **nome do palco**. Selecione **Salvar** para salvar seu pipeline.

    ![Nomeie o palco](media/devops/stage-name.png)

1. Selecione Adicionar um botão **de artefato.** No Adicionar um painel **de artefatos,** selecione o repositório onde seu código existe, preencha as informações relevantes e clique **em Adicionar**. Selecione **Salvar** para salvar seu pipeline.

    ![Adicionar um artefato](media/devops/add-artifact.png)

1. Na guia **Variáveis,** selecione **+ Adicionar** para criar uma variável para **URL endpoint** que será usada na tarefa. Escreva o **nome** e o **valor** do ponto final. Selecione **Salvar** para salvar seu pipeline. 

    ![Criar variável](media/devops/create-variable.png)

    Para encontrar sua Endpoint_URL, a página de visão geral do **cluster Azure Data Explorer** no portal Azure contém o uri do cluster Azure Data Explorer. Construa o URI `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`no formato a seguir.  Por exemplo,\/https: /kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Azure Data Explorer cluster URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Criar tarefas para implantar

1. Na guia **Pipeline,** clique em **1 trabalho, 0 tarefa** para adicionar tarefas. 

    ![Adicionar tarefas](media/devops/add-task.png)

1. Crie três tarefas para implantar **Tabelas, Funções**e **Políticas**nesta ordem. **Tables** 

1. Na guia **Tarefas,** selecione **+** por **trabalho do Agente**. Pesquise **Azure Data Explorer**. No **Marketplace,** instale a extensão **Azure Data Explorer – Comandos de Administrador.** Em seguida, **selecione Adicionar** no **comando Executar azure Data Explorer .**

     ![Adicionar comandos de admin](media/devops/add-admin-commands.png)

1. Clique no **Comando Kusto** à esquerda e atualize a tarefa com as seguintes informações:
    * **Nome de exibição**: Nome da tarefa
    * **Caminho do arquivo**: Na tarefa **Tabelas,** especifique */Tabelas/*.csl, já que os arquivos de criação de tabela estão na pasta *Tabela.*
    * **URL ponto final** `EndPoint URL`: digite a variável criada na etapa anterior.
    * Selecione **Use Service Endpoint** e selecione **+ Novo**.

    ![Atualizar a tarefa de comando kusto](media/devops/kusto-command-task.png)

1. Complete as seguintes informações na janela **de conexão Add Azure Data Explorer:**

    |Configuração  |Valor sugerido  |
    |---------|---------|
    |**Nome da conexão**     |    Digite um nome para identificar este ponto final do serviço     |
    |**Cluster Url**    |    O valor pode ser encontrado na seção visão geral do cluster Azure Data Explorer no portal Azure | 
    |**ID do Diretor de Serviço**    |    Insira o ID do aplicativo AAD (criado como pré-requisito)     |
    |**Chave do aplicativo principal do serviço**     |    Digite a chave do aplicativo AAD (criada como pré-requisito)    |
    |**ID do inquilino AAD**    |      Digite seu inquilino AAD (como microsoft.com, contoso.com...)    |

    Selecione **Permitir que todos os pipelines usem esta caixa de seleção de conexão.** Selecione **OK**.

    ![Adicionar conexão de serviço](media/devops/add-service-connection.png)

1. Repita as etapas 1-5 outras duas vezes para implantar arquivos das *pastas Funções* e *Políticas.* Selecione **Salvar**. Na guia **Tarefas,** consulte as três tarefas criadas: **Implantar** **tabelas, implantar funções**e implantar **políticas**.

    ![Implantar todas as pastas](media/devops/deploy-all-folders.png)

1. Selecione **+ Libere Criar** > **versão** para criar uma versão.

    ![Criar uma versão](media/devops/create-release.png)

1. Na guia **Logs,** verifique se o status de implantação é bem-sucedido.

    ![A implantação é bem sucedida](media/devops/deployment-successful.png)

Você já concluiu a criação de um pipeline de liberação para implantação de três tarefas para a pré-produção.