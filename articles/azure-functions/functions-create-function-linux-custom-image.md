---
title: Crie Azure Functions no Linux usando uma imagem personalizada
description: Saiba como criar Azure Functions em execução em uma imagem personalizada do Linux.
author: ggailey777
ms.author: glenga
ms.date: 09/27/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 54d7dc4e57991f6b773169f539a86fdc8451cbba
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950383"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Crie uma função no Linux usando uma imagem personalizada

O Azure Functions permite hospedar suas funções no Linux em seu próprio contêiner personalizado. Você também pode [hospedar em um contêiner padrão do Serviço de Aplicativo do Azure](functions-create-first-azure-function-azure-cli-linux.md). Essa funcionalidade requer [o tempo de execução do Functions 2.x](functions-versions.md).

Neste tutorial, você aprenderá a implantar suas funções no Azure como uma imagem personalizada do Docker. Esse padrão será útil quando você precisar personalizar a imagem de contêiner interna. Convém usar uma imagem personalizada quando suas funções precisarem de uma versão de idioma específico, ou exigirem uma configuração ou dependência específica que não é fornecida na imagem interna. Imagens base com suporte para Azure Functions encontram-se no [repositório de imagens de base do Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base). 

Este tutorial explica como usar o Azure Functions Core Tools para criar uma função em uma imagem personalizada do Linux. Publique essa imagem em um aplicativo de funções no Azure, que foi criado usando a CLI do Azure. Posteriormente, você atualizará a função para se conectar ao armazenamento de Filas do Azure. Você também a habilitará.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie um aplicativo de funções e o Dockerfile usando o Core Tools.
> * Compile uma imagem personalizada usando o Docker.
> * Publique uma imagem personalizada em um registro de contêiner.
> * Criar uma conta do Armazenamento do Azure.
> * Criar um plano de hospedagem Premium.
> * Implante um aplicativo de funções do Hub do Docker.
> * Adicione configurações de aplicativo ao aplicativo de funções.
> * Habilitar a implantação contínua.
> * Habilitar conexões SSH para o contêiner.
> * Adicionar uma associação de saída de armazenamento de filas. 
> * Adicionar o monitoramento do Application Insights.

As etapas a seguir têm suporte em um computador Mac, Windows ou Linux. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, você deve ter o seguinte:

* Instale a [versão do Azure Core Tools 2.x](functions-run-local.md#v2).

* Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem.  
Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/bash).

* Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Criar o projeto local

