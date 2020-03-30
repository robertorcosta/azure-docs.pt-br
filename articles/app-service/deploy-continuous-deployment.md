---
title: configurar uma implantação contínua
description: Saiba como ativar o CI/CD no Azure App Service do GitHub, BitBucket, Azure Repos ou outros repos. Selecione o pipeline de construção que se ajusta às suas necessidades.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: d58eb333c930d2ffac4eb57340ea776338325181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266033"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implantação contínua no Serviço de Aplicativo do Azure

[O Azure App Service](overview.md) permite a implantação contínua dos repositórios GitHub, BitBucket e [Azure Repos,](https://azure.microsoft.com/services/devops/repos/) puxando as atualizações mais recentes. Este artigo mostra como usar o portal Azure para implantar continuamente seu aplicativo através do serviço de compilação Kudu ou [do Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Para obter mais informações sobre os serviços de controle de origem, consulte [Criar um repo (GitHub)], [Criar um repo (BitBucket)]ou [Criar um novo repo Git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizar o serviço de aplicativos do Azure 

Para usar os Azure Repos, certifique-se de que sua organização Azure DevOps esteja vinculada à sua assinatura do Azure. Para obter mais informações, consulte [Configurar uma conta do Azure DevOps Services para que ela possa ser implantada em um aplicativo web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

Para bitbucket ou GitHub, autorize o Azure App Service a se conectar ao seu repositório. Você só precisa autorizar com um serviço de controle de origem uma vez. 

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Serviços de Aplicativos.** 

   ![Procure serviços de App.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Selecione o aplicativo web que deseja implantar.

   ![Selecione seu aplicativo.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Na página do aplicativo, selecione **Centro de implantação** no menu à esquerda.
   
1. Na página **Centro de implantação,** selecione **GitHub** ou **Bitbucket**e selecione **Autorizar**. 
   
   ![Selecione o serviço de controle de origem e selecione Autorizar.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Faça login no serviço, se necessário, e siga as instruções de autorização. 

## <a name="enable-continuous-deployment"></a>Habilitar a implantação contínua 

Depois de autorizar um serviço de controle de origem, configure seu aplicativo para implantação contínua através do servidor de [compilação do Kudu App Service](#option-1-use-the-app-service-build-service)integrado ou através [do Azure Pipelines](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Opção 1: Use o serviço de construção do App Service

Você pode usar o servidor de compilação kudu app service incorporado para implantar continuamente a partir de Reséis Do GitHub, Bitbucket ou Azure. 

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Serviços de Aplicativos**e selecione o aplicativo web que deseja implantar. 
   
1. Na página do aplicativo, selecione **Centro de implantação** no menu à esquerda.
   
1. Selecione seu provedor de controle de origem autorizado na página **Do Centro de Implantação** e selecione **Continuar**. Para GitHub ou Bitbucket, você também pode selecionar **Alterar conta** para alterar a conta autorizada. 
   
   > [!NOTE]
   > Para usar os Azure Repos, certifique-se de que sua organização Azure DevOps Services esteja vinculada à sua assinatura do Azure. Para obter mais informações, consulte [Configurar uma conta do Azure DevOps Services para que ela possa ser implantada em um aplicativo web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. Para os Reptórios GitHub ou Azure, na página do **provedor build,** selecione **o serviço de construção do App Service**e selecione **Continuar**. O Bitbucket sempre usa o serviço de construção do App Service.
   
   ![Selecione serviço de compilação do aplicativo e selecione Continuar.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na página **Configurar:**
   
   - Para o GitHub, baixe e selecione a **Organização,** **Repositório**e **Branch** que deseja implantar continuamente.
     
     > [!NOTE]
     > Se você não ver nenhum repositório, talvez seja necessário autorizar o Azure App Service no GitHub. Navegue até o repositório do GitHub e vá para **Configurações** > **Aplicativos Autorizados** > **OAuth**. Selecione **o Serviço de Aplicativos Azure**e selecione **Grant**. Para repositórios de organização, você deve ser um proprietário da organização para conceder as permissões.
     
   - Em 'Bitbucket', selecione a **Equipe**Bitbucket , **Repositório**e **Branch** que deseja implantar continuamente.
     
   - Para os Reis Azure, selecione a **Organização DevOps do Azure,** **o Projeto,** **o Repositório**e **o Ramo** que deseja implantar continuamente.
     
     > [!NOTE]
     > Se a organização Azure DevOps não estiver listada, certifique-se de que ela esteja vinculada à sua assinatura do Azure. Para obter mais informações, consulte [Configurar uma conta do Azure DevOps Services para que ela possa ser implantada em um aplicativo web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)..
     
1. Selecione **Continuar**.
   
   ![Preencha as informações do repositório e selecione Continuar.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Depois de configurar o provedor de compilação, revise as configurações na página **Resumo** e selecione **Concluir**.
   
   Novos compromissos no repositório e filial selecionados agora são implantados continuamente no aplicativo App Service. Você pode acompanhar os compromissos e implantações na página **do Centro de Implantação.**
   
   ![Acompanhe confirmações e implantações no Centro de Implantação](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Opção 2: Use pipelines Azure 

Se sua conta tiver as permissões necessárias, você pode configurar o Azure Pipelines para implantar continuamente nos repositórios GitHub ou Azure Repos. Para obter mais informações sobre como implantar através do Azure Pipelines, consulte [Implantar um aplicativo web no Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Para que o Azure App Service crie pipelines Azure de entrega contínua em sua organização Azure DevOps: 

- Sua conta do Azure deve ter permissões para escrever no Azure Active Directory e criar um serviço. 
  
- Sua conta do Azure deve ter a função **Proprietário** em sua assinatura do Azure.

- Você deve ser um administrador no projeto Azure DevOps que deseja usar.

Para configurar o Azure Pipelines (Preview):

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Serviços de Aplicativos**e selecione o aplicativo web que deseja implantar. 
   
1. Na página do aplicativo, selecione **Centro de implantação** no menu à esquerda.
   
1. Na página **do provedor de compilação,** selecione **Azure Pipelines (Preview)** e selecione **Continuar**. 
   
1. Na **página Configurar,** na seção **Código:**
   
   - Para o GitHub, baixe e selecione a **Organização,** **Repositório**e **Branch** que deseja implantar continuamente.
     
     > [!NOTE]
     > Se você não ver nenhum repositório, talvez seja necessário autorizar o Azure App Service no GitHub. Navegue até o repositório do GitHub e vá para **Configurações** > **Aplicativos Autorizados** > **OAuth**. Selecione **o Serviço de Aplicativos Azure**e selecione **Grant**. Para repositórios de organização, você deve ser um proprietário da organização para conceder as permissões.
     
   - Para os Reis Do Zure, selecione a **Organização Azure DevOps,** **Projeto,** **Repositório**e **Ramo** que deseja implantar continuamente ou configure uma nova organização Azure DevOps.
     
     > [!NOTE]
     > Se a organização Existente do Azure DevOps não estiver listada, talvez seja necessário vinculá-la à sua assinatura do Azure. Para obter mais informações, consulte [Definir o pipeline de liberação de CDs](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Selecione **Continuar**.
   
1. Para os Reptórios Do Azure, na seção **Construir,** especifique a estrutura de idioma que o Azure Pipelines deve usar para executar tarefas de compilação e, em seguida, selecione **Continuar**.
   
1. Na página **Teste,** escolha se deve ativar os testes de carga e, em seguida, selecione **Continuar**.
   
1. Dependendo do nível [de preços do](https://azure.microsoft.com/pricing/details/app-service/plans/)plano de serviço do aplicativo, você pode ver uma página **implantar para staging.** Escolha se [deve ativar os slots de implantação](deploy-staging-slots.md)e, em seguida, selecione **Continuar**.
   
   > [!NOTE]
   > A Azure Pipelines não permite a entrega contínua para o slot de produção. Essa restrição impede implantações acidentais na produção. Configure a entrega contínua em um slot de preparação, verifique as alterações lá e, em seguida, troque os slots quando estiver pronto.
   
1. Depois de configurar o provedor de compilação, revise as configurações na página **Resumo** e selecione **Concluir**.
   
   Novos compromissos no repositório e filial selecionados agora são implantados continuamente no aplicativo App Service. Você pode acompanhar os compromissos e implantações na página **do Centro de Implantação.**
   
   ![Acompanhe confirmações e implantações no Centro de Implantação](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Desabilitar a implantação contínua

Para desativar a implantação contínua, **selecione Desconectar** na parte superior da página do Centro de **Implantação** do aplicativo.

![Desabilitar a implantação contínua](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Use repositórios sem suporte

Para aplicativos windows, você pode configurar manualmente a implantação contínua a partir de um git na nuvem ou repositório mercurial que o portal não suporta diretamente, como [o GitLab](https://gitlab.com/). Você faz isso escolhendo a caixa Externa na página **Centro de implantação.** Para obter mais informações, consulte [Configurar a implantação contínua usando etapas manuais](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Recursos adicionais

* [Investigue problemas comuns com implantação contínua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Usar PowerShell do Azure](/powershell/azureps-cmdlets-docs)
* [Documentação do Git](https://git-scm.com/documentation)
* [Kudu do Projeto](https://github.com/projectkudu/kudu/wiki)

[Criar um repositório (GitHub)]: https://help.github.com/articles/create-a-repo
[Criar um repositório (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Crie um novo repo Git (Azure Repos)]: /azure/devops/repos/git/creatingrepo
