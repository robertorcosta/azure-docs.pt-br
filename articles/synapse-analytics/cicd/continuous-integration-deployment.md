---
title: Integração e entrega contínuas para o espaço de trabalho Synapse
description: Saiba como usar a integração e a entrega contínuas para implantar alterações no espaço de trabalho de um ambiente (desenvolvimento, teste, produção) para outro.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 2f2221ad10a2e07a3443cab9f957c8ec26969a3b
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96031117"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Integração e entrega contínuas para o espaço de trabalho Synapse do Azure

## <a name="overview"></a>Visão geral

A CI (integração contínua) é o processo de automatização da compilação e do teste de código sempre que um membro da equipe confirma alterações no controle de versão. A implantação contínua (CD) é o processo para criar, testar, configurar e implantar a partir de vários ambientes de teste ou de preparo para um ambiente de produção.

Para o espaço de trabalho Synapse do Azure, a integração contínua e a entrega (CI/CD) movem todas as entidades de um ambiente (desenvolvimento, teste, produção) para outro. Para promover seu espaço de trabalho para outro espaço de trabalho, há duas partes: usar [Azure Resource Manager modelos](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) para criar ou atualizar recursos do espaço de trabalho (pools e espaço de trabalho); migrar artefatos (scripts SQL, Notebook, definição de trabalho do Spark, pipelines, conjuntos de dados, fluxos de dados e assim por diante) com ferramentas de CI/CD do Synapse no Azure DevOps. 

Este artigo descreverá o uso do pipeline de liberação do Azure para automatizar a implantação de um espaço de trabalho Synapse em vários ambientes.

## <a name="pre-requirements"></a>Pré-requisitos

-   O espaço de trabalho usado para desenvolvimento foi configurado com um repositório git no estúdio, consulte [controle do código-fonte no Synapse Studio](source-control.md).
-   Um projeto DevOps do Azure foi preparado para executar o pipeline de liberação.

## <a name="set-up-a-release-pipelines"></a>Configurar pipelines de liberação

