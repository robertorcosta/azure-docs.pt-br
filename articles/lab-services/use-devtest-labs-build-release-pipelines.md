---
title: Usar o DevTest Labs no build do Azure Pipelines e em pipelines de lançamento
description: Saiba como usar o Azure DevTest Labs em Azure Pipelines para construir e liberar pipelines.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169232"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Usar o DevTest Labs no build do Azure Pipelines e em pipelines de lançamento
Este artigo fornece informações sobre como o DevTest Labs pode ser usado em pipelines de construção e lançamento de pipelines do Azure Pipelines. 

## <a name="overall-flow"></a>Fluxo geral
O fluxo básico é ter um **pipeline de construção** que faça as seguintes tarefas:

1. Construa o código da aplicação.
1. Crie o ambiente base em DevTest Labs.
1. Atualize o ambiente com informações personalizadas.
1. Implantar o aplicativo no ambiente DevTest Labs
1. Teste o código. 

Uma vez que a compilação tenha sido concluída com sucesso, o **oleoduto de liberação** usará os artefatos de construção para implantar a encenação ou a produção. 

Uma das premissas necessárias é que todas as informações necessárias para recriar o ecossistema testado estão disponíveis dentro dos artefatos de construção, incluindo a configuração dos recursos do Azure. Como os recursos do Azure incorrem em um custo quando usados, as empresas querem controlar ou rastrear o uso desses recursos. Em algumas situações, os modelos do Azure Resource Manager usados para criar e configurar os recursos podem ser gerenciados por outro departamento como a TI. E, esses modelos podem ser armazenados em um repositório diferente. Isso leva a uma situação interessante onde uma compilação será criada e testada, e tanto o código quanto a configuração precisarão ser armazenados dentro dos artefatos de construção para recriar adequadamente o sistema em produção. 

Usando o DevTest Labs durante a fase de compilação/teste, você pode adicionar modelos do Azure Resource Manager e arquivos de suporte às fontes de compilação para que durante a fase de lançamento a configuração exata usada para testar seja implantada na produção. A **tarefa Criar Azure DevTest Labs Environment** com a configuração adequada salvará os modelos do Gerenciador de recursos dentro dos artefatos de compilação. Para este exemplo, você estará usando o código do [Tutorial: Crie um aplicativo web .NET Core e SQL Database no Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md), para implantar e testar o aplicativo web no Azure.

![Fluxo geral](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure
Há alguns itens que precisam ser criados de antemão:

- Dois repositórios. O primeiro com o código do tutorial e um modelo de Gerenciador de Recursos com duas VMs adicionais. O segundo conterá o modelo base Azure Resource Manager (configuração existente).
- Um grupo de recursos para implantação do código de produção e configuração.
- Um laboratório precisa ser configurado com uma [conexão com o repositório de configuração](devtest-lab-create-environment-from-arm.md) para o pipeline de construção. O modelo resource manager precisa ser verificado no repositório de configuração como azuredeploy.json com o metadata.json para permitir que o DevTest Labs reconheça e implante o modelo.

O pipeline de compilação criará um ambiente DevTest Labs e implantará o código para testes.

## <a name="set-up-a-build-pipeline"></a>Monte um pipeline de construção
No Azure Pipelines, crie um pipeline de compilação usando o código do [Tutorial: Crie um aplicativo web .NET Core e SQL Database no Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md). Use o modelo **ASP.NET Core,** que preencherá a tarefa necessária para construir, testar e publicar o código.

![Selecione o modelo ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Você precisa adicionar três tarefas adicionais para criar o ambiente no DevTest Labs e implantar no ambiente.

![Pipeline com três tarefas](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Criar tarefa de ambiente
Na tarefa criar ambiente **(a tarefa Azure DevTest Labs Criar ambiente)** use as listas de saque para selecionar os seguintes valores:

- Assinatura do Azure
- nome do laboratório
- nome do repositório
- nome do modelo (que mostra a pasta onde o ambiente é armazenado). 

Recomendamos que você use listas de baixa na página em vez de inserir as informações manualmente. Se você inserir manualmente as informações, digite IDs de recursos do Azure totalmente qualificados. A tarefa exibe os nomes amigáveis em vez de ids de recurso. 

O nome do ambiente é o nome exibido mostrado no DevTest Labs. Deve ser um nome único para cada compilação. Por exemplo: **TestEnv$(Build.BuildId)**. 

Você pode especificar parâmetros de arquivo ou parâmetros para passar informações para o modelo do Gerenciador de recursos. 

Selecione as variáveis Criar saída com base na opção **de saída do modelo de ambiente** e digite um nome de referência. Para este exemplo, **digite BaseEnv** para obter o nome de referência. Você usará este BaseEnv ao configurar a próxima tarefa. 

![Crie a tarefa de ambiente do Azure DevTest Labs](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Preencher tarefa de ambiente
A segunda tarefa (**Azure DevTest Labs Populate Environment** task) é atualizar o ambiente existente do DevTest Labs. A tarefa Criar saídas de ambiente **BaseEnv.environmentResourceId** que é usada para configurar o nome do ambiente para essa tarefa. O modelo do Gerenciador de recursos para este exemplo tem dois parâmetros - **adminUserName** e **adminPassword**. 

![Preencher a tarefa de ambiente do Azure DevTest Labs](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Serviço de aplicativo implantar tarefa
A terceira tarefa é a **tarefa Azure App Service Deploy.** O tipo de aplicativo é definido como **Web App** e o nome do App Service é definido **como $(WebSite)**.

![Tarefa de implantação do serviço de aplicativos](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Configurar o pipeline de liberação
Você cria um pipeline de versão com duas tarefas: **Implantação do Azure: Criar ou atualizar o grupo de recursos** e implantar o serviço de aplicativos do **Azure.** 

Para a primeira tarefa, especifique o nome e a localização do grupo de recursos. A localização do modelo é um artefato ligado. Se o modelo do Gerenciador de recursos incluir modelos vinculados, uma implantação de grupo de recursos personalizado susta a implantação será implementada. O modelo está no artefato de gota publicado. Substituir parâmetros do modelo para o modelo do Gerenciador de recursos. Você pode deixar as configurações restantes com valores padrão. 

Para a segunda tarefa, implante o **serviço de aplicativos do Azure,** especifique a assinatura do Azure, selecione o Web **App** para o **tipo App**e **$(WebSite)** para o **nome do Serviço de Aplicativo**. Você pode deixar as configurações restantes com valores padrão. 

## <a name="test-run"></a>Execução de teste
Agora que ambos os gasodutos estão configurados, faça fila manual de uma compilação e veja-a funcionar. O próximo passo é definir o gatilho apropriado para a construção e conectar a compilação ao pipeline de liberação.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Integre o Azure DevTest Labs em seu pipeline de integração e entrega contínua do Azure Pipelines](devtest-lab-integrate-ci-cd-vsts.md)
- [Integre ambientes em seus gasodutos CI/CD do Azure Pipelines](integrate-environments-devops-pipeline.md)
