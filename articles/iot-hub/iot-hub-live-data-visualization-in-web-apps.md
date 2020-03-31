---
title: Visualização de dados em tempo real de seus dados de hub de IoT em um aplicativo web
description: Use um aplicativo web para visualizar dados de temperatura e umidade que são coletados de um sensor e enviados para o seu hub Iot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675312"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualize dados de sensores em tempo real do seu hub Azure IoT em um aplicativo web

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Neste tutorial, você aprende a visualizar dados de sensores em tempo real que seu hub de IoT recebe com um aplicativo web node.js em execução em seu computador local. Depois de executar o aplicativo web localmente, você pode seguir opcionalmente passos para hospedar o aplicativo web no Azure App Service. Se você quiser tentar visualizar os dados em seu Hub IoT usando o Power BI, consulte [Usar o Power BI para visualizar dados de sensor em tempo real do Hub IoT do Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>O que fazer

* Adicione um grupo de consumidores ao seu hub de IoT que o aplicativo web usará para ler dados de sensores
* Baixe o código do aplicativo web do GitHub
* Examine o código do aplicativo web
* Configure variáveis de ambiente para segurar os artefatos ioT hub necessários pelo seu aplicativo web
* Execute o aplicativo web em sua máquina de desenvolvimento
* Abra uma página da Web para ver os dados de temperatura e umidade em tempo real do seu hub de IoT
* (Opcional) Use o Azure CLI para hospedar seu aplicativo web no Azure App Service

## <a name="what-you-need"></a>O que você precisa

* Complete o tutorial [do simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com nó.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes abrangem os seguintes requisitos:

  * Uma assinatura ativa do Azure
  * Um Hub IoT em sua assinatura
  * Um aplicativo cliente que envia mensagens para o seu Hub IoT

* [Baixar Git](https://www.git-scm.com/downloads)

* As etapas deste artigo assumem uma máquina de desenvolvimento do Windows; no entanto, você pode facilmente executar essas etapas em um sistema Linux em seu shell preferido.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Execute o comando a seguir para adicionar a Extensão do Microsoft Azure IoT para a CLI do Azure à instância do Cloud Shell. A Extensão de IoT adiciona comandos específicos do Hub IoT, do IoT Edge e do DPS (Serviço de Provisionamento de Dispositivos IoT) à CLI do Azure.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

[Grupos de consumidores](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) fornecem visualizações independentes sobre o fluxo de eventos que permitem que aplicativos e serviços do Azure consumam independentemente dados do mesmo ponto final do Event Hub. Nesta seção, você adiciona um grupo de consumidores ao ponto final incorporado do seu hub de IoT que o aplicativo web usará para ler dados.

Execute o seguinte comando para adicionar um grupo de consumidores ao ponto final incorporado do seu hub IoT:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Anote o nome que você escolher, você vai precisar dele mais tarde neste tutorial.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Obtenha uma seqüência de conexão de serviço para o seu hub de IoT

Os hubs de IoT são criados com várias políticas de acesso padrão. Uma dessas políticas é a política **de serviço,** que fornece permissões suficientes para um serviço ler e escrever os pontos finais do hub de IoT. Execute o seguinte comando para obter uma seqüência de conexões para o seu hub ioT que adere à política de serviço:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A seqüência de conexões deve ser semelhante à seguinte:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Anote a seqüência de conexão de serviço, você vai precisar dela mais tarde neste tutorial.

## <a name="download-the-web-app-from-github"></a>Baixe o aplicativo web do GitHub

Abra uma janela de comando e digite os seguintes comandos para baixar a amostra do GitHub e mudar para o diretório de exemplo:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Examine o código do aplicativo web

A partir do diretório web-apps-node-iot-hub-data-visualization, abra o aplicativo web em seu editor favorito. A seguir, mostra a estrutura do arquivo visualizada no CÓDIGO VS:

![Estrutura de arquivos de aplicativos da Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Tire um momento para examinar os seguintes arquivos:

* **Server.js** é um script do lado do serviço que inicializa o soquete web e a classe de invólucro event Hub. Ele fornece um retorno de chamada para a classe de invólucro do Event Hub que a classe usa para transmitir mensagens recebidas para o soquete da Web.

* **Event-hub-reader.js** é um script do lado do serviço que se conecta ao ponto final incorporado do hub IoT usando a cadeia de conexão especificada e o grupo de consumidores. Ele extrai o DeviceId e o EnqueuedTimeUtc de metadados em mensagens recebidas e, em seguida, retransmite a mensagem usando o método de retorno de chamada registrado pelo server.js.

* **Chart-device-data.js** é um script do lado do cliente que ouve na tomada web, mantém o controle de cada DeviceId e armazena os últimos 50 pontos de dados de entrada para cada dispositivo. Em seguida, ele vincula os dados do dispositivo selecionado saem ao objeto gráfico.

* **Index.html** lida com o layout de IA para a página da Web e faz referência aos scripts necessários para a lógica do lado do cliente.

## <a name="configure-environment-variables-for-the-web-app"></a>Configure variáveis de ambiente para o aplicativo web

Para ler dados do seu hub de IoT, o aplicativo web precisa da seqüência de conexões do seu hub de IoT e do nome do grupo de consumidores que ele deve ler. Ele obtém essas strings do ambiente de processo nas seguintes linhas em server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Defina as variáveis de ambiente na janela de comando com os seguintes comandos. Substitua os valores do espaço reservado pela seqüência de conexão de serviço para o seu hub de IoT e o nome do grupo de consumidores que você criou anteriormente. Não cite as cordas.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Executar o aplicativo Web

1. Certifique-se de que seu dispositivo está sendo executado e enviando dados.

2. Na janela de comando, execute as seguintes linhas para baixar e instalar pacotes referenciados e iniciar o site:

   ```cmd
   npm install
   npm start
   ```

3. Você deve ver a saída no console que indica que o aplicativo web se conectou com sucesso ao seu hub IoT e está ouvindo na porta 3000:

   ![Aplicativo web começou no console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Abra uma página da Web para ver dados do seu hub de IoT

Abra um navegador em `http://localhost:3000`.

Na lista **Selecionar um dispositivo,** selecione seu dispositivo para ver uma parcela em execução dos últimos 50 pontos de dados de temperatura e umidade enviados pelo dispositivo para o seu hub IoT.

![Página de aplicativo Web mostrando a umidade e a temperatura em tempo real](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Você também deve ver a saída no console que mostra as mensagens que seu aplicativo web está transmitindo para o cliente do navegador:  

![Saída de transmissão de aplicativo web no console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hospede o aplicativo web no App Service

O [recurso Web Apps do Azure App Service](https://docs.microsoft.com/azure/app-service/overview) fornece uma plataforma como serviço (PAAS) para hospedagem de aplicativos web. Os aplicativos da Web hospedados no Azure App Service podem se beneficiar de recursos poderosos do Azure, como segurança adicional, balanceamento de carga e escalabilidade, bem como soluções DevOps do Azure e parceiro, como implantação contínua, gerenciamento de pacotes e assim por diante. O Azure App Service suporta aplicativos web desenvolvidos em muitos idiomas populares e implantados na infra-estrutura Windows ou Linux.

Nesta seção, você disponibiliza um aplicativo web no App Service e implanta seu código nele usando comandos Azure CLI. Você pode encontrar detalhes dos comandos usados na documentação do [webapp az.](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) Antes de iniciar, certifique-se de que você completou as etapas para [adicionar um grupo de recursos ao seu hub de IoT,](#add-a-consumer-group-to-your-iot-hub)obtenha [uma seqüência de conexão de serviço para o seu hub de IoT](#get-a-service-connection-string-for-your-iot-hub)e [baixe o aplicativo web do GitHub](#download-the-web-app-from-github).

1. Um [plano de Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) define um conjunto de recursos de computação para um aplicativo hospedado no App Service para ser executado. Neste tutorial, usamos o nível Desenvolvedor/Grátis para hospedar o aplicativo web. Com o nível Free, seu aplicativo web é executado em recursos compartilhados do Windows com outros aplicativos do App Service, incluindo aplicativos de outros clientes. O Azure também oferece planos do App Service para implantar aplicativos web em recursos de computação Linux. Você pode pular esta etapa se você já tiver um plano de Serviço de Aplicativo que você deseja usar.

   Para criar um plano de serviço de aplicativo usando o nível gratuito do Windows, execute o seguinte comando. Use o mesmo grupo de recursos em que seu hub de IoT está. O nome do seu plano de serviço pode conter letras maiúsculas e minúsculas, números e hífens.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Agora provite um aplicativo web em seu plano de Serviço de Aplicativo. O `--deployment-local-git` parâmetro permite que o código do aplicativo web seja carregado e implantado a partir de um repositório Git em sua máquina local. O nome do seu aplicativo web deve ser globalmente único e pode conter letras maiúsculas e minúsculas, números e hífens. Certifique-se de especificar a versão 10.6 ou posterior do Nó para o `--runtime` parâmetro, dependendo da versão do tempo de execução do Node.js que você está usando. Você pode `az webapp list-runtimes` usar o comando para obter uma lista de tempos de execução suportados.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Agora adicione configurações de aplicativos para as variáveis de ambiente que especificam a cadeia de conexão do hub IoT e o grupo de consumidores do hub Event. As configurações individuais são `-settings` delimitadas no parâmetro. Use a seqüência de conexão de serviço para o seu hub de IoT e o grupo de consumidores que você criou anteriormente neste tutorial. Não cite os valores.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Habilite o protocolo Soquetes da Web para o aplicativo web e defina o aplicativo web apenas para receber solicitações HTTPS (as solicitações HTTP são redirecionadas para HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Para implantar o código no App Service, você usará suas [credenciais de implantação em nível de usuário](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Suas credenciais de implantação em nível de usuário são diferentes das credenciais do Azure e são usadas para implantações git locais e FTP em um aplicativo web. Uma vez definido, eles são válidos em todos os seus aplicativos do App Service em todas as assinaturas em sua conta do Azure. Se você já definiu credenciais de implantação em nível de usuário, poderá usá-las.

   Se você não tiver definido anteriormente as credenciais de implantação no nível do usuário ou não puder se lembrar de sua senha, execute o seguinte comando. Seu nome de usuário de implantação deve ser único dentro do Azure, e não deve conter o símbolo '@' para pushes git locais. Quando for solicitado, digite e confirme sua nova senha. A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Faça com que a URL do Git seja usada para empurrar seu código para o Serviço de Aplicativos.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adicione um controle remoto ao seu clone que faz referência ao repositório Git para o aplicativo web no App Service. Para \<url clone\>do Git, use a URL retornada na etapa anterior. Execute o seguinte comando em sua janela de comando.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Para implantar o código no Serviço de Aplicativos, digite o seguinte comando na janela de comando. Se você for solicitado para credenciais, digite as credenciais de implantação de nível de usuário que você criou na etapa 5. Certifique-se de que você empurra para o ramo principal do controle remoto do Serviço de Aplicativo.

    ```cmd
    git push webapp master:master
    ```

9. O progresso da implantação será atualizado em sua janela de comando. Uma implantação bem-sucedida terminará com linhas semelhantes à seguinte saída:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Execute o seguinte comando para consultar o estado do seu aplicativo web e certifique-se de que ele está sendo executado:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Navegue até `https://<your web app name>.azurewebsites.net` em um navegador. Uma página da Web semelhante à que você viu quando executou o aplicativo web é exibida localmente. Supondo que seu dispositivo esteja executando e enviando dados, você deve ver um gráfico em execução das 50 leituras de temperatura e umidade mais recentes enviadas pelo dispositivo.

## <a name="troubleshooting"></a>Solução de problemas

Se você encontrar qualquer problema com esta amostra, tente as etapas nas seguintes seções. Se você ainda tiver problemas, envie-nos feedback no final deste tópico.

### <a name="client-issues"></a>Problemas com clientes

* Se um dispositivo não aparecer na lista ou nenhum gráfico estiver sendo desenhado, certifique-se de que o código do dispositivo está sendo executado no seu dispositivo.

* No navegador, abra as ferramentas do desenvolvedor (em muitos navegadores a chave F12 irá abri-la), e encontre o console. Procure por quaisquer avisos ou erros impressos lá.

* Você pode depurar o script do lado do cliente em /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemas no site local

* Observe a saída na janela onde você lançou nó para saída do console.

* Depurar o código do servidor, especificamente server.js e /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problemas do Azure App Service

* No portal Azure, acesse seu aplicativo web. Em **Monitoramento** no painel esquerdo, selecione **registros de serviço de aplicativo**. Ativar **o registro de aplicativos (sistema de arquivos)** para onda, definir **nível** para erro e, em seguida, selecionar **Salvar**. Em seguida, abra **o log stream** (em **Monitoramento**).

* A partir do seu aplicativo web no portal Azure, em Ferramentas `node -v` `npm -v` **de Desenvolvimento** selecionam **Console** e validam versões de nó e npm com e .

* Se você vir um erro sobre não encontrar um pacote, você pode ter executado os passos fora de ordem. Quando o site é `git push`implantado (com `npm install`) o serviço do aplicativo é executado, que é executado com base na versão atual do nó que ele configurou. Se isso for alterado na configuração mais tarde, você precisará fazer uma alteração sem sentido no código e empurrar novamente.

## <a name="next-steps"></a>Próximas etapas

Você usou com êxito seu aplicativo Web para visualizar dados do sensor em tempo real do seu Hub IoT.

Para outra maneira de visualizar dados do Azure IoT Hub, consulte [Use Power BI para visualizar dados de sensores em tempo real do seu hub IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
