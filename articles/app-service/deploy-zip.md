---
title: Implantar código com um arquivo ZIP ou WAR
description: Saiba como implantar seu aplicativo no Serviço de Aplicativo do Azure com um arquivo zip (ou arquivo WAR para desenvolvedores de Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 3569c6a066b09daa0c24975b9de840a844b6ba2c
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670219"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Implantar seu aplicativo no Serviço de Aplicativo do Azure com um arquivo ZIP ou WAR

Este artigo mostra como usar um arquivo ZIP ou arquivo WAR para implantar seu aplicativo Web no [Serviço de Aplicativo do Azure](overview.md). 

Essa implantação de arquivo zip usa o mesmo serviço Kudu que alimenta implementações baseadas em integração contínua. O Kudu dá suporte para a seguinte funcionalidade para implantação de arquivo zip: 

- Exclusão de arquivos remanescentes de uma implantação anterior.
- Opção para ativar o processo de compilação padrão que inclui a restauração do pacote.
- [Personalização da implantação](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), incluindo execução de scripts de implantação.  
- Logs de implantação. 
- Um limite de tamanho de arquivo de 2048 MB.

Para obter mais informações, consulte [Documentação do Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

A implantação do arquivo WAR implanta seu arquivo [WAR](https://wikipedia.org/wiki/WAR_(file_format)) ao Serviço de Aplicativo para executar seu aplicativo Web Java. Consulte [Implantar arquivo WAR](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar as etapas neste artigo:

* [Crie um aplicativo do Serviço de Aplicativo](/azure/app-service/) ou use um aplicativo que você criou para outro tutorial.

## <a name="create-a-project-zip-file"></a>Criar um projeto de arquivo zip

>[!NOTE]
> Se você fez o download dos arquivos em um arquivo zip, primeiro extraia os arquivos. Por exemplo, se você fez o download de um arquivo zip do GitHub, não será possível implantar esse arquivo. O GitHub adiciona diretórios aninhados adicionais que não funcionam com o Serviço de Aplicativo. 
>

Em uma janela do terminal local, navegue até o diretório raiz do projeto do aplicativo. 

Esse diretório deve conter o arquivo de entrada no seu aplicativo Web, como _index.html_, _index.php_ e _app.js_. Além disso, é possível conter arquivos de gerenciamento de pacotes como _project.json_, _composer.json_, _package.json_, _bower.json_ e _requirements.txt_.

Criar um arquivo zip de tudo em seu projeto. O comando a seguir usa a ferramenta padrão em seu terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
O ponto de extremidade acima não funciona para os serviços de aplicativos do Linux no momento. Considere usar o FTP ou a [API de implantação de zip](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) em vez disso.

## <a name="deploy-zip-file-with-azure-cli"></a>Implantar arquivo ZIP com CLI do Azure

Certifique-se de que sua versão da CLI do Azure seja 2.0.21 ou posterior. Para ver qual versão você possui, execute o comando `az --version` na janela do seu terminal.

Implante o arquivo zip carregado no seu aplicativo Web usando o comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

O exemplo a seguir implanta o arquivo zip do upload que você fez. Ao usar uma instalação local da CLI do Azure, especifique o caminho para o arquivo zip local para `--src`.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Esse comando implanta os arquivos e diretórios do arquivo zip para sua pasta de aplicativo do Serviço de Aplicativo do Azure padrão (`\home\site\wwwroot`) e reinicia o aplicativo.

Por padrão, o mecanismo de implantação assume que um arquivo ZIP está pronto para ser executado no estado em que se encontra e não executa nenhuma automação de compilação. Para habilitar a mesma automação de compilação que em uma [implantação do git](deploy-local-git.md), defina a configuração do aplicativo `SCM_DO_BUILD_DURING_DEPLOYMENT` executando o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```



Para obter mais informações, consulte [Documentação do Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Implantar arquivo WAR

Para implantar um arquivo WAR no serviço de aplicativo, envie uma solicitação POST para `https://<app_name>.scm.azurewebsites.net/api/wardeploy`. A solicitação POST deve conter o arquivo .war no corpo da mensagem. As credenciais de implantação para seu aplicativo são fornecidas na solicitação usando a autenticação BÁSICA HTTP.

Para a autenticação HTTP BÁSICA, você precisa das credenciais de implantação do Serviço de Aplicativo. Para ver como definir as credenciais de implantação, consulte [Definir e redefinir as credenciais de usuário](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Com o cURL

O exemplo a seguir usa a ferramenta cURL para implantar um arquivo .war. Substitua os espaços reservados `<username>`, `<war-file-path>` e `<app-name>`. Quando solicitado pelo cURL, digite a senha.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Com o PowerShell

O exemplo a seguir usa [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) para carregar o arquivo. War. Substitua os espaços reservados `<group-name>`, `<app-name>` e `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Próximos passos

Para cenários mais avançados de implantação, tente [implantação no Azure com Git](deploy-local-git.md). Implantação baseada em Git no Azure permite o controle de versão, restauração do pacote, MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Kudu: implementação de um arquivo zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Credenciais de implantação do Serviço de Aplicativo do Azure](deploy-ftp.md)
