---
title: Conecte seu computador de desenvolvimento a um cluster AKS (visualização)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Saiba como conectar seu computador de desenvolvimento a um cluster AKS com o Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235016"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>Conecte seu computador de desenvolvimento a um cluster AKS (visualização)

O Azure Dev Spaces permite que você execute e depura código com ou sem um contêiner em seu computador de desenvolvimento, enquanto ainda estiver conectado ao seu cluster Kubernetes com o resto de sua aplicação ou serviços. Conectar seu computador de desenvolvimento ao cluster ajuda você a desenvolver rapidamente seu aplicativo e realizar testes de ponta a ponta sem ter que criar nenhuma configuração do Docker ou do Kubernetes. Você também pode se conectar ao seu cluster AKS sem afetar outras cargas de trabalho ou usuários que possam estar usando o mesmo cluster.

O Azure Dev Spaces redireciona o tráfego entre o cluster AKS conectado e o computador de desenvolvimento. Esse redirecionamento de tráfego permite que o código em seu computador de desenvolvimento e serviços em execução em seu cluster AKS se comuniquecomo se estivessem no mesmo cluster AKS. Uma vez que seu código está sendo executado em seu computador de desenvolvimento, você também tem flexibilidade nas ferramentas de desenvolvimento que você está usando para executar e depurar esse código. O Azure Dev Spaces também fornece uma maneira de replicar variáveis de ambiente e arquivos montados disponíveis para pods em seu cluster AKS em seu computador de desenvolvimento.

Neste guia, você aprenderá a:

* Configurar o Azure Dev Spaces em um cluster Kubernetes gerenciado no Azure.
* Implantar um aplicativo grande com vários microsserviços em um espaço de desenvolvimento.
* Use o Azure Dev Spaces para redirecionar o tráfego entre o cluster AKS e o código em execução no computador de desenvolvimento.

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="before-you-begin"></a>Antes de começar