Execute o comando a seguir na linha de comando para criar um projeto de aplicativo de funções na pasta `MyFunctionProj` do diretório local atual. Para um projeto Python, você [precisa estar executando em um ambiente virtual](functions-create-first-function-python.md#create-and-activate-a-virtual-environment-optional).

```bash
func init MyFunctionProj --docker
```

Quando você inclui a opção `--docker`, um dockerfile é gerada para o projeto. Esse arquivo é usado para criar um contêiner personalizado no qual executar o projeto. A imagem base usada depende da linguagem do tempo de execução do trabalho escolhida.  

Quando solicitado, escolha um tempo de execução do trabalho com as seguintes linguagens:

* `dotnet`: cria um projeto de biblioteca de classes do .NET Core (.csproj).
* `node`: cria um projeto de JavaScript.
* `python`: cria um projeto do Python.  

Use o comando a seguir para navegar até a nova pasta do projeto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Compilar do arquivo do Docker

Examine o _Dockerfile_ na pasta raiz do projeto. Este arquivo descreve o ambiente necessário para executar o aplicativo de funções no Linux. O exemplo a seguir é um Dockerfile que cria um contêiner que executa um aplicativo de funções no tempo de execução do trabalho em JavaScript (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> A lista completa de imagens base com suporte para Azure Functions encontram-se na [página de imagens de base do Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Execute o comando `build`

Na pasta raiz, execute o comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e forneça um nome, `mydockerimage`, e uma marca, `v1.0.0`. Substitua `<docker-id>` pela ID da conta do Hub do Docker. Esse comando compila a imagem do Docker para o contêiner.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Quando o comando for concluído, você poderá executar o novo contêiner localmente.

### <a name="run-the-image-locally"></a>Executar a imagem localmente
Verifique se a imagem criada funciona executando a imagem do Docker em um contêiner local. Execute o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e envie o nome e a marcação da imagem para ele. Lembre-se de especificar a porta usando o argumento `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Com a imagem personalizada em execução em um contêiner local do Docker, verifique se o aplicativo de funções e o contêiner estão funcionando corretamente navegando até <http://localhost:8080>.

![Execute o aplicativo de funções localmente.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> Neste ponto, ao tentar chamar sua função HTTP específica, você obtém uma resposta de erro HTTP 401. Isso ocorre porque sua função é executada no contêiner local como ocorreria no Azure, o que significa que a chave de função é necessária. Como o contêiner ainda não foi publicado em um aplicativo de funções, não há nenhuma chave de função disponível. Você verá posteriormente que, quando usar as Core Tools para publicar seu contêiner, as teclas de função serão mostradas a você. Se quiser testar sua função em execução no contêiner local, você poderá alterar a [chave de autorização](functions-bindings-http-webhook.md#authorization-keys) para `anonymous`. 

Depois de verificar o aplicativo de funções no contêiner, interrompa a execução. Agora, você pode enviar por push a imagem personalizada à sua conta do Hub do Docker.

## <a name="push-to-docker-hub"></a>Enviar por push para o Docker Hub

Um Registro é um aplicativo que hospeda imagens e fornece a imagem de serviços e serviços de contêiner. Para compartilhar sua imagem, você deve enviá-la por push para um registro. O Hub do Docker é um Registro para imagens do Docker que permite hospedar seus próprios repositórios públicos ou particulares.

Antes de enviar uma imagem por push, você deve entrar no Hub do Docker usando o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Substitua `<docker-id>` pelo nome de sua conta e digite sua senha no console do prompt. Para obter outras opções de senha de Hub do Docker, confira a [documentação de comandos de logon do docker](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Uma mensagem “logon com êxito” confirma que você está conectado. Depois de conectado, envie a imagem por push para o Hub do Docker usando o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Após o push ter êxito, você pode usar a imagem como a origem de implantação para um novo aplicativo de funções no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Criar um plano Premium

Hospedagem do Linux para contêineres personalizados do Functions compatíveis em [planos Dedicados (Serviço de Aplicativo)](functions-scale.md#app-service-plan) e [planos Premium](functions-premium-plan.md#features). Este tutorial usa um plano Premium, que pode ser dimensionado conforme necessário. Para saber mais sobre hospedagem, confira [Comparação de planos de hospedagem do Azure Functions](functions-scale.md).

O exemplo a seguir cria um plano Premium chamado `myPremiumPlan` no **tipo de preço Elástico Premium 1** (`--sku EP1`), na região Oeste dos EUA (`-location WestUS`) e em um contêiner do Linux (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Criar um aplicativo com base na imagem

O aplicativo de funções gerencia a execução das funções em seu plano de hospedagem. Crie um aplicativo de funções a partir de uma imagem do Hub do Docker usando o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

No comando a seguir, substitua um nome de aplicativo de funções exclusivo quando você vir o espaço reservado `<app_name>` e o nome da conta de armazenamento por `<storage_name>`. O `<app_name>` é usado como domínio DNS padrão para o aplicativo de funções, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. Como antes, `<docker-id>` é o nome de conta do Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

O parâmetro _deployment-container-image-name_ indica a imagem hospedada no Hub do Docker a ser usada para criar o aplicativo de funções. Use o comando [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) para exibir informações sobre a imagem usada para a implantação. Use o comando [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) para implantar de uma imagem diferente.

## <a name="configure-the-function-app"></a>Configurar o aplicativo de funções

A função precisa da cadeia de conexão para se conectar à conta de armazenamento padrão. Quando você estiver publicando sua imagem personalizada em uma conta de contêiner privado, defina essas configurações de aplicativo como variáveis de ambiente no Dockerfile usando a [instrução ENV](https://docs.docker.com/engine/reference/builder/#env), ou algo semelhante.

Nesse caso, `<storage_name>` é o nome da conta de armazenamento criada. Obtenha a cadeia de conexão com o comando [az storage account show-connection-string](/cli/azure/storage/account). Adicione essas configurações de aplicativo ao aplicativo de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Se o contêiner for privado, você precisaria definir as seguintes configurações de aplicativo também  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Você terá que parar e iniciar o aplicativo de função para que esses valores sejam coletados

## <a name="verify-your-functions"></a>Verificar as funções

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

A função disparada por HTTP que você criou requer uma [chave de função](functions-bindings-http-webhook.md#authorization-keys) ao chamar o ponto de extremidade. Neste momento, a maneira mais fácil de obter a URL da função, incluindo a chave, é do [portal do Azure]. 

> [!TIP]
> Você também pode obter suas chaves de função usando as [APIs de gerenciamento de chaves](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), o que exige que você apresente um [token de portador para autenticação](/cli/azure/account#az-account-get-access-token).

Localize seu novo aplicativo de funções no [portal do Azure] digitando o nome do aplicativo de funções na caixa **Pesquisar** na parte superior da página e selecionando o recurso **Serviço de Aplicativo**.

Selecione a função **MyHttpTrigger**, selecione **</> Obter URL da função** > **padrão (Chave de função)**  > **Copiar**.

![Copiar a URL da função do Portal do Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

Nessa URL, a chave de função é o parâmetro de consulta `code`. 

> [!NOTE]  
> Já que seu aplicativo de funções é implantado como um contêiner, você não pode fazer alterações no seu código de função no portal. Em vez disso, você deve atualizar o projeto no contêiner local e republicá-lo no Azure.

Cole a URL de função na barra de endereços do navegador. Adicione o valor da cadeia de caracteres de consulta `&name=<yourname>` ao final desta URL e pressione `Enter` em seu teclado para executar a solicitação. Você deverá ver a resposta retornada pela função exibida no navegador.

O exemplo a seguir mostra a resposta no navegador:

![Resposta da função no navegador.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

A URL da solicitação inclui uma chave que é necessária, por padrão, para acessar sua função via HTTP. 

## <a name="enable-continuous-deployment"></a>Habilitar a implantação contínua

Um dos benefícios de usar contêineres é o suporte para implantação contínua. O Functions permite que você implante atualizações automaticamente quando o contêiner é atualizado no Registro. Habilitar a implantação contínua com o comando [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config).

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Esse comando retorna a URL de webhook de implantação após a implantação contínua ser habilitada. Você também pode usar o comando [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) para retornar essa URL. 

Copie a URL de implantação e navegue até seu repositório DockerHub, escolha a guia **Webhooks**, digite um **nome do Webhook** para o webhook, cole a URL em **URL do Webhook**e, em seguida, escolha o sinal de adição ( **+** ).

![Adicione o webhook em seu repositório DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Com o conjunto de webhook, todas as atualizações para a imagem vinculada no DockerHub fazem com que o aplicativo de funções baixe e instale a imagem mais recente.

## <a name="enable-ssh-connections"></a>Habilitar conexões SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Com o SSH habilitado, você pode se conectar ao contêiner usando as Ferramentas Avançadas do Serviço de Aplicativo (Kudu). Para facilitar a conexão com o contêiner usando SSH, o Functions fornece uma imagem base que já tem o SSH habilitado. 

### <a name="change-the-base-image"></a>Alterar a imagem base

Em seu Dockerfile, acrescente a cadeia de caracteres `-appservice` à imagem base na instrução `FROM`, que para um projeto JavaScript é semelhante à mostrada a seguir.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

As diferenças nas duas imagens base habilitam conexões SSH em seu contêiner. Essas diferenças são detalhadas [neste tutorial dos Serviços de Aplicativos](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>Recompilar e reimplantar a imagem

Na pasta raiz, execute o comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) novamente e, assim como antes, substitua `<docker-id>` pela ID da conta do Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Envie por push a imagem atualizada de volta para o Docker Hub.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

A imagem atualizada é reimplantada para o aplicativo de funções.

### <a name="connect-to-your-container-in-azure"></a>Conectar-se ao contêiner no Azure

No navegador, navegue até o seguinte ponto de extremidade do `scm.` das Ferramentas Avançadas (Kudu) para o contêiner do aplicativo de funções, substituindo `<app_name>` pelo nome do aplicativo de funções.

```
https://<app_name>.scm.azurewebsites.net/
```

Entre em sua conta do Azure e selecione a guia **SSH** para criar uma conexão SSH em seu contêiner.

Depois que a conexão for estabelecida, execute o comando `top` para exibir os processos em execução atualmente. 

![Comando superior do Linux em execução em uma sessão SSH.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Gravar no Armazenamento de Fila

O Functions permite que você conecte os serviços do Azure e outros recursos às funções sem precisar escrever seu próprio código de integração. Essas *associações*, que representam a entrada e a saída, são declaradas na definição de função. Dados de associações são fornecidos à função como parâmetros. Um *gatilho* é um tipo especial de associação de entrada. Embora uma função tenha apenas um gatilho, ela pode ter várias associações de entrada e de saída. Para saber mais, confira [Conceitos de gatilhos e de associações do Azure Functions](functions-triggers-bindings.md).

Esta seção mostra como integrar sua função a uma fila do Armazenamento do Azure. A associação de saída que você adiciona a essa função escreve dados de uma solicitação HTTP em uma mensagem na fila.

### <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

Como está usando uma associação de saída Armazenamento de Filas, você precisa ter a extensão de associações de Armazenamento instalada antes de executar o projeto. 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Com exceção dos gatilhos de timer e HTTP, as associações são implementadas como pacotes de extensão. Execute o comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) a seguir na janela Terminal para adicionar o pacote de extensão Armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> Ao usar o Visual Studio, você também pode usar o gerenciador de pacotes NuGet para adicionar esse pacote.

---

Agora, você pode adicionar uma associação de saída do Armazenamento ao projeto.

### <a name="add-an-output-binding"></a>Adicionar uma associação de saída

No Functions, cada tipo de associação requer que um `direction`, `type` e um `name` exclusivo seja definido no arquivo functions.json. A maneira como você define esses atributos depende do idioma do seu aplicativo de funções.

# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Adicionar o código que usa a associação de saída

Depois que a associação é definida, você pode usar o `name` da associação para acessá-la como um atributo na assinatura de função. Ao usar uma associação de saída, não é necessário usar o código do SDK do Armazenamento do Azure para se autenticar, para obter uma referência de fila ou para escrever dados. O tempo de execução do Functions e a associação de saída da fila fazem essas tarefas para você.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>Atualizar o contêiner hospedado

Na pasta raiz, execute o comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) novamente e, desta vez, atualize a versão na tag para `v1.0.2`. Como antes, substitua `<docker-id>` pela ID da conta do Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Envie por push a imagem atualizada de volta para o repositório.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Verificar as atualizações no Azure

Use a mesma URL de antes do navegador para disparar sua função. Você deverá ver a mesma resposta. No entanto, desta vez, a cadeia de caracteres que você passa como o parâmetro `name` é gravada na fila de armazenamento `outqueue`.

### <a name="set-the-storage-account-connection"></a>Definir a conexão da Conta de armazenamento

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Consultar a Fila de armazenamento

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou com êxito o contêiner personalizado em um aplicativo de funções no Azure, considere ler mais sobre os seguintes tópicos:

+ [Funções de monitoramento](functions-monitoring.md)
+ [Opções de escala e de hospedagem](functions-scale.md)
+ [Hospedagem sem servidor baseada em Kubernetes](functions-kubernetes-keda.md)

[Portal do Azure]: https://portal.azure.com
