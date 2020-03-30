---
title: Implantação por meio do repositório Git local
description: Saiba como habilitar a implantação do Git local no Serviço de Aplicativo do Azure. Uma das maneiras mais simples de implantar código de sua máquina local.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152985"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implantação local do Git para o Serviço de Aplicativos Do Azure

Este guia de como fazer mostra como implantar seu aplicativo no [Azure App Service](overview.md) a partir de um repositório git em seu computador local.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas neste guia de instruções:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Instale o Git](https://www.git-scm.com/downloads).
  
- Tenha um repositório Git local com código que deseja implantar. Para baixar um repositório de exemplo, execute o seguinte comando na janela do terminal local:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Implantar com o servidor de compilação Kudu

A maneira mais fácil de habilitar a implantação local do Git para o seu aplicativo com o servidor de compilação do Kudu App Service é usar o Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Obtenha o URL de implantação

Para obter a URL para habilitar a implantação [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) local do Git para um aplicativo existente, execute no Cloud Shell. Substitua \<o nome \<do aplicativo> e o nome do grupo> pelos nomes do seu aplicativo e do seu grupo de recursos Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Se você estiver usando um plano de serviço de aplicativo linux, você precisa adicionar este parâmetro: --runtime python|3.7


Ou, para criar um novo aplicativo habilitado para Git, execute [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) no Cloud Shell com o `--deployment-local-git` parâmetro. Substitua \<o nome \<do aplicativo>, o nome do grupo> e \<o nome do plano> pelos nomes do seu novo aplicativo Git, seu grupo de recursos Azure e seu plano de serviço de aplicativos Do Azure.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Qualquer comando retorna uma `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`URL como: . Use esta URL para implantar seu aplicativo na próxima etapa.

Em vez de usar essa URL de nível de conta, você também pode habilitar o Git local usando credenciais de nível de aplicativo. O Azure App Service gera automaticamente essas credenciais para cada aplicativo. 

Obtenha as credenciais do aplicativo executando o seguinte comando no Cloud Shell. Substitua \<o nome \<do aplicativo> e o nome do grupo> pelo nome do aplicativo e pelo nome do grupo de recursos do Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Use a URL que retorna para implantar seu aplicativo na próxima etapa.

### <a name="deploy-the-web-app"></a>Implantar o aplicativo Web

1. Abra uma janela de terminal local para o repositório git local e adicione um controle remoto Azure. No comando a \<seguir, substitua a url> pela URL específica do usuário de implantação ou url específica do aplicativo que você obteve da etapa anterior.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Empurre para o controle `git push azure master`remoto do Azure com . 
   
1. Na janela **Gerenciador de credenciais do Git,** digite sua senha de [usuário de implantação,](#configure-a-deployment-user)e não sua senha de login do Azure.
   
1. Examine a saída. Você pode ver automação específica de tempo de execução, como o MSBuild para ASP.NET, `npm install` para Node.js e `pip install` para Python. 
   
1. Navegue até o seu aplicativo no portal Azure para verificar se o conteúdo está implantado.

## <a name="deploy-with-azure-pipelines-builds"></a>Implantar com a compilação de pipelines azure

Se sua conta tiver as permissões necessárias, você pode configurar o Azure Pipelines (Preview) para habilitar a implantação local do Git para o seu aplicativo. 

- Sua conta do Azure deve ter permissões para escrever no Azure Active Directory e criar um serviço. 
  
- Sua conta do Azure deve ter a função **Proprietário** em sua assinatura do Azure.

- Você deve ser um administrador no projeto Azure DevOps que deseja usar.

Para habilitar a implantação local do Git para o seu aplicativo com o Azure Pipelines (Preview):

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Serviços de Aplicativos.** 

1. Selecione o aplicativo Azure App Service e selecione **Centro de Implantação** no menu à esquerda.
   
1. Na página **Centro de implantação,** selecione **Local Git**e selecione **Continuar**. 
   
   ![Selecione Local Git e selecione Continuar](media/app-service-deploy-local-git/portal-enable.png)
   
1. Na página **do provedor de compilação,** selecione **Azure Pipelines (Preview)** e selecione **Continuar**. 
   
   ![Selecione Azure Pipelines (Preview) e selecione Continuar.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Na página **Configurar,** configure uma nova organização Azure DevOps ou especifique uma organização existente e selecione **Continuar**.
   
   > [!NOTE]
   > Se a organização Existente do Azure DevOps não estiver listada, talvez seja necessário vinculá-la à sua assinatura do Azure. Para obter mais informações, consulte [Definir o pipeline de liberação de CDs](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. Dependendo do nível [de preços do](https://azure.microsoft.com/pricing/details/app-service/plans/)plano de serviço do aplicativo, você pode ver uma página **implantar para staging.** Escolha se [deve ativar os slots de implantação](deploy-staging-slots.md)e, em seguida, selecione **Continuar**.
   
1. Na página **Resumo,** revise as configurações e selecione **Concluir**.
   
1. Quando o Azure Pipeline estiver pronto, copie a URL do repositório git da página **do Centro de Implantação** para usar na próxima etapa. 
   
   ![Copie a URL do repositório git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Em sua janela de terminal local, adicione um controle remoto Do Zure ao seu repositório Git local. No comando, \<substitua a url> pela URL do repositório Git que você obteve da etapa anterior.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Empurre para o controle `git push azure master`remoto do Azure com . 
   
1. Na página **Do Gerenciador de Credenciais do Git,** faça login com seu visualstudio.com nome de usuário. Para outros métodos de autenticação, consulte [visão geral da autenticação do Azure DevOps Services](/vsts/git/auth-overview?view=vsts).
   
1. Uma vez concluída a implantação, `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`veja o progresso `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`da compilação em , e o progresso da implantação em .
   
1. Navegue até o seu aplicativo no portal Azure para verificar se o conteúdo está implantado.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Implantação de solução de problemas

Você pode ver as seguintes mensagens de erro comuns quando você usa o Git para publicar em um aplicativo do App Service no Azure:

|Mensagem|Causa|Resolução
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|O aplicativo não está funcionando.|inicie o aplicativo no portal do Azure. A implantação do Git não está disponível quando o aplicativo web é interrompido.|
|`Couldn't resolve host 'hostname'`|As informações de endereço do controle remoto 'azure' estão incorretas.|use o comando `git remote -v` para listar todos os remotes (remotos), juntamente com a URL associada. Verifique se a URL do remote do 'azure' está correta. Se necessário, remova e recrie esse remoto usando a URL correta.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Você não especificou `git push`um ramo durante , `push.default` ou `.gitconfig`você não definiu o valor em .|Execute `git push` novamente, especificando o `git push azure master`ramo mestre: .|
|`src refspec [branchname] does not match any.`|Você tentou empurrar para um ramo que não seja mestre no controle remoto "azule".|Execute `git push` novamente, especificando o `git push azure master`ramo mestre: .|
|`RPC failed; result=22, HTTP code = 5xx.`|esse erro poderá ocorrer se você tentar efetuar push de um repositório Git de grande porte por HTTPS.|Altere a configuração git na `postBuffer` máquina local para tornar a maior. Por exemplo: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Você implantou um aplicativo Node.js com um arquivo _package.json_ que especifica módulos necessários adicionais.|Revise `npm ERR!` as mensagens de erro antes deste erro para obter mais contexto sobre a falha. As seguintes são as causas conhecidas `npm ERR!` deste erro e as mensagens correspondentes:<br /><br />**Arquivo pack.json malformado:**`npm ERR! Couldn't read dependencies.`<br /><br />**O módulo nativo não tem uma distribuição binária para Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />ou <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Recursos adicionais

- [Documentação do projeto Kudu](https://github.com/projectkudu/kudu/wiki)
- [Implantação contínua do Serviço de Aplicativos Do Azure](deploy-continuous-deployment.md)
- [Exemplo: Crie um aplicativo web e implante código a partir de um repositório git local (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Exemplo: Crie um aplicativo web e implante código a partir de um repositório Git local (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
