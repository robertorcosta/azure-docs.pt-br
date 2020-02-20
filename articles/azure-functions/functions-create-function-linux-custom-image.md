---
title: Crie Azure Functions no Linux usando uma imagem personalizada
description: Saiba como criar Azure Functions em execução em uma imagem personalizada do Linux.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions01
ms.openlocfilehash: 9c97606b21a6e98494fffb689567aaab6e2f0621
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210184"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Criar uma função no Linux usando um contêiner personalizado

Neste tutorial, você cria e implanta código em Python no Azure Functions como um contêiner do Docker personalizado usando uma imagem base do Linux. Normalmente, você usa uma imagem personalizada quando suas funções exigem uma versão de linguagem específica ou têm uma configuração ou dependência específica que não é fornecida pela imagem interna.

Você também pode usar um contêiner padrão do Serviço de Aplicativo do Azure, conforme descrito em [Criar sua primeira função hospedada em Linux](functions-create-first-azure-function-azure-cli-linux.md). Imagens base com suporte para Azure Functions encontram-se no [repositório de imagens de base do Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo de funções e um Dockerfile usando o Azure Functions Core Tools.
> * Compile uma imagem personalizada usando o Docker.
> * Publique uma imagem personalizada em um registro de contêiner.
> * Criar recursos de suporte no Azure para o aplicativo de funções
> * Implante um aplicativo de funções do Hub do Docker.
> * Adicione configurações de aplicativo ao aplicativo de funções.
> * Habilitar a implantação contínua.
> * Habilitar conexões SSH para o contêiner.
> * Adicionar uma associação de saída de armazenamento de filas. 

Você pode seguir este tutorial em qualquer computador que execute o Windows, o Mac OS ou o Linux. A realização do tutorial gerará custos de alguns dólares americanos em sua conta do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- O [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.7.1846 ou posterior
- A [CLI do Azure](/cli/azure/install-azure-cli) versão 2.0.77 ou posterior
- O [runtime do Azure Functions 2.x](functions-versions.md)
- Os seguintes componentes de runtime de linguagem:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x ou posterior](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 de 64 bits](https://www.python.org/downloads/release/python-3610/) ou [Python 3.7 de 64 bits](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- Uma [ID do Docker](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

1. Em um terminal ou uma janela Comando, execute `func --version` para verificar se a versão do Azure Functions Core Tools é 2.7.1846 ou posterior.
1. Execute `az --version` para verificar se a versão da CLI do Azure é a 2.0.76 ou posterior.
1. Execute `az login` para entrar no Azure e verifique se a assinatura está ativa.
1. Execute `docker login` para entrar no Docker. Esse comando falhará se o Docker não estiver em execução. Nesse caso, inicie o Docker e tente o comando novamente.

## <a name="create-and-test-the-local-functions-project"></a>Criar e testar o projeto de funções local

1. Em um terminal ou prompt de comando, crie uma pasta para este tutorial em uma localização adequado e, em seguida, navegue até ela.

1. Siga as instruções em [Criar e ativar um ambiente virtual](functions-create-first-function-python.md#create-and-activate-a-virtual-environment) para criar um ambiente virtual para uso neste tutorial.

1. Execute o comando a seguir referente à linguagem escolhida para criar um projeto de aplicativo de funções em uma pasta chamada `LocalFunctionsProject`. A opção `--docker` gera um `Dockerfile` para o projeto, que define um contêiner personalizado adequado para uso com o Azure Functions e com o runtime selecionado.

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. Navegue até a pasta do projeto:

    ```
    cd LocalFunctionsProject
    ```
    
1. Adicione uma função ao projeto usando o comando a seguir, em que o argumento `--name` é o nome exclusivo da função e o argumento `--template` especifica o gatilho da função. `func new` cria uma subpasta correspondente ao nome da função que contém um arquivo de código apropriado para a linguagem escolhida do projeto, bem como um arquivo de configuração chamado *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Para testar a função localmente, inicie o host de runtime local do Azure Functions na pasta *LocalFunctionsProject*:
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. Quando vir o ponto de extremidade `HttpExample` na saída, navegue até `http://localhost:7071/api/HttpExample?name=Functions`. O navegador deve exibir uma mensagem semelhante a "Olá, Functions" (que varia ligeiramente dependendo da linguagem de programação escolhida).

1. Use **CTRL**-**C** para interromper o host.

## <a name="build-the-container-image-and-test-locally"></a>Compilar a imagem de contêiner e testá-la localmente

1. (Opcional) Examine o "Dockerfile" na pasta *LocalFunctionsProj*. O Dockerfile descreve o ambiente necessário para executar o aplicativo de funções no Linux: 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > A lista completa de imagens base com suporte para Azure Functions encontram-se na [página de imagens de base do Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. Na pasta *LocalFunctionsProject*, execute o comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e forneça um nome, `azurefunctionsimage`, e uma marca, `v1.0.0`. Substitua `<docker_id>` pela ID da conta do Hub do Docker. Esse comando compila a imagem do Docker para o contêiner.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Quando o comando for concluído, você poderá executar o novo contêiner localmente.
    
1. Para testar o build, execute a imagem em um contêiner local usando o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/), substituindo novamente `<docker_id>` pela ID do Docker e adicionando o argumento de portas, `-p 8080:80`:

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Quando a imagem estiver em execução em um contêiner local, abra um navegador para `http://localhost:8080`, o que deve exibir a imagem de espaço reservado mostrada abaixo. A imagem aparece neste ponto porque a função está sendo executada no contêiner local, como aconteceria no Azure, o que significa que ela está protegida por uma chave de acesso, conforme definido em *function.json* com a propriedade `"authLevel": "function"`. No entanto, o contêiner ainda não foi publicado em um aplicativo de funções no Azure, de modo que a chave ainda não está disponível. Se quiser testar localmente, pare o Docker, altere a propriedade de autorização para `"authLevel": "anonymous"`, recompile a imagem e reinicie o Docker. Em seguida, redefina `"authLevel": "function"` em *function.json*. Para obter mais informações, confira [chaves de autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

    ![Imagem de espaço reservado indicando que o contêiner está sendo executado localmente](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Depois de verificar o aplicativo de funções no contêiner, interrompa o Docker pressionando **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Enviar a imagem por push para o Docker Hub

Um Docker Hub é um Registro de Contêiner que hospeda imagens e fornece serviços de contêiner e imagem. Para compartilhar a imagem, que inclui sua implantação no Azure, você precisa enviá-la por push para um Registro.

1. Se ainda não tiver entrado no Docker, faça isso usando o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/), substituindo `<docker_id>` pela ID do Docker. Esse comando solicita seu nome de usuário e sua senha. A mensagem “Logon com Êxito” confirma que você está conectado.

    ```
    docker login
    ```
    
1. Depois de conectado, envie a imagem por push para o Docker Hub usando o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/), mais uma vez substituindo `<docker_id>` pela ID do Docker.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Dependendo da velocidade da rede, o primeiro envio da imagem por push pode levar alguns minutos (o envio de alterações posteriores é muito mais rápido). Enquanto estiver aguardando, você pode prosseguir para a próxima seção e criar recursos do Azure em outro terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Criar recursos de suporte do Azure para a função

Para implantar o código da função no Azure, você precisa criar três recursos:

- Um grupo de recursos, que é um contêiner lógico para recursos relacionados.
- Uma conta de Armazenamento do Azure, que mantém o estado e outras informações sobre seus projetos.
- Um aplicativo de funções do Azure, que fornece o ambiente para execução do código de função. Um aplicativo de funções é mapeado para seu projeto de função local e permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos.

Use comandos da CLI do Azure para criar esses itens. Cada comando fornece uma saída em JSON após a conclusão.

1. Entre no Azure usando o comando [az login](/cli/azure/reference-index#az-login):

    ```azurecli
    az login
    ```
    
1. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos chamado `AzureFunctionsContainers-rg` na região `westeurope`. (De modo geral, você cria o grupo de recursos e os recursos em uma região próxima a você usando uma região disponível com o comando `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Você não pode hospedar aplicativos Windows e Linux no mesmo grupo de recursos. Se você tiver um grupo de recursos chamado `AzureFunctionsContainers-rg` com um aplicativo de funções ou um aplicativo Web do Windows, você precisará usar um grupo de recursos diferente.
    
1. Crie uma conta de armazenamento para uso geral em seu grupo de recursos e região usando o comando [az storage account create](/cli/azure/storage/account#az-storage-account-create). No exemplo a seguir, substitua `<storage_name>` por um nome exclusivo globalmente que for adequado para você. Os nomes devem conter de 3 a 24 caracteres e podem conter somente números e letras minúsculas. `Standard_LRS` especifica uma conta para uso geral típica.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Para este tutorial, a conta de armazenamento gera um custo de apenas alguns centavos de dólar.
    
1. Use o comando para criar um plano Premium do Azure Functions chamado `myPremiumPlan`, com o tipo de preço **Elástico Premium 1** (`--sku EP1`), na região Europa Ocidental (`-location westeurope`, ou use uma região adequada próxima a você) e em um contêiner Linux (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Há suporte para a hospedagem no Linux de contêineres de funções personalizadas nos [Planos Dedicados (Serviço de Aplicativo)](functions-scale.md#app-service-plan) e [Planos Premium](functions-premium-plan.md#features). Aqui, usamos o plano Premium, que pode ser dimensionado conforme necessário. Para saber mais sobre hospedagem, confira [Comparação de planos de hospedagem do Azure Functions](functions-scale.md). Para calcular os custos, confira a [Página de preços do Functions](https://azure.microsoft.com/pricing/details/functions/).

    O comando também provisiona uma instância associada do Azure Application Insights no mesmo grupo de recursos, com a qual você pode monitorar seu aplicativo de funções e exibir logs. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md). A instância não gera nenhum custo até você ativá-la.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Criar e configurar um aplicativo de funções no Azure usando a imagem

Um aplicativo de funções no Azure gerencia a execução das funções em seu plano de hospedagem. Nesta seção, você usa os recursos do Azure da seção anterior para criar um aplicativo de funções baseado em uma imagem no Docker Hub e configurá-lo com uma cadeia de conexão para o Armazenamento do Azure.

1. Crie o aplicativo do Functions usando o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). No exemplo a seguir, substitua `<storage_name>` pelo nome usado na seção anterior para a conta de armazenamento. Além disso, substitua `<app_name>` por um nome exclusivo globalmente que for adequado para você e `<docker_id>` pela ID do Docker.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    O parâmetro *deployment-container-image-name* especifica a imagem a ser usada para o aplicativo de funções. Você pode usar o comando [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) para exibir informações sobre a imagem usada para a implantação. Você também pode usar o comando [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) para implantar de uma imagem diferente.

1. Recupere a cadeia de conexão para a conta de armazenamento que você criou usando o comando [az storage account show-connection-string](/cli/azure/storage/account), atribuindo-a a uma variável de shell `storageConnectionString`:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    
1. Add this setting to the function app by using the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command. In the following command, replace `<app_name>` with the name of your function app, and replace `<connection_string>` with the connection string from the previous step (a long encoded string that begins with "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. Agora, a função pode usar essa cadeia de conexão para acessar a conta de armazenamento.

> [!TIP]
> No Bash, você pode usar uma variável de shell para capturar a cadeia de conexão em vez de usar a área de transferência. Primeiro, use o seguinte comando para criar uma variável com a cadeia de conexão:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Em seguida, faça referência à variável no segundo comando:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Se publicar a imagem personalizada em uma conta de contêiner particular, você deverá usar as variáveis de ambiente no Dockerfile para a cadeia de conexão. Para obter mais informações, confira a [Instrução ENV](https://docs.docker.com/engine/reference/builder/#env). Você também precisa definir as variáveis `DOCKER_REGISTRY_SERVER_USERNAME` e `DOCKER_REGISTRY_SERVER_PASSWORD`. Em seguida, para usar os valores, você deve recompilar a imagem, enviá-la por push para o Registro e, então, reiniciar o aplicativo de funções no Azure.

## <a name="verify-your-functions-on-azure"></a>Verificar as funções no Azure

Com a imagem implantada no aplicativo de funções no Azure, você pode invocar a função usando solicitações HTTP. Como a definição de *function.json* inclui a propriedade `"authLevel": "function"`, primeiro você precisa obter a chave de acesso (também chamada de "chave de função") e incluí-la como um parâmetro de URL em qualquer solicitação para o ponto de extremidade.

1. Recupere a URL da função com a chave de acesso (função) usando o portal do Azure ou a CLI do Azure, com o comando `az rest`.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Entre no portal do Azure e, em seguida, localize o aplicativo de funções inserindo o nome dele na caixa **Pesquisar** na parte superior da página. Nos resultados, selecione o recurso **Serviço de Aplicativo**.

    1. No painel de navegação à esquerda, em **Funções (Somente Leitura)** , selecione o nome da função.

    1. No painel de detalhes, selecione **</> Obter URL da função**:
    
        ![O comando Obter URL da função no portal do Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. No pop-up, selecione **Padrão (chave de função)** e, em seguida, **Copiar**. A chave é a cadeia de caracteres que segue `?code=`.

        ![Copiando a URL da função do Portal do Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Já que seu aplicativo de funções é implantado como um contêiner, você não pode fazer alterações no seu código de função no portal. Em vez disso, você precisa atualizar o projeto na imagem local, enviar a imagem por push para o Registro novamente e reimplantar no Azure. Você pode configurar a implantação contínua em uma seção posterior.
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

    1. Construa uma cadeia de caracteres de URL no seguinte formato, substituindo `<subscription_id>`, `<resource_group>` e `<app_name>` pela ID de assinatura do Azure, pelo grupo de recursos do aplicativo de funções e pelo nome do aplicativo de funções, respectivamente:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Por exemplo, a URL pode se parecer com o seguinte endereço:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Para fins de conveniência, você pode atribuir a URL a uma variável de ambiente e usá-la no comando `az rest`.
    
    1. Execute o seguinte comando `az rest` (disponível na CLI do Azure versão 2.0.77 e posterior), substituindo `<uri>` pela cadeia de caracteres do URI da última etapa, incluindo as aspas:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. A saída do comando é a chave de função. A URL da função completa, então, é `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, substituindo `<app_name>`, `<function_name>` e `<key>` pelos valores específicos.
    
        > [!NOTE]
        > A chave recuperada aqui é a chave de *host*, que funciona para todas as funções no aplicativo de funções. O método mostrado para o portal recupera somente a chave da única função.

    ---

1. Cole a URL da função na barra de endereços do navegador, adicionando o parâmetro `&name=Azure` ao final da URL. Um texto semelhante a"Olá Azure" deve aparecer no navegador.

    ![Resposta da função no navegador.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Para testar a autorização, remova o parâmetro code= da URL e verifique se você não recebe nenhuma resposta da função.


## <a name="enable-continuous-deployment-to-azure"></a>Habilitar a implantação contínua no Azure

Você pode habilitar o Azure Functions para atualizar automaticamente sua implantação de uma imagem sempre que você atualizar a imagem no Registro.

1. Habilite a implantação contínua usando o comando [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config), substituindo `<app_name>` pelo nome do aplicativo de funções:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Esse comando habilita a implantação contínua e retorna a URL do webhook de implantação. (Você pode recuperar essa URL a qualquer momento usando o comando [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url).)

1. Copie a URL do webhook de implantação para a área de transferência.

1. Abra o [Docker Hub](https://hub.docker.com/), entre e selecione **Repositórios** na barra de navegação. Localize e selecione a imagem, selecione a guia **Webhooks**, especifique um **Nome do webhook**, cole a URL em **URL do Webhook** e, em seguida, selecione **Criar**:

    ![Adicione o webhook em seu repositório DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Com o webhook definido, o Azure Functions reimplanta sua imagem sempre que você a atualiza no Docker Hub.

## <a name="enable-ssh-connections"></a>Habilitar conexões SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Com o SSH habilitado, você pode se conectar ao contêiner usando as Ferramentas Avançadas do Serviço de Aplicativo (Kudu). Para facilitar a conexão com o contêiner usando SSH, o Azure Functions fornece uma imagem base que já tem o SSH habilitado. Você só precisa editar o Dockerfile e, em seguida, recompilar e reimplantar a imagem. Em seguida, você pode se conectar ao contêiner usando as Ferramentas Avançadas (Kudu)

1. No Dockerfile, acrescente a cadeia de caracteres `-appservice` à imagem base na instrução `FROM`:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    As diferenças entre as imagens base são descritas em [Serviços de Aplicativos – Tutorial de imagens personalizadas do Docker](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Recompile a imagem usando o comando `docker build` novamente, substituindo `<docker_id>` pela ID do Docker:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Envie por push a imagem atualizada para o Docker Hub, o que deve levar bem menos tempo do que o primeiro push, pois somente os segmentos atualizados da imagem precisam ser carregados.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. O Azure Functions reimplanta automaticamente a imagem em seu aplicativo de funções. O processo leva menos de um minuto.

1. Em um navegador, abra `https://<app_name>.scm.azurewebsites.net/`, substituindo `<app_name>` por seu nome exclusivo. Essa URL é o ponto de extremidade das Ferramentas Avançadas (Kudu) para seu contêiner do aplicativo de funções.

1. Entre em sua conta do Azure e selecione **SSH** para estabelecer uma conexão com o contêiner. A conexão poderá levar algum tempo se o Azure ainda estiver no processo de atualizar a imagem de contêiner.

1. Depois que a conexão com o contêiner for estabelecida, execute o comando `top` para exibir os processos em execução. 

    ![Comando top do Linux em execução em uma sessão de SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Gravar em uma fila do Armazenamento do Azure

O Azure Functions permite conectar suas funções a outros serviços e recursos do Azure sem precisar escrever um código de integração. Essas *associações*, que representam a entrada e a saída, são declaradas na definição de função. Dados de associações são fornecidos à função como parâmetros. Um *gatilho* é um tipo especial de associação de entrada. Embora uma função tenha apenas um gatilho, ela pode ter várias associações de entrada e de saída. Para saber mais, confira [Conceitos de gatilhos e de associações do Azure Functions](functions-triggers-bindings.md).

Esta seção mostra como integrar sua função a uma fila do Armazenamento do Azure. A associação de saída que você adiciona a essa função escreve dados de uma solicitação HTTP em uma mensagem na fila.

## <a name="retrieve-the-azure-storage-connection-string"></a>Recuperar a cadeia de conexão do Armazenamento do Azure

Anteriormente, você criou uma conta de Armazenamento do Azure para uso pelo aplicativo de funções. A cadeia de conexão dessa conta é armazenada com segurança nas configurações do aplicativo no Azure. Ao baixar a configuração para o arquivo *local.settings.json*, use essa conexão para gravar em uma fila do Armazenamento na mesma conta ao executar a função localmente. 

1. Na raiz do projeto, execute o comando a seguir, substituindo `<app_name>` pelo nome do aplicativo de funções do início rápido anterior. Esse comando substituirá todos os valores existentes no arquivo.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Abra *local.settings.json* e localize o valor chamado `AzureWebJobsStorage`, que é a cadeia de conexão da conta de armazenamento. Você usará o nome `AzureWebJobsStorage` e a cadeia de conexão em outras seções deste artigo.

> [!IMPORTANT]
> Como *local.settings.json* contém segredos baixados do Azure, sempre exclua esse arquivo do controle do código-fonte. O arquivo *.gitignore* criado com um projeto de funções locais exclui o arquivo por padrão.

### <a name="add-an-output-binding-to-functionjson"></a>Adicionar uma associação de saída a function.json

No Azure Functions, cada tipo de associação requer que uma `direction`, um `type` e um `name` exclusivo sejam definidos no arquivo *functions.json*. Seu *function.json* já inclui uma associação de entrada para o tipo "httpTrigger" e uma associação de saída para a resposta HTTP. Para adicionar uma associação a uma fila de armazenamento, modifique o arquivo da maneira a seguir. Fazer isso adiciona uma associação de saída para o tipo "queue", em que a fila aparece no código como um argumento de entrada chamado `msg`. A associação de fila também requer o nome da fila a ser usada, nesse caso `outqueue`, e o nome das configurações que contêm a cadeia de conexão, nesse caso `AzureWebJobStorage`.

::: zone pivot="programming-language-csharp"

Em um projeto de biblioteca de classes C#, as associações são definidas como atributos de associação no método de função. O arquivo *function.json* é, então, gerado automaticamente com base nesses atributos.

1. Para a associação de fila, execute o comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) a seguir para adicionar o pacote de extensão de Armazenamento ao seu projeto.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Abra o arquivo *HttpTrigger.cs* e adicione a seguinte instrução `using`:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Adicione o seguinte parâmetro à definição do método `Run`:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    A definição do método `Run` agora deverá ser correspondente ao seguinte código:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

O parâmetro `msg` é um tipo `ICollector<T>`, que representa uma coleção de mensagens que são gravadas em uma associação de saída quando a função é concluída. Nesse caso, a saída é uma fila de armazenamento denominada `outqueue`. A cadeia de conexão para a Conta de armazenamento é definida pelo `StorageAccountAttribute`. Esse atributo indica a configuração que contém a cadeia de conexão da Conta de armazenamento e pode ser aplicada no nível de classe, método ou parâmetro. Nesse caso, você poderia omitir `StorageAccountAttribute` porque já está usando a conta de armazenamento padrão.

::: zone-end

::: zone pivot="programming-language-javascript"

Atualize *function.json* para que corresponda ao seguinte, adicionando a associação de fila após a associação HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-powershell"

Atualize *function.json* para que corresponda ao seguinte, adicionando a associação de fila após a associação HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-python"

Atualize *function.json* para que corresponda ao seguinte, adicionando a associação de fila após a associação HTTP:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-typescript"

Atualize *function.json* para que corresponda ao seguinte, adicionando a associação de fila após a associação HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>Adicionar código para usar a associação de saída

Após a associação ser definida, seu nome, nesse caso `msg`, aparece no código da função como um argumento (ou no objeto `context` em JavaScript e TypeScript). Em seguida, você pode usar essa variável para gravar mensagens na fila. Você precisa escrever código para autenticação, para obter uma referência de fila ou para gravar dados. Todas essas tarefas de integração são convenientemente processadas no Azure Functions Runtime e na associação de saída da fila.

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>Atualizar a imagem no Registro

1. Na pasta raiz, execute `docker build` novamente e, desta vez, atualize a versão na marca para `v1.0.1`. Assim como antes, substitua `<docker_id>` pela ID da conta do Docker Hub:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Envie a imagem atualizada por push de volta para o repositório usando `docker push`:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Como você configurou a entrega contínua, atualizar a imagem no Registro novamente atualiza automaticamente seu aplicativo de funções no Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Exibir a mensagem na fila do Armazenamento do Azure

Em um navegador, use a mesma URL que antes para invocar a função. O navegador deve exibir a mesma resposta que antes, porque você não modificou essa parte do código da função. O código adicionado, no entanto, gravou uma mensagem usando o parâmetro de URL `name` na fila de armazenamento `outqueue`.

Veja a fila no [portal do Azure](../storage/queues/storage-quickstart-queues-portal.md) ou no [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/). Veja também a fila na CLI do Azure, conforme descrito nas seguintes etapas:

1. Abra o arquivo *local.setting.json* do projeto de funções e copie o valor da cadeia de conexão. Em um terminal ou uma janela Comando, execute o comando a seguir para criar uma variável de ambiente chamada `AZURE_STORAGE_CONNECTION_STRING`, colando a cadeia de conexão específica no lugar de `<connection_string>`. (Essa variável de ambiente significa que você não precisa fornecer a cadeia de conexão para cada comando posterior usando o argumento `--connection-string`.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Opcional) Use o comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) para ver as filas de armazenamento em sua conta. A saída desse comando deve incluir uma fila chamada `outqueue`, que foi criada quando a função gravou a primeira mensagem nessa fila.
    
    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Use o comando [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) para exibir as mensagens nessa fila, que deve ser o primeiro nome usado ao testar a função anteriormente. O comando recupera a primeira mensagem na fila na [codificação de Base64](functions-bindings-storage-queue.md#encoding), de modo que você também precisará decodificar a mensagem para exibi-la como texto.

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Como você precisa desreferenciar a coleção de mensagens e decodificar da Base64, execute o PowerShell e use o comando do PowerShell.

    ---

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser continuar trabalhando com o Azure Functions usando os recursos criados neste tutorial, mantenha todos esses recursos. Como criou um Plano Premium para o Azure Functions, você terá um custo contínuo de USD 1 ou 2 por dia.

Para evitar os custos contínuos, exclua o grupo de recursos `AzureFunctionsContainer-rg` para limpar todos os recursos que estão nele: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Próximas etapas

+ [Funções de monitoramento](functions-monitoring.md)
+ [Opções de escala e de hospedagem](functions-scale.md)
+ [Hospedagem sem servidor baseada em Kubernetes](functions-kubernetes-keda.md)
