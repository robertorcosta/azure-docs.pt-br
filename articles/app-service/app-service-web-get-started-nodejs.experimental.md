---
title: 'Início Rápido: Criar um aplicativo Web Node.js'
description: Implante seu primeiro Olá, Mundo em Node.js no Serviço de Aplicativo do Azure em minutos. Você fará a implantação usando um pacote zip, que é um dos vários modos de implantação no Serviço de Aplicativo.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/23/2019
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 585eb1f11a285722e34c6dd084f4c6cb26a06249
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247615"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Criar um aplicativo Web do Node.js no Azure

> [!NOTE]
> Este artigo implanta um aplicativo no Serviço de Aplicativo no Windows. Para implantar o Serviço de Aplicativo em _Linux_, consulte [Criar um aplicativo Web Node.js no Serviço de Aplicativo do Azure em Linux](./containers/quickstart-nodejs.md).
>

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches.  Este início rápido mostra como implantar um aplicativo Node.js no Serviço de Aplicativo do Azure. Você cria o aplicativo Web usando o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), mas você também pode executar esses comandos localmente com a [CLI do Azure](/cli/azure/install-azure-cli). Implante o código de exemplo do Node.js no aplicativo Web usando o comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

![Aplicativo de exemplo em execução no Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Você pode seguir as etapas aqui usando um computador Mac, Windows ou Linux. Leva aproximadamente três minutos para concluir as etapas.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

No Cloud Shell, crie um diretório de início rápido e depois altere-o.

```console
mkdir quickstart

cd $HOME/quickstart
```

Em seguida, execute o comando a seguir para clonar o repositório de aplicativos de exemplo ao seu diretório de início rápido.

```console
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Na execução, ele exibe informações semelhantes ao seguinte exemplo:

```output
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
```

> [!NOTE]
> O index.js de exemplo define a porta de escuta para process.env.PORT. Essa variável de ambiente é atribuída pelo Serviço de Aplicativo.
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>Criar um aplicativo Web

No Cloud Shell, crie um aplicativo Web no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create).

No exemplo a seguir, substitua `<app_name>` por um nome do aplicativo exclusivo globalmente (os caracteres válidos são `a-z`, `0-9` e `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="set-nodejs-runtime"></a>Definir runtime do Node.js

Defina o runtime do Node como 10.14.1. Para ver todos os runtimes com suporte, execute [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes).

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

Navegue até o aplicativo Web recém-criado. Substitua `<app_name>` por um nome de aplicativo exclusivo.

```http
http://<app_name>.azurewebsites.net
```

Seu novo aplicativo Web deve ficar assim: ![Página de aplicativo Web vazia](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>Implantar arquivo ZIP

No Cloud Shell, navegue até o diretório raiz do seu aplicativo e crie um novo arquivo zip para o projeto de exemplo.

```console
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

Implante o arquivo zip no seu aplicativo Web usando o comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Esse comando implanta os arquivos e diretórios do arquivo zip para sua pasta de aplicativo do Serviço de Aplicativo do Azure padrão (`\home\site\wwwroot`) e reinicia o aplicativo. Se qualquer processo de compilação personalizada adicional for configurado, ele também será executado. Para obter mais informações, consulte [Documentação do Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado usando o navegador da Web.

```http
http://<app_name>.azurewebsites.net
```

O código de exemplo do Node.js está em execução em um aplicativo Web do Serviço de Aplicativo do Azure.

![Aplicativo de exemplo em execução no Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

> [!NOTE]
> No Serviço de Aplicativo do Azure, o aplicativo é executado no IIS usando o [iisnode](https://github.com/Azure/iisnode). Para habilitar a execução do aplicativo com o iisnode, o diretório raiz do aplicativo contém um arquivo web.config. O arquivo pode ser lido pelos IIS, e as configurações relacionadas ao iisnode são documentadas no [repositório GitHub iisnode](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config).

**Parabéns!** Você implantou seu primeiro aplicativo do Node.js no Serviço de Aplicativo.

## <a name="update-and-redeploy-the-code"></a>Atualizar e reimplantar o código

No Cloud Shell, digite `code index.js` para abrir o editor do Cloud Shell.

![Código index.js](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

Faça uma pequena alteração no texto na chamada para `response.end`:

```javascript
response.end("Hello Azure!");
```

Salve suas alterações e saia do editor. Use o comando `^S` para salvar e `^Q` para sair.

Crie um arquivo zip e implante-o usando o comando [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Mude para a janela do navegador aberta na etapa **Navegar até o aplicativo** e atualize a página.

![Aplicativo de exemplo atualizado em execução no Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Gerenciar seu novo aplicativo do Azure

Vá para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo Web que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

A página Visão Geral do seu aplicativo Web é exibida. Aqui você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir.

![Página Serviço de Aplicativo no portal do Azure](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

O menu à esquerda fornece páginas diferentes para configurar seu aplicativo.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Node.js com o MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