Este guia usa o [aplicativo de exemplo Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar a conexão do seu computador de desenvolvimento a um cluster AKS. Siga as instruções no [aplicativo de amostra Azure Dev Spaces Bike Sharing README](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) para executar o aplicativo de amostra. Alternativamente, se você tiver seu próprio aplicativo em um cluster AKS, você ainda pode seguir as etapas abaixo e usar os nomes de seus próprios serviços e pods.

### <a name="limitations"></a>Limitações

* O UDP não é suportado no momento.

### <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure][azure-cli] instalada.
* [Visual Studio Code][vs-code] com a extensão [Azure Dev Spaces][azds-vs-code] instalada e em execução no MacOS ou Windows 10.
* O [aplicativo de exemplo Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) ou seu próprio aplicativo em execução em um cluster AKS.

## <a name="connect-your-development-computer"></a>Conecte seu computador de desenvolvimento

Abra *espaços de desenvolvimento/amostras/BikeSharingApp/Bikes* no Visual Studio Code e use a extensão Azure Dev Spaces para conectar seu computador de desenvolvimento ao seu cluster AKS.

Para usar a extensão Azure Dev Spaces, abra a paleta de comando no Visual Studio Code clicando em *Exibir* e depois *na paleta de comando*. Comece `Azure Dev Spaces: Redirect` a digitar `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`e `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`clique `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`em qualquer um, ou .

![Comandos](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Selecione uma opção de redirecionamento

Se você `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`for executado, você é solicitado a escolher um serviço Kubernetes existente:

![Escolha o serviço](../media/how-to-connect/connect-choose-service.png)

Esta opção redireciona todo o tráfego no cluster AKS para este serviço para a versão do seu aplicativo em execução em seu computador de desenvolvimento. Se este serviço tiver vários pods em execução no cluster AKS, todo o tráfego para este serviço será encaminhado apenas para o seu computador de desenvolvimento. O Azure Dev Spaces também encaminha todo o tráfego de saída do aplicativo de volta para o cluster AKS.

Se você `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`for executado, você é solicitado a escolher um pod específico:

![Escolha pod](../media/how-to-connect/connect-choose-pod.png)

Esta opção se conecta a um pod específico. Esta opção é útil para interagir com pods que não enviam ou recebem tráfego e replicam pods extintos. Se o pod enviar e receber tráfego, essa opção `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` se comporta de forma semelhante e redirecionará todo o tráfego no cluster AKS para todos os pods relacionados ao serviço do pod selecionado.

Se você `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`for executado, você não será solicitado a selecionar um pod ou serviço existente. Esta opção redireciona todo o tráfego de saída do aplicativo em execução no seu computador de desenvolvimento para o cluster AKS.

Para este exemplo, escolha `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` e selecione o serviço de *bicicletas.*

### <a name="select-a-connection-mode"></a>Selecione um modo de conexão

Depois de selecionar sua opção de redirecionamento, você é solicitado a escolher o modo *de conexão Substituir* ou *Clonar.*

![Substituir ou Clonar](../media/how-to-connect/connect-replace-clone.png)

A opção *Substituir* substitui o pod ou serviço atual no cluster AKS e redireciona todo o tráfego para esse serviço para o seu computador de desenvolvimento. Essa opção pode ser disruptiva para outros serviços em seu cluster AKS que interagem com o serviço que você está redirecionando pode não funcionar até que você inicie o aplicativo em seu computador de desenvolvimento. A opção *Clone* permite que você escolha um espaço de desenvolvimento infantil existente ou crie um novo espaço de desenvolvimento infantil para redirecionar o tráfego para um pod ou serviço para o seu computador de desenvolvimento. Esta opção permite que você trabalhe isoladamente e não interrompa outros serviços, uma vez que apenas o tráfego para esse espaço de desenvolvimento infantil será redirecionado para o seu computador de desenvolvimento. A opção *Clone* requer que o cluster AKS tenha espaços de desenvolvimento do Azure ativados.

Para este exemplo, escolha *Substituir*.

> [!NOTE]
> Se o pod do seu serviço existente tiver vários contêineres, você também será solicitado a escolher o contêiner do aplicativo.

### <a name="select-a-port-for-your-application"></a>Selecione uma porta para sua aplicação

Depois de selecionar o modo de conexão, você é solicitado a inserir a porta TCP no aplicativo local. Se o aplicativo abrir várias portas, separe-as por uma comuma, por *exemplo, 80,81*. Se o seu aplicativo não aceitar nenhuma solicitação de rede, digite *0*. Para este exemplo, digite *3000*.

![Conectar a porta de escolha](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Confirme se você está conectado

Depois de selecionar a porta TCP do aplicativo, o Azure Dev Spaces estabelecerá uma conexão com o cluster AKS. O Azure Dev Spaces injeta um agente no seu cluster AKS para redirecionar o tráfego entre o cluster AKS e seu computador de desenvolvimento. Estabelecer essa conexão pode levar alguns minutos. O Azure Dev Spaces também solicitará acesso ao administrador para modificar o arquivo *hosts* em seu computador de desenvolvimento.

> [!IMPORTANT]
> Uma vez que o Azure Dev Spaces estabeleça uma conexão com o cluster AKS, os outros serviços em seu cluster AKS podem não funcionar corretamente até que você inicie o serviço em seu computador de desenvolvimento se você escolher o modo *de substituição* de conexão. Você pode escolher o modo de conexão *Clone* em vez de criar um espaço de desenvolvimento infantil para o seu redirecionamento e evitar qualquer interrupção no espaço dos pais. Além disso, se o seu serviço tem uma dependência que não está disponível em seu computador de desenvolvimento, você pode precisar modificar seu aplicativo ou fornecer [configuração adicional](#additional-configuration)

O Azure Dev Spaces abre uma janela de terminal intitulada *AZDS Connect - Bikes* depois de estabelecer uma conexão com o seu cluster AKS. Esta janela de terminal tem todas as variáveis de ambiente e entradas De DNS configuradas a partir do cluster AKS. Qualquer código executado nesta janela de terminal ou usando o depurador Visual Studio Code está conectado ao cluster AKS.

![Terminal](../media/how-to-connect/connect-terminal.png)

Além disso, o Azure Dev Spaces cria uma janela intitulada *Dev Spaces Connect* com toda a sua saída.

![Saída](../media/how-to-connect/connect-output.png)

O Azure Dev Spaces também tem um item de barra de status mostrando o status da conexão.

![Status](../media/how-to-connect/connect-status.png)

Verificar a barra de status mostra *Espaços de dev: Conectado a dev/bikes na porta local 3000*.

### <a name="configure-your-application-on-your-development-computer"></a>Configure seu aplicativo em seu computador de desenvolvimento

Abra a janela de terminal *AZDS Connect - Bikes* e execute: `npm install`

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Clique *em Depurar* e *depois Abrir configurações*. Se solicitado a selecionar um ambiente, escolha *Node.js*. Isso cria `.vscode/launch.json` um arquivo. Substitua o conteúdo desse arquivo pelo seguinte:

```json
{
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

Abra [o package.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) e adicione um script de depuração:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Inicie seu aplicativo em seu computador de desenvolvimento

Clique no ícone *Debug* à esquerda e clique no botão iniciar ao lado *de Iniciar via NPM* na parte superior.

![Lançamento via NPM](../media/how-to-connect/launch-npm.png)

Seu aplicativo será inicialeedo e o Azure Dev Spaces redireciona o tráfego entre seu cluster AKS e seu computador de desenvolvimento. Você verá mensagens semelhantes às abaixo no *Console Debug*:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Navegue até o serviço *de compartilhamento de bicicletas* clicando na barra de status do Azure Dev Spaces e escolhendo a URL pública do seu aplicativo. Você também pode encontrar a `azds list-uris` URL pública a partir do comando que você executou anteriormente. Se você não estiver usando o Azure Dev Spaces em seu cluster, use o IP ou a URL para o aplicativo para o namespace que você está usando. No exemplo acima, a URL pública para o serviço *bikesharingweb* é `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selecione *Aurelia Briggs (cliente)* como usuário e selecione uma bicicleta para alugar.

### <a name="set-a-break-point"></a>Defina um break point

Abra [server.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) e clique em algum lugar na linha 233 para colocar seu cursor lá. Defina um breakpoint ao bater *F9* ou clicando *em Debug* e, em seguida, *Toggle Breakpoint*.

Navegue até o serviço *de compartilhamento de bicicletas,* abrindo a URL pública. Selecione *Aurelia Briggs (cliente)* como usuário e selecione uma bicicleta para alugar. Observe que a imagem da moto não carrega. Destaque para o Visual Studio Code e observe a linha 233. O ponto de partida que você definiu parou o serviço na linha 233. Para retomar o serviço, pressione *F5* ou clique em *Depurar* e, em seguida, *Continuar*. Retorne ao seu navegador e verifique se você vê uma imagem de espaço reservado para a moto.

Remova o ponto de partida colocando o cursor na linha 233 `server.js` e atingindo *F9*.

### <a name="update-your-application"></a>Atualizar o aplicativo

Editar `server.js` para remover as linhas 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A seção agora deve parecer:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salve suas alterações e clique *em Depurar* e, em seguida, *reiniciar a depuração*. Atualize seu navegador e verifique se você não vê mais uma imagem de espaço reservado para a moto.

Clique em *Depurar* e, em seguida, *Parar Depuração* para parar o depurador. Clique na barra de status do Azure Dev Spaces para se desconectar do cluster AKS.

## <a name="additional-configuration"></a>Configuração adicional

O Azure Dev Spaces pode lidar com o tráfego roteamento e replicar variáveis do ambiente sem qualquer configuração adicional. Se você precisar baixar todos os arquivos que estão montados no contêiner em seu cluster AKS, como um arquivo ConfigMap, você pode criar um `azds-local.env` para baixar esses arquivos para o seu computador de desenvolvimento.

Aqui está `azds-local.env`um exemplo:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>Usando registro e diagnóstico

A saída de registro é escrita na janela *Dev Spaces Connect* depois de conectar seu computador de desenvolvimento ao cluster AKS.

![Saída](../media/how-to-connect/connect-output.png)

Clique na barra de status do Azure Dev Spaces e escolha *Mostrar informações de diagnóstico*. Este comando imprime as variáveis de ambiente atuais e inteiros de DNS na saída de registro.

![Saída com diagnóstico](../media/how-to-connect/connect-output-diagnostics.png)

Além disso, você pode encontrar `Azure Dev Spaces` os registros de diagnóstico no diretório no [diretório *TEMP* do][azds-tmp-dir]seu computador de desenvolvimento.

## <a name="next-steps"></a>Próximas etapas

Saiba como usar o Azure Dev Spaces e o GitHub Actions para testar alterações de uma solicitação de tração diretamente no AKS antes que a solicitação de tração seja incorporada ao ramo principal do seu repositório.

> [!div class="nextstepaction"]
> [GitHub Ações & Serviço Azure Kubernetes][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download