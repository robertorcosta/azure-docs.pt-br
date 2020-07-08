---
title: Configurar aplicativos do Windows Node.js
description: Saiba como configurar um aplicativo Node.js nas instâncias nativas do Windows do serviço de aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907856"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Configurar um aplicativo do Windows Node.js para o serviço Azure App

Node.js aplicativos devem ser implantados com todas as dependências de NPM necessárias. O mecanismo de implantação do serviço de aplicativo é executado automaticamente `npm install --production` quando você implanta um [repositório git](deploy-local-git.md)ou um [pacote zip](deploy-zip.md) com a automação de compilação habilitada. No entanto, se você implantar seus arquivos usando [FTP/S](deploy-ftp.md), será necessário carregar os pacotes necessários manualmente. Para obter informações sobre aplicativos do Linux, consulte [configurar um aplicativo do Linux php para Azure app serviço](containers/configure-language-nodejs.md).

Este guia fornece os principais conceitos e instruções para os desenvolvedores de Node.js que se implantam no serviço de aplicativo. Se você nunca usou Azure App serviço, siga o guia de [início rápidoNode.js](app-service-web-get-started-nodejs.md) e [Node.js com o MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md) primeiro.

## <a name="show-nodejs-version"></a>Mostrar versão de Node.js

Para mostrar a versão atual do Node.js, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Para mostrar todas as versões de Node.js com suporte, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Definir Node.js versão

Para definir seu aplicativo para uma [versão Node.js com suporte](#show-nodejs-version), execute o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir `WEBSITE_NODE_DEFAULT_VERSION` como uma versão com suporte:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Essa configuração especifica a versão de Node.js a ser usada, em tempo de execução e durante a restauração automatizada de pacote durante a automação de compilação do serviço de aplicativo.

> [!NOTE]
> Você deve definir a versão Node.js no seu projeto `package.json` . O mecanismo de implantação é executado em um processo separado que contém todas as versões de Node.js com suporte.

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](configure-common.md) fora do código do aplicativo. Em seguida, você pode acessá-los usando o padrão de Node.js padrão. Por exemplo, para acessar uma configuração de aplicativo chamada `NODE_ENV`, use o seguinte código:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Executar Grunt/Bower/Gulp

Por padrão, a automação de compilação do serviço de aplicativo é executada `npm install --production` quando reconhece um aplicativo Node.js é implantada por meio do git (ou implantação de zip com a automação de compilação habilitada). Se seu aplicativo exigir qualquer uma das ferramentas de automação populares, como Grunt, Bower ou Gulp, você precisará fornecer um [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) para executá-lo.

Para habilitar seu repositório para executar essas ferramentas, você precisa adicioná-las às dependências no *package.jsno.* Por exemplo:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Em uma janela de terminal local, altere o diretório para a raiz do repositório e execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A raiz do repositório agora tem dois arquivos adicionais: *. Deployment* e *Deploy.sh*.

Abra *Deploy.sh* e localize a `Deployment` seção, que é semelhante a esta:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Esta seção termina com em execução `npm install --production` . Adicione a seção de código de que você precisa para executar a ferramenta necessária *no final* da `Deployment` seção:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Veja um [exemplo no exemplo de MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), em que o script de implantação também executa um `npm install` comando personalizado.

### <a name="bower"></a>Bower

Este trecho de código é executado `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Este trecho de código é executado `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Este trecho de código é executado `grunt` .

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

Estrutura Web populares permitem que você acesse informações do `X-Forwarded-*` no seu padrão de aplicativo básico. No [Express](https://expressjs.com/), você pode usar [proxies de confiança](https://expressjs.com/guide/behind-proxies.html). Por exemplo:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Solução de problemas

Quando um aplicativo Node.js funcionando se comporta de forma diferente no serviço de aplicativo ou tem erros, tente o seguinte:

- [Acessar o fluxo de log](#access-diagnostic-logs).
- Teste o aplicativo localmente no modo de produção. O Serviço de Aplicativo executa seus aplicativos do Node.js no modo de produção, portanto, verifique se seu projeto funciona como esperado localmente no modo de produção. Por exemplo:
    - Dependendo do seu *package.jsno*, pacotes diferentes podem ser instalados para o modo de produção ( `dependencies` vs. `devDependencies` ).
    - Determinadas estruturas da Web podem implantar arquivos estáticos de maneira diferente no modo de produção.
    - Determinadas estruturas da Web podem usar scripts de inicialização personalizados ao serem executados no modo de produção.
- Execute seu aplicativo no serviço de aplicativo no modo de desenvolvimento. Por exemplo, em [MEAN.js](https://meanjs.org/), você pode definir seu aplicativo para o modo de desenvolvimento em tempo de execução [definindo a `NODE_ENV` configuração do aplicativo](configure-common.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Node.js com o MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

