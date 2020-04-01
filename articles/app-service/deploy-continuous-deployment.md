---
title: configurar uma implantação contínua
description: Saiba como ativar o CI/CD no Azure App Service do GitHub, BitBucket, Azure Repos ou outros repos. Selecione o pipeline de construção que se ajusta às suas necessidades.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 847de2c2c8916558d542473d9b7c80fd5552dbf7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437183"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Implantação contínua no Serviço de Aplicativo do Azure

[O Azure App Service](overview.md) permite a implantação contínua dos repositórios GitHub, BitBucket e [Azure Repos,](https://azure.microsoft.com/services/devops/repos/) puxando as atualizações mais recentes. Este artigo mostra como usar o portal Azure para implantar continuamente seu aplicativo através do serviço de compilação Kudu ou [do Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Para obter mais informações sobre os serviços de controle de origem, consulte [Criar um repo (GitHub)], [Criar um repo (BitBucket)]ou [Criar um novo repo Git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizar o serviço de aplicativos do Azure 

Para usar os Azure Repos, certifique-se de que sua organização Azure DevOps esteja vinculada à sua assinatura do Azure. Para obter mais informações, consulte [Configurar uma conta do Azure DevOps Services para que ela possa ser implantada em um aplicativo web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

Para bitbucket ou GitHub, autorize o Azure App Service a se conectar ao seu repositório. Você só precisa autorizar com um serviço de controle de origem uma vez. 

1. No [portal Azure,](https://portal.azure.com)procure **serviços de aplicativos** e selecione.

   ![Procure serviços de App.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Selecione o Serviço de Aplicativo que deseja implantar.

   ![Selecione seu aplicativo.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Na página do aplicativo, selecione **Centro de implantação** no menu à esquerda.
   
1. Na página **Centro de implantação,** selecione **GitHub** ou **Bitbucket**e selecione **Autorizar**. 
   
   ![Selecione o serviço de controle de origem e selecione Autorizar.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Faça login no serviço, se necessário, e siga as instruções de autorização. 

## <a name="enable-continuous-deployment"></a>Habilitar a implantação contínua 

Depois de autorizar um serviço de controle de origem, configure seu aplicativo para implantação contínua através do servidor de compilação [do Kudu App Service](#option-1-kudu-app-service) integrado ou através do [Azure Pipelines](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Opção 1: Kudu App Service

Você pode usar o servidor de compilação kudu app service incorporado para implantar continuamente a partir de Reséis Do GitHub, Bitbucket ou Azure. 

1. No [portal Azure](https://portal.azure.com), procure **serviços de aplicativos**e selecione o Serviço de Aplicativo que deseja implantar. 
   
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
   
1. Novos compromissos no repositório e filial selecionados agora são implantados continuamente no aplicativo App Service. Você pode acompanhar os compromissos e implantações na página **do Centro de Implantação.**
   
   ![Acompanhe confirmações e implantações no Centro de Implantação](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Opção 2: Azure Pipelines 

Se sua conta tiver as permissões necessárias, você pode configurar o Azure Pipelines para implantar continuamente no GitHub ou no Azure Repos. Para obter mais informações sobre como implantar através do Azure Pipelines, consulte [Implantar um aplicativo web no Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

#### <a name="prerequisites"></a>Pré-requisitos

Para que o Azure App Service crie uma entrega contínua usando o Azure Pipelines, a organização Azure DevOps deve ter as seguintes permissões: 

- Sua conta do Azure deve ter permissões para escrever no Azure Active Directory e criar um serviço. 
  
- Sua conta do Azure deve ter a função **Proprietário** em sua assinatura do Azure.

- Você deve ser um administrador no projeto Azure DevOps que deseja usar.

#### <a name="github--azure-pipelines"></a>GitHub + Azure Pipelines

1. No [portal Azure](https://portal.azure.com), procure **serviços de aplicativos**e selecione o Serviço de Aplicativo que deseja implantar. 
   
1. Na página do aplicativo, selecione **Centro de implantação** no menu à esquerda.

1. Selecione **o GitHub** como o provedor de controle de origem na página **do Centro de Implantação** e **selecione Continuar**. Para **o GitHub,** você pode selecionar **Alterar conta** para alterar a conta autorizada.

    ![controle do código-fonte](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. Na página **'Provedor de compilação',** **selecione Azure Pipelines (Preview)** e selecione **Continuar**.

    ![provedor de construção](media/app-service-continuous-deployment/select-build-provider.png)
   
1. Na página **Configurar,** na seção **Código,** selecione a **Organização,** **Repositório**e **Ramo** que deseja implantar continuamente e selecione **Continuar**.
     
     > [!NOTE]
     > Se você não ver nenhum repositório, talvez seja necessário autorizar o Azure App Service no GitHub. Navegue até o repositório do GitHub e vá para **Configurações** > **Aplicativos Autorizados** > **OAuth**. Selecione **o Serviço de Aplicativos Azure**e selecione **Grant**. Para repositórios de organização, você deve ser um proprietário da organização para conceder as permissões.
       
    Na seção **Construir,** especifique a organização, o projeto, a estrutura de idiomas do Azure Que o Azure Pipelines deve usar para executar tarefas de compilação e, em seguida, selecione **Continuar**.

   ![provedor de construção](media/app-service-continuous-deployment/build-configure.png)

1. Depois de configurar o provedor de compilação, revise as configurações na página **Resumo** e selecione **Concluir**.

   ![provedor de construção](media/app-service-continuous-deployment/summary.png)
   
1. Novos compromissos no repositório e filial selecionados agora são implantados continuamente no seu Serviço de Aplicativo. Você pode acompanhar os compromissos e implantações na página **do Centro de Implantação.**
   
   ![Acompanhe confirmações e implantações no Centro de Implantação](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos + Azure Pipelines

1. No [portal Azure](https://portal.azure.com), procure **serviços de aplicativos**e selecione o Serviço de Aplicativo que deseja implantar. 
   
1. Na página do aplicativo, selecione **Centro de implantação** no menu à esquerda.

1. Selecione **os Repos Do Azure** como o provedor de controle de origem na página **do Centro de Implantação** e **selecione Continuar**.

    ![controle do código-fonte](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. Na página **'Provedor de compilação',** **selecione Azure Pipelines (Preview)** e selecione **Continuar**.

    ![controle do código-fonte](media/app-service-continuous-deployment/azure-pipelines.png)

1. Na página **Configurar,** na seção **Código,** selecione a **Organização,** **Repositório**e **Ramo** que deseja implantar continuamente e selecione **Continuar**.

   > [!NOTE]
   > Se a organização Existente do Azure DevOps não estiver listada, talvez seja necessário vinculá-la à sua assinatura do Azure. Para obter mais informações, consulte [Definir o pipeline de liberação de CDs](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   Na seção **Construir,** especifique a organização, o projeto, a estrutura de idiomas do Azure Que o Azure Pipelines deve usar para executar tarefas de compilação e, em seguida, selecione **Continuar**.

   ![provedor de construção](media/app-service-continuous-deployment/build-configure.png)

1. Depois de configurar o provedor de compilação, revise as configurações na página **Resumo** e selecione **Concluir**.  
     
   ![provedor de construção](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. Novos compromissos no repositório e filial selecionados agora são implantados continuamente no seu Serviço de Aplicativo. Você pode acompanhar os compromissos e implantações na página **do Centro de Implantação.**

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