1.  No [Azure DevOps](https://dev.azure.com/), abra o projeto criado para a versão.

1.  No lado esquerdo da página, selecione **Pipelines** e, em seguida, selecione **Versões**.

    ![Selecionar Pipelines, Versões](media/create-release-1.png)

1.  Selecione **Novo pipeline** ou, se tiver pipelines existentes, selecione **Novo** e, em seguida, **Novo pipeline de lançamento**.

1.  Selecione o modelo **Trabalho vazio**.

    ![Selecionar Trabalho vazio](media/create-release-select-empty.png)

1.  Na caixa **Nome da fase**, insira o nome do seu ambiente.

1.  Selecione **Adicionar artefato** e, em seguida, selecione o repositório git configurado com seu Development Synapse Studio. Selecione o repositório git que você usou para gerenciar o modelo ARM de pools e o espaço de trabalho. Se você usar o GitHub como a origem, precisará criar uma conexão de serviço para sua conta do GitHub e repositórios de pull. Para obter mais informações sobre a [conexão de serviço](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints) 

    ![Adicionar Branch de publicação](media/release-creation-github.png)

1.  Selecione a ramificação do modelo ARM para atualização de recursos. Para a **Versão padrão**, selecione **Mais recente do branch padrão**.

    ![Adicionar modelo ARM](media/release-creation-arm-branch.png)

1.  Selecione o [branch de publicação](source-control.md#configure-publishing-settings) do repositório para o **Branch padrão**. Por padrão, esse branch de publicação é `workspace_publish`. Para a **Versão padrão**, selecione **Mais recente do branch padrão**.

    ![Adicionar um artefato](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Configurar uma tarefa de estágio para criação e atualização de recursos ARM 

Adicione uma tarefa de implantação de Azure Resource Manager para criar ou atualizar recursos, incluindo espaço de trabalho e pools:

1. No modo de exibição de fase, selecione **Exibir tarefas da fase**.

    ![Exibição de fase](media/release-creation-stage-view.png)

1. Crie uma tarefa. Pesquise **implantação de modelo ARM** e, em seguida, selecione **Adicionar**.

1. Na tarefa de implantação, selecione a assinatura, o grupo de recursos e o local para o espaço de trabalho de destino. Forneça as credenciais, se necessário.

1. Na lista **Ação**, selecione **Criar ou atualizar grupo de recursos**.

1. Selecione o botão de reticências ( **…** ) ao lado da caixa **Modelo**. Procure o modelo de Azure Resource Manager do seu espaço de trabalho de destino

1. Selecione **...** ao lado da caixa **Parâmetros do modelo** para escolher o arquivo de parâmetros.

1. Selecione **...** ao lado da caixa **Substituir parâmetros de modelo** e insira os valores de parâmetro desejados para o espaço de trabalho de destino. 

1. Selecione **Incremental** para o **Modo de implantação**.
    
    ![implantação de espaço de trabalho e pools](media/pools-resource-deploy.png)

1. Adicional Adicione **Azure PowerShell** para a atribuição de função de espaço de trabalho Grant and Update. Se você usar o pipeline de liberação para criar um espaço de trabalho do Synapse, a entidade de serviço do pipeline precisará ser adicionada como administrador do espaço de trabalho padrão. Você pode executar o PowerShell para conceder a outras contas acesso ao espaço de trabalho. 
    
    ![conceder permissão](media/release-creation-grant-permission.png)

 > [!WARNING]
> No modo de implantação completa, os recursos existentes no grupo de recursos, mas não são especificados no novo modelo do Resource Manager, serão **excluídos**. Para obter mais informações, consulte [Azure Resource Manager modos de implantação](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Configurar uma tarefa de estágio para a implantação de artefatos 

Use a tarefa [criar & de espaços de trabalho do Synapse](https://marketplace.visualstudio.com/items?itemName=PraveenMathamsetty.synapsecicd-deploy) para implantar outros itens no espaço de trabalho Synapse, como DataSet, script SQL, Notebook, definição de trabalho do Spark, Dataflow, pipeline, serviço vinculado, credenciais e IR (Integration Runtime).  

1. Verifique se a entidade de serviço do pipeline DevOps do Azure recebeu a permissão de assinatura e também foi atribuída como administrador de espaço de trabalho para o espaço de trabalho de destino. 

1. Crie uma tarefa. Procure os **espaços de trabalho do Synapse Build & versão** e, em seguida, selecione **Adicionar**.

1.  Na tarefa, forneça as informações de repositório git relacionadas de **workspace_publish** e selecione grupo de recursos, região, nome e ambiente de nuvem para o espaço de trabalho de destino. Forneça parâmetros e valores, se necessário.

    ![implantação do espaço de trabalho Synapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> Em cenários de CI/CD, o tipo de IR (runtime de integração) em ambientes diferentes deve ser o mesmo. Por exemplo, se você tiver um IR auto-hospedado no ambiente de desenvolvimento, o mesmo IR também deverá ser do tipo auto-hospedado em outros ambientes, como teste e produção. Da mesma forma, se você estiver compartilhando runtimes de integração em várias fases, será necessário configurar os runtimes de integração como auto-hospedados vinculados em todos os ambientes, como desenvolvimento, teste e produção.

## <a name="create-release-for-deployment"></a>Criar versão para implantação 

Depois de salvar todas as alterações, você pode selecionar **criar versão** para criar uma versão manualmente. Para automatizar a criação de versões, confira [Gatilhos de versão do Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/release/triggers)

   ![Selecionar Criar versão](media/release-creation-manually.png)

## <a name="best-practices-for-cicd"></a>Práticas recomendadas para CI/CD

Se você estiver usando a integração do git com seu espaço de trabalho do Synapse e tiver um pipeline de CI/CD que move as alterações do desenvolvimento para o teste e, em seguida, para a produção, recomendamos estas práticas recomendadas:

-   **Integração do Git**. Configure somente seu espaço de trabalho de Synapse de desenvolvimento com a integração do git. As alterações nos espaços de trabalho de teste e produção são implantadas por meio de CI/CD e não precisam de integração com o git.
-   **Preparar pools antes da migração de artefatos**. Se você anexar pools ao seu script ou bloco de anotações SQL no espaço de trabalho de desenvolvimento, o mesmo nome de pools em ambientes diferentes será esperado. 
-   **Outros**. Veja [outras práticas recomendadas](/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)

## <a name="unsupported-features"></a>Recursos sem suporte

- O Synapse Studio não permite a seleção de Cherry de confirmações ou publicação seletiva de recursos. 
- O Synapse Studio não dá suporte à personalização de mensagem de confirmação.
- Por design, a ação de exclusão será confirmada no git diretamente

