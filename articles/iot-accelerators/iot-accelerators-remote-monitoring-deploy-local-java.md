---
title: Implantar solução de monitoramento remoto localmente - IntelliJ IDE - Azure | Microsoft Docs
description: Este guia de como fazer mostra como implantar o acelerador de soluções de monitoramento remoto em sua máquina local usando o IntelliJ para testes e desenvolvimento.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888819"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Implantar o acelerador de solução de Monitoramento Remoto localmente – IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Este artigo mostra como implantar o acelerador de solução de Monitoramento Remoto no computador local para teste e desenvolvimento. Você aprenderá a executar os microsserviços na IntelliJ. Uma implantação local de microserviços usará os seguintes serviços de nuvem: IoT Hub, Azure Cosmos DB, Azure Streaming Analytics e Azure Time Series Insights.

Se você quiser executar o acelerador de solução de Monitoramento Remoto no Docker em seu computador local, confira [Implantar o acelerador de solução de Monitoramento Remoto localmente – Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Pré-requisitos

Para implantar os serviços do Azure usados pelo acelerador de solução de Monitoramento Remoto, você precisará de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Azure Free Trial](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Configuração do computador

Para concluir a implantação local, você precisa ter as seguintes ferramentas instaladas no computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [Plugin IntelliJ Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [Plugin IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Plugin executor intelliJ SBT](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 é um pré-requisito para o PCS CLI que os scripts usam para criar recursos do Azure. Não use o Node.js v10.

> [!NOTE]
> O IntelliJ IDE está disponível para Windows e Mac.

## <a name="download-the-source-code"></a>Fazer o download do código-fonte

Os repositórios de código-fonte de Monitoramento Remoto incluem o código-fonte e os arquivos de configuração do Docker necessários para executar as imagens do Docker dos microsserviços.

Para clonar e criar uma versão local do repositório, use seu ambiente de linha de comando para ir a uma pasta adequada em sua máquina local. Em seguida, execute um dos seguintes conjuntos de comandos para clonar o repositório Java:

* Para baixar a versão mais recente das implementações do microserviço Java, execute o seguinte comando:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Para recuperar os submódulos mais recentes, execute os seguintes comandos:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Esses comandos baixam o código-fonte para todos os microsserviços, bem como os scripts usados para executar os microsserviços localmente. Você não precisa do código fonte para executar os microsserviços em Docker. Mas o código-fonte é útil se você mais tarde planeja modificar o acelerador da solução e testar suas alterações localmente.

## <a name="deploy-the-azure-services"></a>Implantar os serviços do Azure

Embora este artigo mostre como executar os microsserviços localmente, eles dependem de serviços do Azure em execução na nuvem. Use o seguinte script a seguir para implantar os serviços do Azure. Os exemplos de script supõem que você está usando o repositório Java em uma máquina Windows. Se estiver trabalhando em outro ambiente, ajuste os caminhos, extensões de arquivo e separadores de caminho adequadamente.

### <a name="create-new-azure-resources"></a>Criar novos recursos do Azure

Se você ainda não criou os recursos do Azure necessários, siga estas etapas:

1. No ambiente de linha de comando, vá para a pasta **\services\scripts\local\launch** na cópia clonada do repositório.

1. Execute os seguintes comandos para instalar as ferramentas da CLI **pcs** e entrar em sua conta do Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Execute o script **start.cmd**. O script solicitará as seguintes informações:

   * Um nome de solução.
   * A assinatura do Azure a utilizar.
   * O local do datacenter do Azure para usar.

   O script cria um grupo de recursos no Azure que tem o nome da sua solução. Este grupo de recursos contém os recursos do Azure que o acelerador de soluções usa. Você pode excluir esse grupo de recursos depois de não precisar mais dos recursos correspondentes.

   O script também adiciona um conjunto de variáveis de ambiente à sua máquina local. Cada nome de variável tem o prefixo **PCS**. Essas variáveis de ambiente fornecem detalhes que permitem que o Monitoramento Remoto leia seus valores de configuração a partir de um recurso do Azure Key Vault.

   > [!TIP]
   > Quando o script termina, ele salva as variáveis de ambiente para um arquivo chamado ** \<home folder\>\\.pcs\\\<solution name\>.env**. Você pode usá-los para futuras implantações de acelerador de soluções. Observe que todas as variáveis de ambiente definidas na máquina local sobrepõem os valores nos **scripts de\\serviços\\local\\.env** file quando você **executa docker-compor**.

1. Feche seu ambiente de linha de comando.

### <a name="use-existing-azure-resources"></a>Usar recursos existentes do Azure

Se você já criou os recursos azure necessários, defina as variáveis de ambiente correspondentes em sua máquina local:
* **PCS_KEYVAULT_NAME:** O nome do recurso Key Vault.
* **PCS_AAD_APPID**: ID do aplicativo Azure Active Directory (Azure AD).
* **PCS_AAD_APPSECRET**: O segredo do aplicativo Azure AD.

Os valores de configuração serão lidos a partir deste recurso Key Vault. Essas variáveis de ambiente podem ser salvas no ** \<\>\\\>arquivo de solução .pcs\\\<.env** da implantação. Observe que quaisquer variáveis de ambiente definidas em seu computador local substituem os valores no arquivo **serviços\\scripts\\local\\.env** ao executar **docker-compose**.

Parte da configuração necessária pelo microserviço é armazenada em uma instância do Key Vault que foi criada na implantação inicial. As variáveis correspondentes no cofre da chave devem ser modificadas conforme necessário.

## <a name="run-the-microservices"></a>Executar os microsserviços

Nesta seção, você executa os microsserviços do Monitoramento Remoto. Você pode executar:

* A web UI nativamente.
* Os serviços de Simulação de Dispositivos IoT do Azure, Auth e Azure Stream Analytics Manager no Docker.
* Os microsserviços da IntelliJ.

### <a name="run-the-device-simulation-service"></a>Execute o serviço de simulação de dispositivos

Abra uma nova janela de comando prompt. Verifique se você tem acesso às variáveis de ambiente definidas pelo script **start.cmd** na seção anterior.

Execute o seguinte comando para abrir o contêiner Docker para o serviço de simulação de dispositivo. O serviço simula dispositivos para a solução de Monitoramento Remoto.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Executar o serviço de Autenticação

Abra uma nova janela prompt de comando e execute o seguinte comando para abrir o contêiner Docker para o serviço Auth. Ao usar este serviço, você pode gerenciar os usuários que estão autorizados a acessar soluções Azure IoT.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Execute o serviço stream analytics manager

Abra uma nova janela prompt de comando e execute o seguinte comando para abrir o contêiner Docker para o serviço Stream Analytics Manager. Com este serviço, você pode gerenciar os trabalhos do Stream Analytics. Esse gerenciamento inclui a configuração do trabalho e a configuração do trabalho e o início, a parada e o monitoramento do status do trabalho.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Implante todos os outros microsserviços em sua máquina local

As etapas a seguir mostram como executar os microsserviços de Monitoramento Remoto na IntelliJ.

#### <a name="import-a-project"></a>Importar um projeto

1. Abra o IDE Da IntelliJ.
1. Selecione **Projeto de Importação**.
1. Escolha **azure-iot-pcs-remote-monitoring-java\services\build.sbt**.

#### <a name="create-run-configurations"></a>Criar configurações de execução

1. Selecione **Executar** > **configurações de edição**.
1. Selecione **Adicionar nova configuração** > **de tarefa sbt**.
1. Digite **Nome**e, em seguida, insira **Tarefas** como **executado**.
1. Selecione o **Diretório de trabalho** com base no serviço que deseja executar.
1. Selecione **Aplicar** > **OK** para salvar suas escolhas.
1. Criar configurações de execução para os seguintes serviços web:
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

Como exemplo, a seguinte imagem mostra como adicionar uma configuração para um serviço:

[![Captura de tela da janela Configurações de execução/depuração do IDE Da IntelliJ, mostrando a opção de armazenamentoAdaptado destacado na lista de tarefas do SBT no painel esquerdo e entradas nas caixas de parâmetros Nome, Tarefas, Trabalho e VM no painel direito.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Criar uma configuração composta

1. Para executar todos os serviços juntos, selecione Adicionar novo**composto de** **configuração** > .
1. Digite **Nome**e selecione **adicionar tarefas do SBT**.
1. Selecione **Aplicar** > **OK** para salvar suas escolhas.

Como exemplo, a imagem a seguir mostra como adicionar todas as tarefas do SBT a uma única configuração:

[![Captura de tela da janela Configurações de execução/depuração da IntelliJ IDE, mostrando a opção AllServices destacada na lista Composto no painel esquerdo e na opção 'dispositivoTelemetria' da tarefa do SBT destacada no painel direito.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Selecione **Executar** para construir e executar os serviços web na máquina local.

Cada serviço web abre uma janela de prompt de comando e uma janela do navegador da Web. No prompt de comando, você vê a saída do serviço em execução. A janela do navegador permite monitorar o status. Não feche as janelas ou páginas da Web do Prompt de comando, pois essas ações param o serviço web.

Para acessar o status dos serviços, acesse as seguintes URLs:

* Gerente de IoT-Hub:[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetria do dispositivo:[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Config:[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* adaptador de armazenamento:[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Iniciar o trabalho do Stream Analytics

Execute estas etapas para iniciar o trabalho do Stream Analytics:

1. Vá para o [portal Azure.](https://portal.azure.com)
1. Vá para o **grupo Resource** criado para sua solução. O nome do grupo de recursos é o nome que você escolheu para sua solução quando executou o script **start.cmd.**
1. Selecione o **trabalho do Stream Analytics** na lista de recursos.
1. Na página visão **geral** do trabalho do Stream Analytics, selecione o botão **Iniciar** e, em seguida, **selecione Iniciar** para iniciar o trabalho.

### <a name="run-the-web-ui"></a>Executar a interface do usuário da Web

Nesta etapa, você inicia a interface do usuário da Web. Abra uma nova janela de comando prompt. Verifique se você tem acesso às variáveis de ambiente definidas pelo script **start.cmd.** Vá para a pasta **webui** em sua cópia local do repositório e execute os seguintes comandos:

```cmd
npm install
npm start
```

Quando o comando **iniciar** é concluído, seu [http://localhost:3000/dashboard](http://localhost:3000/dashboard)navegador exibe a página no endereço . São esperados erros nessa página. Para visualizar o aplicativo sem erros, complete as seguintes etapas.

### <a name="configure-and-run-nginx"></a>Configure e execute o Nginx

Configure um servidor proxy reverso que vincula o aplicativo web aos microserviços em execução na sua máquina local:

1. Copie o arquivo **nginx.conf** da pasta **webui\scripts\localhost** em sua cópia local do repositório para o diretório de instalação **nginx\conf.**
1. Executa Nginx.

Para obter mais informações sobre como executar o Nginx, consulte [nginx para Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Conectar-se ao painel

Para acessar o painel de http://localhost:9000 soluções de monitoramento remoto, acesse no seu navegador.

## <a name="clean-up"></a>Limpar

Para evitar cobranças desnecessárias, remova os serviços de nuvem da sua assinatura do Azure depois de terminar seus testes. Para remover os serviços, acesse o [portal do Azure](https://ms.portal.azure.com)e exclua o grupo de recursos que o script **start.cmd** criou.

Você também pode excluir a cópia local do repositório de monitoramento remoto que foi criado quando você clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou a solução de monitoramento remoto, a próxima etapa é [explorar os recursos do painel de solução](quickstart-remote-monitoring-deploy.md).
