---
title: Configure aplicativos Node.js
description: Saiba como configurar um contêiner Node.js pré-construído para o seu aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252721"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configure um aplicativo Linux Node.js para o Azure App Service

Os aplicativos Node.js devem ser implantados com todas as dependências npm necessárias. O mecanismo de implantação do App `npm install --production` Service (Kudu) é executado automaticamente para você quando você implanta um [repositório Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)ou um [pacote Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de compilação ligados. Se você implantar seus arquivos usando [FTP/S,](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)no entanto, você precisa carregar os pacotes necessários manualmente.

Este guia fornece conceitos e instruções importantes para os desenvolvedores do Node.js que usam um contêiner Linux incorporado no App Service. Se você nunca usou o Azure App Service, siga o [Node.js quickstart](quickstart-nodejs.md) e [node.js com tutorial do MongoDB](tutorial-nodejs-mongodb-app.md) primeiro.

## <a name="show-nodejs-version"></a>Mostrar versão node.js

Para mostrar a versão atual do Node.js, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões node.js suportadas, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Definir versão Node.js

Para definir seu aplicativo em uma [versão node.js suportada,](#show-nodejs-version)execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Esta configuração especifica a versão Node.js para usar, tanto em tempo de execução quanto durante a restauração automatizada do pacote em Kudu.

> [!NOTE]
> Você deve definir a versão Node.js `package.json`na versão do seu projeto. O motor de implantação funciona em um recipiente separado que contém todas as versões node.js suportadas.

## <a name="customize-build-automation"></a>Personalize a automação de compilação

Se você implantar seu aplicativo usando pacotes Git ou zip com automação de compilação ligada, o App Service construirá etapas de automação através da seguinte seqüência:

1. Executar o script personalizado, se especificado por `PRE_BUILD_SCRIPT_PATH`.
1. Executar `npm install` sem bandeiras, que `preinstall` `postinstall` inclui npm e `devDependencies`scripts e também instala .
1. Execute `npm run build` se um script de compilação for especificado em seu *package.json*.
1. Executar `npm run build:azure` se um script build:azure for especificado em seu *package.json*.
1. Executar o script personalizado, se especificado por `POST_BUILD_SCRIPT_PATH`.

> [!NOTE]
> Como descrito em [npm docs,](https://docs.npmjs.com/misc/scripts)scripts nomeados `prebuild` e `postbuild` executados antes e depois, `build`respectivamente, se especificado. `preinstall`e `postinstall` correr antes `install`e depois, respectivamente.

`PRE_BUILD_COMMAND`e `POST_BUILD_COMMAND` são variáveis de ambiente que estão vazias por padrão. Para executar comandos de `PRE_BUILD_COMMAND`pré-construção, defina . Para executar comandos pós-compilação, defina `POST_BUILD_COMMAND`.

O exemplo a seguir especifica as duas variáveis para uma série de comandos, separados por commas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para obter variáveis adicionais de ambiente para personalizar a automação de construção, consulte [a configuração Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o App Service executa e constrói aplicativos Node.js no Linux, consulte [a documentação oryx: como os aplicativos Node.js são detectados e construídos](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Configurar o servidor Node.js

Os contêineres Node.js vêm com [PM2](https://pm2.keymetrics.io/), um gerente de processo de produção. Você pode configurar seu aplicativo para começar com PM2, ou com NPM, ou com um comando personalizado.

- [Executar comando personalizado](#run-custom-command)
- [Executar npm start](#run-npm-start)
- [Executar com PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Executar comando personalizado

O App Service pode iniciar seu aplicativo usando um comando personalizado, como um executável como *run.sh*. Por exemplo, `npm run start:prod`para executar, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Executar npm start

Para iniciar o `npm start`uso do `start` aplicativo, certifique-se de que um script está no arquivo *package.json.* Por exemplo: 

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Para usar um *pacote personalizado.json* em seu projeto, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Executar com PM2

O contêiner inicia automaticamente seu aplicativo com PM2 quando um dos arquivos node.js comuns é encontrado em seu projeto:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Um dos seguintes [arquivos PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* e *ecosystem.config.js*

Você também pode configurar um arquivo de início personalizado com as seguintes extensões:

- Um arquivo *.js*
- Um [arquivo PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) com a extensão *.json,* *.config.js,* *.yaml*, ou *.yml*

Para adicionar um arquivo de início personalizado, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Depurar remotamente

> [!NOTE]
> A depuração remota está atualmente em Visualização.

Você pode depurar seu aplicativo Node.js remotamente no [Visual Studio Code](https://code.visualstudio.com/) se você configurá-lo para ser executado com [PM2](#run-with-pm2), exceto quando você executá-lo usando um *.config.js, *.yml ou *.yaml*.

Na maioria dos casos, nenhuma configuração extra é necessária para o seu aplicativo. Se o seu aplicativo for executado com um arquivo *process.json* (padrão ou personalizado), ele deve ter uma `script` propriedade na raiz JSON. Por exemplo: 

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Para configurar o Visual Studio Code para depuração remota, instale a [extensão App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Siga as instruções na página de extensão e faça login no Azure no Visual Studio Code.

No explorador Azure, encontre o aplicativo que deseja depurar, clique com o botão direito do mouse e selecione **Iniciar depuração remota**. Clique **em Sim** para habilitá-lo para o seu aplicativo. O App Service inicia um proxy de túnel para você e anexa o depurador. Em seguida, você pode fazer solicitações para o aplicativo e ver o depurador parando em pontos de interrupção.

Uma vez terminado com a depuração, pare o depurador selecionando **Desconectar**. Quando solicitado, você deve clicar **em Sim** para desativar a depuração remota. Para desativá-lo mais tarde, clique com o botão direito do mouse no aplicativo novamente no explorador Azure e **selecione Desativar depuração remota**.

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do código do aplicativo. Então você pode acessá-los usando o padrão Node.js padrão. Por exemplo, para acessar uma configuração de aplicativo chamada `NODE_ENV`, use o seguinte código:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Executar Grunt/Bower/Gulp

Por padrão, o `npm install --production` Kudu é executado quando reconhece que um aplicativo Node.js é implantado. Se o seu aplicativo precisar de alguma das ferramentas populares de automação, como Grunt, Bower ou Gulp, você precisa fornecer um [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) para executá-lo.

Para habilitar seu repositório para executar essas ferramentas, você precisa adicioná-las às dependências do *package.json.* Por exemplo: 

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

A partir de uma janela de terminal local, altere o diretório para a raiz do repositório e execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Sua raiz de repositório agora tem dois arquivos adicionais: *.deployment* e *deploy.sh*.

Abra *deploy.sh* e `Deployment` encontre a seção, que se parece com isso:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Esta seção `npm install --production`termina com a execução. Adicione a seção de código necessária para executar `Deployment` a ferramenta necessária no *final* da seção:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Veja um [exemplo na amostra MEAN.js,](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)onde o `npm install` script de implantação também executa um comando personalizado.

### <a name="bower"></a>Bower

Este trecho é `bower install`executado.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Este trecho é `gulp imagemin`executado.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Este trecho é `grunt`executado.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No Serviço de Aplicativo, a [Terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede de modo que todas as solicitações HTTPS cheguem ao seu aplicativo como solicitações HTTP não criptografadas. Se a lógica de aplicativo precisar verificar se as solicitações do usuário estão criptografadas ou não, inspecione o cabeçalho `X-Forwarded-Proto`.

Estrutura Web populares permitem que você acesse informações do `X-Forwarded-*` no seu padrão de aplicativo básico. No [Express,](https://expressjs.com/)você pode usar [proxies de confiança](https://expressjs.com/guide/behind-proxies.html). Por exemplo: 

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão SSH aberta no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Solução de problemas

Quando um aplicativo Node.js em funcionamento se comportar de forma diferente no App Service ou tiver erros, tente o seguinte:

- [Acessar o fluxo de log](#access-diagnostic-logs).
- Teste o aplicativo localmente no modo de produção. O App Service executa seus aplicativos Node.js no modo de produção, então você precisa ter certeza de que seu projeto funciona como esperado no modo de produção localmente. Por exemplo: 
    - Dependendo do *pacote.json,* diferentes pacotes podem ser`dependencies` instalados para o modo de produção (vs. `devDependencies`).
    - Certos frameworks da Web podem implantar arquivos estáticos de forma diferente no modo de produção.
    - Certos frameworks da Web podem usar scripts de inicialização personalizados ao ser executados no modo de produção.
- Execute seu aplicativo no App Service no modo de desenvolvimento. Por exemplo, em [MEAN.js,](https://meanjs.org/)você pode definir seu aplicativo para o modo de desenvolvimento em tempo de [execução, definindo a configuração `NODE_ENV` do aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Node.js com MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](app-service-linux-faq.md)
