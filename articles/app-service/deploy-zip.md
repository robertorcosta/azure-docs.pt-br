---
title: Implantar código com um arquivo ZIP ou WAR
description: Saiba como implantar seu aplicativo no Serviço de Aplicativo do Azure com um arquivo zip (ou arquivo WAR para desenvolvedores de Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945166"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Implantar seu aplicativo no Serviço de Aplicativo do Azure com um arquivo ZIP ou WAR

Este artigo mostra como usar um arquivo ZIP ou arquivo WAR para implantar seu aplicativo Web no [Serviço de Aplicativo do Azure](overview.md). 

Essa implantação de arquivo zip usa o mesmo serviço Kudu que alimenta implementações baseadas em integração contínua. O Kudu dá suporte para a seguinte funcionalidade para implantação de arquivo zip: 

- Exclusão de arquivos remanescentes de uma implantação anterior.
- Opção para ativar o processo de compilação padrão que inclui a restauração do pacote.
- Personalização da implantação, incluindo execução de scripts de implantação.  
- Logs de implantação. 
- Um limite de tamanho de arquivo de 2048 MB.

Para obter mais informações, consulte [Documentação do Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

A implantação do arquivo WAR implanta seu arquivo [WAR](https://wikipedia.org/wiki/WAR_(file_format)) ao Serviço de Aplicativo para executar seu aplicativo Web Java. Consulte [Implantar arquivo WAR](#deploy-war-file).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, [crie um aplicativo do serviço de aplicativo](/azure/app-service/)ou use um aplicativo que você criou para outro tutorial.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
O ponto de extremidade acima não funciona para os serviços de aplicativos do Linux no momento. Considere usar o FTP ou a [API de implantação de zip](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) em vez disso.

## <a name="deploy-zip-file-with-azure-cli"></a>Implantar arquivo ZIP com CLI do Azure

Implante o arquivo zip carregado no seu aplicativo Web usando o comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

O exemplo a seguir implanta o arquivo zip do upload que você fez. Ao usar uma instalação local da CLI do Azure, especifique o caminho para o arquivo zip local para `--src`.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Esse comando implanta os arquivos e diretórios do arquivo zip para sua pasta de aplicativo do Serviço de Aplicativo do Azure padrão (`\home\site\wwwroot`) e reinicia o aplicativo.

Por padrão, o mecanismo de implantação assume que um arquivo ZIP está pronto para ser executado no estado em que se encontra e não executa nenhuma automação de compilação. Para habilitar a mesma automação de compilação que em uma [implantação do git](deploy-local-git.md), defina a configuração do aplicativo `SCM_DO_BUILD_DURING_DEPLOYMENT` executando o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Para obter mais informações, consulte [Documentação do Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Implantar arquivo WAR

Para implantar um arquivo WAR no serviço de aplicativo, envie uma solicitação POST para `https://<app-name>.scm.azurewebsites.net/api/wardeploy`. A solicitação POST deve conter o arquivo .war no corpo da mensagem. As credenciais de implantação para seu aplicativo são fornecidas na solicitação usando a autenticação BÁSICA HTTP.

Sempre use `/api/wardeploy` ao implantar arquivos WAR. Essa API expandirá o arquivo WAR e o posicionará na unidade de arquivo compartilhada. o uso de outras APIs de implantação pode resultar em um comportamento inconsistente. 

Para a autenticação HTTP BÁSICA, você precisa das credenciais de implantação do Serviço de Aplicativo. Para ver como definir as credenciais de implantação, consulte [Definir e redefinir as credenciais de usuário](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Com o cURL

O exemplo a seguir usa a ferramenta cURL para implantar um arquivo .war. Substitua os espaços reservados `<username>`, `<war-file-path>` e `<app-name>`. Quando solicitado pelo cURL, digite a senha.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
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
