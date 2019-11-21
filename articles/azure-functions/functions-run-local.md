---
title: Trabalhar com o Azure Functions Core Tools
description: Saiba como codificar e testar o Azure Functions do prompt de comando ou terminal no computador local antes de executá-las no Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 8604df894367ccc25d7e9ffae4453a6b3080b7d8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226691"
---
# <a name="work-with-azure-functions-core-tools"></a>Trabalhar com o Azure Functions Core Tools

Azure Functions Core Tools permite desenvolver e testar as funções em seu computador local a partir do prompt de comando ou terminal. Suas funções locais podem se conectar a serviços do Azure em tempo real e você pode depurar as suas funções em seu computador local usando o runtime total do Functions. Você ainda pode implantar um aplicativo de funções para sua assinatura do Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Developing functions on your local computer and publishing them to Azure using Core Tools follows these basic steps:

> [!div class="checklist"]
> * [Install the Core Tools and dependencies.](#v2)
> * [Create a function app project from a language-specific template.](#create-a-local-functions-project)
> * [Register trigger and binding extensions.](#register-extensions)
> * [Define Storage and other connections.](#local-settings-file)
> * [Create a function from a trigger and language-specific template.](#create-func)
> * [Run the function locally](#start)
> * [Publish the project to Azure](#publish)

## <a name="core-tools-versions"></a>Versões de Core Tools

Há duas versões do Azure Functions Core Tools. A versão que você usa depende do ambiente de desenvolvimento local, da [escolha da linguagem](supported-languages.md) e do nível de suporte necessário:

+ Versão 1.x: oferece suporte à versão 1.x do runtime. Esta versão das ferramentas só tem suporte em computadores Windows e é instalada por meio de um [pacote npm](https://docs.npmjs.com/getting-started/what-is-npm). Com esta versão, você pode criar funções em linguagens experimentais que não são suportadas oficialmente. Para obter mais informações, consulte [línguas suportadas no Azure Functions](supported-languages.md)

+ [Versão 2.x](#v2): oferece suporte à [versão 2.x do runtime](functions-versions.md). Esta versão oferece suporte a [Windows](#windows-npm), [macOS](#brew), e [Linux](#linux). Usa gerenciadores de pacotes específicos da plataforma ou npm para instalação.

A menos que indicado o contrário, os exemplos neste artigo são para a versão 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalação das ferramentas básicas do Azure Functions

O [Ferramentas básicas do Azure Functions] é uma versão local do Azure Functions runtime que pode ser executada no computador local de desenvolvimento. Ele também fornece comandos para criar funções, se conectar ao Azure e implantar projetos de função.

### <a name="v2"></a>Versão 2.x

A versão 2.x das ferramentas usa o 2.x do Azure Functions runtime que se baseia em .NET Core. Esta versão tem suporte em todas as plataformas que o .NET Core 2.x oferece suporte, incluindo [Windows](#windows-npm), [macOS](#brew), e [Linux](#linux). 

> [!IMPORTANT]
> You can bypass the requirement for installing the .NET Core 2.x SDK by using [extension bundles].

#### <a name="windows-npm"></a>Windows

As etapas a seguir usam npm para instalar ferramentas principais no Windows. Você também pode usar [Chocolatey](https://chocolatey.org/). Para obter mais informações, confira o [arquivo Leiame das Ferramentas Principais](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Instale o [Node.js], que inclui npm. Para a versão 2.x das ferramentas, somente Node.js 8.5 e versões posteriores têm suporte.

1. Instale o pacote de ferramentas principais:

    ```bash
    npm install -g azure-functions-core-tools
    ```

   It may take a few minutes for npm to download and install the Core Tools package.

1. If you do not plan to use [extension bundles], install the [.NET Core 2.x SDK for Windows](https://www.microsoft.com/net/download/windows).

#### <a name="brew"></a>MacOS com o Homebrew

As etapas a seguir usam o Homebrew para instalar as ferramentas principais em macOS.

1. Instale o [Homebrew](https://brew.sh/), se ele ainda não estiver instalado.

1. Instale o pacote de ferramentas principais:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

1. If you do not plan to use [extension bundles], install [.NET Core 2.x SDK for macOS](https://www.microsoft.com/net/download/macos).


#### <a name="linux"></a> Linux (Ubuntu/Debian) com APT

As etapas a seguir usma [APT](https://wiki.debian.org/Apt) para instalar as ferramentas principais em sua distribuição Ubuntu/Debian Linux. Para outras distribuições do Linux, confira o [arquivo Leiame das ferramentas principais](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Install the Microsoft package repository GPG key, to validate package integrity:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Set up the .NET development source list before doing an APT update.

   To set up the APT source list for Ubuntu, run this command:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   To set up the APT source list for Debian, run this command:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Check the `/etc/apt/sources.list.d/dotnetdev.list` file for one of the appropriate Linux version strings listed below:

    | Distribuição Linux | Versão |
    | --------------- | ----------- |
    | Debian 10 | `buster` |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Start the APT source update:

    ```bash
    sudo apt-get update
    ```

1. Instale o pacote de ferramentas principais:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. If you do not plan to use [extension bundles], install [.NET Core 2.x SDK for Linux](https://www.microsoft.com/net/download/linux).

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Um diretório de projeto de funções contém os arquivos [host. JSON](functions-host-json.md) e [local.settings.json](#local-settings-file), ao longo de subpastas que contêm o código para funções individuais. Esse diretório é o equivalente ao de um aplicativo de funções no Azure. Para saber mais sobre a estrutura de pastas do Functions, consulte o [guia de desenvolvedores do Azure Functions](functions-reference.md#folder-structure).

Versão 2.x exige que você selecione um idioma padrão para seu projeto quando ele é inicializado, e todas as funções adicionadas usam modelos de idioma padrão. Na versão 1.x, você especifica a linguagem cada vez que você criar uma função.

Na janela do terminal ou em um prompt de comando, execute o seguinte comando para criar o projeto e o repositório Git local:

```bash
func init MyFunctionProj
```

Quando você fornece um nome de projeto, uma nova pasta com esse nome é criada e inicializada. Caso contrário, a pasta atual é inicializada.  
Na versão 2.x, quando você executar o comando você deve escolher um runtime para o seu projeto. 

```output
Select a worker runtime:
dotnet
node
python 
powershell
```

Use cima/para baixo de teclas de direção para escolher um idioma, em seguida, pressione Enter. If you plan to develop JavaScript or TypeScript functions, choose **node**, and then select the language. TypeScript has [some additional requirements](functions-reference-node.md#typescript). 

A saída se parece com o seguinte exemplo para um projeto JavaScript:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` dá suporte às seguintes opções, que são somente da versão 2.x, a menos que haja outro tipo de orientação:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Initializes a [C# class library (.cs) project](functions-dotnet-class-library.md). |
| **`--csx`** | Initializes a [C# script (.csx) project](functions-reference-csharp.md). Você precisam especificar `--csx` nos próximos comandos. |
| **`--docker`** | Crie um Dockerfile para um contêiner usando uma imagem base que se baseia no `--worker-runtime` escolhido. Use esta opção quando planejar publicar em um contêiner do Linux personalizado. |
| **`--docker-only`** |  Adds a Dockerfile to an existing project. Prompts for the worker-runtime if not specified or set in local.settings.json. Use this option when you plan to publish an existing project to a custom Linux container. |
| **`--force`** | Inicializa o projeto mesmo quando há arquivos existentes no projeto. Essa configuração substitui os arquivos existentes pelo mesmo nome. Os outros arquivos na pasta do projeto não serão afetados. |
| **`--java`**  | Initializes a [Java project](functions-reference-java.md). |
| **`--javascript`**<br/>**`--node`**  | Initializes a [JavaScript project](functions-reference-node.md). |
| **`--no-source-control`**<br/>**`-n`** | Impede a criação padrão de um repositório Git na versão 1.x. Na versão 2.x, o repositório git não será criado por padrão. |
| **`--powershell`**  | Initializes a [PowerShell project](functions-reference-powershell.md). |
| **`--python`**  | Initializes a [Python project](functions-reference-python.md). |
| **`--source-control`** | Controla se um repositório git é criado. Por padrão, não é criado um repositório. Quando `true`, um repositório é criado. |
| **`--typescript`**  | Initializes a [TypeScript project](functions-reference-node.md#typescript). |
| **`--worker-runtime`** | Define o runtime de linguagem para o projeto. Supported values are: `csharp`, `dotnet`, `java`, `javascript`,`node` (JavaScript), `powershell`, `python`, and `typescript`. Quando não estiver definido, será solicitado que você escolha seu runtime durante a inicialização. |

> [!IMPORTANT]
> Por padrão, a versão 2.x das ferramentas do Core cria a função de projetos de aplicativo para o runtime do .NET como [projetos de classes C#](functions-dotnet-class-library.md) (. csproj). Esses projetos C#, que podem ser usados com o Visual Studio ou com Visual Studio Code, são compilados durante o teste e ao publicar no Azure. Se você deseja criar e trabalhar com o os mesmos arquivos de script C# (. CSx) criados na versão 1. x e no portal, você deve incluir o `--csx` parâmetro ao criar e implantar funções.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por padrão, essas configurações não são migradas automaticamente quando o projeto é publicado no Azure. Use a opção `--publish-local-settings` [quando publicar](#publish) para se certificar de que essas configurações serão adicionadas ao aplicativo de funções no Azure. Observe que os valores em **ConnectionStrings** nunca são publicados.

Os valores de configuração do aplicativo de funções também podem ser lidos em seu código como variáveis de ambiente. Para obter mais informações, confira a seção de variáveis de Ambiente desses tópicos de referência específicos de linguagem:

* [C# pré-compilado](functions-dotnet-class-library.md#environment-variables)
* [Script do C# (. CSx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

When no valid storage connection string is set for [`AzureWebJobsStorage`] and the emulator isn't being used, the following error message is shown:

> Valor ausente para AzureWebJobsStorage em local.settings.json. Isso é necessário para todos os gatilhos diferentes de HTTP. É possível executar o func azure functionapp fetch-app-settings \<functionAppName\>' ou especificar uma cadeia de conexão em local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Obter suas cadeias de conexão de armazenamento

Mesmo usando o emulador de armazenamento para desenvolvimento, convém testar com uma conexão de armazenamento real. Supondo que você já tenha [criado uma conta de armazenamento](../storage/common/storage-create-storage-account.md), será possível obter uma cadeia de conexão de armazenamento válida de uma destas maneiras:

- From the [Azure portal], search for and select **Storage accounts**. 
  ![Select Storagea accounts from Azure portal](./media/functions-run-local/select-storage-accounts.png)
  
  Select your storage account, select **Access keys** in **Settings**, then copy one of the **Connection string** values.
  ![Copy connection string from Azure portal](./media/functions-run-local/copy-storage-connection-portal.png)

- Use o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para conectar-se à sua conta do Azure. No **Gerenciador**, expanda sua assinatura, selecione sua conta de armazenamento e copie a cadeia de conexão primária ou secundária.

  ![Copiar cadeia de conexão do Gerenciador de Armazenamento](./media/functions-run-local/storage-explorer.png)

+ Use as Ferramentas Básicas para baixar a cadeia de conexão do Azure com um dos seguintes comandos:

  + Baixe todas as configurações de um aplicativo de funções existente:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Obtenha a cadeia de conexão para uma conta de armazenamento específica:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Enquanto ainda não tiver se conectado ao Azure, será solicitado que você faça isso.

## <a name="create-func"></a>Criar uma função

Para criar uma função, execute o seguinte comando:

```bash
func new
```

Na versão 2.x, quando você executa `func new` você é solicitado a escolher um modelo no idioma padrão do seu aplicativo de funções, em seguida, você também precisará escolher um nome para sua função. Na versão 1.x, você também precisará escolher o idioma.

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

Código de função é gerado em uma subpasta com o nome da função fornecido, como você pode ver na seguinte saída do gatilho de fila:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Você também pode especificar essas opções no comando usando os seguintes argumentos:

| Argumento     | Descrição                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Versão 2. x) Gera os mesmos modelos C# (. CSx) de script usados na versão 1. x e no portal. |
| **`--language -l`**| A linguagem de programação modelo, como C#, F# ou JavaScript. Essa opção é necessária na versão 1.x. Na versão 2.x, não use essa opção nem escolha uma linguagem que corresponda ao runtime do trabalho. |
| **`--name -n`** | O nome da função. |
| **`--template -t`** | Use o `func templates list` comando para ver a lista completa de modelos disponíveis para cada idioma com suporte.   |

Por exemplo, para criar um gatilho de HTTP de JavaScript em um único comando, execute:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Para criar uma função ativada por fila em um único comando, execute:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Executar funções localmente

Para executar um projeto de funções, execute o host de funções. The host enables triggers for all functions in the project. 

### <a name="version-2x"></a>Versão 2.x

In version 2.x of the runtime, the start command varies, depending on your project language.

#### <a name="c"></a>C\#

```command
func start --build
```

#### <a name="javascript"></a>JavaScript

```command
func start
```

#### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

### <a name="version-1x"></a>Versão 1.x

Version 1.x of the Functions runtime requires the `host` command, as in the following example:

```command
func host start
```

`func start` dá suporte para as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Não compile o projeto atual antes da execução. Somente para projetos dotnet. O padrão é definido como false. Versão 2.x somente. |
| **`--cert`** | O caminho para um arquivo .pfx que contém uma chave privada. Usado somente com `--useHttps`. Versão 2.x somente. |
| **`--cors-credentials`** | Permita solicitações autenticadas de origem cruzada (ou seja, cookies e cabeçalho de autenticação) somente na versão 2.x. |
| **`--cors`** | Uma lista separada por vírgulas de origens CORS, sem espaços. |
| **`--language-worker`** | Argumentos para configurar o trabalhador de idioma. Versão 2.x somente. |
| **`--nodeDebugPort -n`** | A porta para usar o depurador de nós. Padrão: um valor de launch.json ou 5858. Versão 1.x somente. |
| **`--password`** | A senha ou um arquivo que contém a senha para um arquivo .pfx. Usado somente com `--cert`. Versão 2.x somente. |
| **`--port -p`** | A porta local na qual escutar. Valor Padrão: 7071. |
| **`--pause-on-error`** | Pausar para entrada adicional antes de encerrar o processo. Usado apenas ao iniciar as Core Tools de um IDE (ambiente de desenvolvimento integrado).|
| **`--script-root --prefix`** | Usado para especificar o caminho para a raiz do aplicativo de funções que deve ser executado ou implantado. Usado para projetos compilados que geram arquivos de projeto para uma subpasta. Por exemplo, quando você cria um projeto de biblioteca de classes C#, os arquivos host.json, local.settings.json e function.json são gerados em uma subpasta *raiz* com um caminho como `MyProject/bin/Debug/netstandard2.0`. Nesse caso, defina o prefixo como `--script-root MyProject/bin/Debug/netstandard2.0`. Essa é a raiz do aplicativo de funções durante a execução no Azure. |
| **`--timeout -t`** | O tempo limite para o host de funções ser iniciado, em segundos. Padrão: 20 segundos.|
| **`--useHttps`** | Associar a `https://localhost:{port}` em vez de `http://localhost:{port}`. Por padrão, essa opção cria um certificado confiável no computador.|

Quando o host de funções é iniciado, ele gera as funções acionadas por URL de HTTP:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>Ao executar localmente, a autenticação não é imposta para pontos de extremidade HTTP. Isso significa que todas as solicitações HTTP locais são tratadas como `authLevel = "anonymous"`. Para obter mais informações, confira o [artigo de associação HTTP](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Transferência dos dados de teste para uma função

Para testar as funções localmente, [inicie o host do Functions](#start) e chame os pontos de extremidade no servidor local usando solicitações HTTP. O ponto de extremidade que você chama depende do tipo de função.

>[!NOTE]
> Os exemplos neste tópico usam a ferramenta cURL para enviar solicitações HTTP do terminal ou de um prompt de comando. Você pode usar uma ferramenta de sua escolha para enviar solicitações HTTP para o servidor local. The cURL tool is available by default on Linux-based systems and Windows 10 build 17063 and later. On older Windows, you must first download and install the [cURL tool](https://curl.haxx.se/).

Para ter informações mais gerais sobre o teste de funções, confira [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funções disparadas por HTTP e webhook

Chame o seguinte ponto de extremidade para executar localmente funções disparadas por HTTP e webhook:

    http://localhost:{port}/api/{function_name}

Use o mesmo nome de servidor e porta no qual o host do Functions está escutando. Veja isso na saída gerada ao iniciar o host do Function. Chame essa URL usando qualquer método HTTP com suporte do disparador.

O seguinte comando cURL dispara a função de início rápido `MyHttpTrigger` de uma solicitação GET com o parâmetro _name_ transmitido na cadeia de consulta.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

O exemplo a seguir é a mesma função chamada a partir de uma solicitação POST passando _name_ no corpo da solicitação:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Você pode fazer solicitações GET de um navegador passando dados na cadeia de consulta. Para todos os outros métodos HTTP, você deve usar cURL, Fiddler, Postman ou uma ferramenta de teste HTTP semelhante.

#### <a name="non-http-triggered-functions"></a>Funções disparadas por algo diferente de HTTP

Para todos os tipos de funções que não sejam gatilhos HTTP e webhooks, você pode testar suas funções localmente chamando um ponto de extremidade de administração. Chamar esse ponto de extremidade com uma solicitação HTTP POST no servidor local dispara a função. Como alternativa, é possível passar dados de teste para a execução no corpo da solicitação POST. Essa funcionalidade é semelhante á guia **Teste** no Portal do Azure.

Chame o seguinte ponto de extremidade de administrador para disparar funções que não são HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Para passar dados de teste para o ponto de extremidade administrador de uma função, é necessário fornecer os dados no corpo de uma mensagem de solicitação POST. O corpo da mensagem deve ter o seguinte formato JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

O valor `<trigger_input>` contém dados em um formato esperado pela função. O exemplo de cURL a seguir é um POST para uma função `QueueTriggerJS`. Nesse caso, a entrada é uma cadeia de caracteres equivalente à mensagem esperada na fila.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Usar o comando `func run` na versão 1.x

>[!IMPORTANT]
> O comando `func run` não tem suporte na versão 2.x das ferramentas. Para saber mais, consulte o tópico [Como direcionar para versões do Azure Functions runtime](set-runtime-version.md).

Você também pode invocar uma função diretamente usando `func run <FunctionName>` e fornecer dados de entrada para a função. Esse comando é semelhante à execução de uma função usando a guia **Testar** no Portal do Azure.

`func run` dá suporte para as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Conteúdo embutido. |
| **`--debug -d`** | Anexe um depurador ao processo de host antes de executar a função.|
| **`--timeout -t`** | Tempo de espera (em segundos) até que o host local de funções esteja pronto.|
| **`--file -f`** | O nome do arquivo a ser usado como conteúdo.|
| **`--no-interactive`** | Não solicitará a entrada. Útil para cenários de automação.|

Por exemplo, para chamar uma função ativada por HTTP e passar o corpo do conteúdo, execute o seguinte comando:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publicar no Azure

The Azure Functions Core Tools supports two types of deployment: deploying function project files directly to your function app via [Zip Deploy](functions-deployment-technologies.md#zip-deploy) and [deploying a custom Docker container](functions-deployment-technologies.md#docker-container). You must have already [created a function app in your Azure subscription](functions-cli-samples.md#create), to which you'll deploy your code. Os projetos que exigem build devem ser compilados para que os binários possam ser implantados.

A project folder may contain language-specific files and directories that shouldn't be published. Excluded items are listed in a .funcignore file in the root project folder.     

### <a name="project-file-deployment"></a>Deployment (project files)

To publish your local code to a function app in Azure, use the `publish` command:

```bash
func azure functionapp publish <FunctionAppName>
```

Esse comando publica a um aplicativo de funções existente no Azure. You'll get an error if you try to publish to a `<FunctionAppName>` that doesn't exist in your subscription. Para saber como criar um aplicativo de funções pelo prompt de comando ou pela janela do terminal usando a CLI do Azure, consulte [Criar um aplicativo de funções para execução sem servidor](./scripts/functions-cli-create-serverless.md). By default, this command uses [remote build](functions-deployment-technologies.md#remote-build) and deploys your app to [run from the deployment package](run-functions-from-deployment-package.md). To disable this recommended deployment mode, use the `--nozip` option.

>[!IMPORTANT]
> Quando você cria um aplicativo de funções no portal do Azure, ele usa a versão 2.x do runtime do Core Tools por padrão. Para que o aplicativo de funções use a versão 1.x do runtime, siga as instruções em [Executar na versão 1.x](functions-versions.md#creating-1x-apps).
> Você não pode alterar a versão do runtime de um aplicativo de funções que tenha funções existentes.

The following publish options apply for both versions, 1.x and 2.x:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Configurações de publicação em local.settings.json do Azure, a solicitação para substituir se a configuração já existe. If you are using the storage emulator, first change the app setting to an [actual storage connection](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Suprima o prompt para substituir as configurações do aplicativo quando `--publish-local-settings -i` for usado.|

Somente há suporte para as seguintes opções de publicação na versão 2.x:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Somente publicar as configurações e ignorar o conteúdo. O padrão é solicitado. |
|**`--list-ignored-files`** | Exibe uma lista de arquivos que são ignorados durante a publicação, que é baseada no arquivo .funcignore. |
| **`--list-included-files`** | Exibe uma lista de arquivos que são publicados, que é baseada no arquivo .funcignore. |
| **`--nozip`** | Transforma o padrão de `Run-From-Package` modo desativado. |
| **`--build-native-deps`** | Ignora a geração da pasta .wheels durante a publicação de python de aplicativos de funções. |
| **`--build`**<br/>**`-b`** | Performs build action when deploying to a Linux function app. Accepts: `remote` and `local`. |
| **`--additional-packages`** | Lista de pacotes para instalar ao compilar dependências nativas. Por exemplo: `python3-dev libevent-dev`. |
| **`--force`** | Ignorar a verificação de pré-publicação em determinados cenários. |
| **`--csx`** | Publicar um projeto de script (.csx) C#. |
| **`--no-build`** | Don't build .NET class library functions. |
| **`--dotnet-cli-params`** | Durante a publicação de funções C# (.csproj), o Core Tools chama 'dotnet build --output bin/publish'. Todos os parâmetros passados para isso serão acrescentados à linha de comando. |

### <a name="deployment-custom-container"></a>Deployment (custom container)

Azure Functions lets you deploy your function project in a [custom Docker container](functions-deployment-technologies.md#docker-container). Para obter mais informações, confira [Criar uma função no Linux usando uma imagem personalizada](functions-create-function-linux-custom-image.md). Os contêineres personalizados precisam ter um Dockerfile. To create an app with a Dockerfile, use the --dockerfile option on `func init`.

```bash
func deploy
```

As seguintes opções de implantação de contêiner personalizado estão disponíveis:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--registry`** | O nome de um Registro do Docker ao qual o usuário atual está conectado. |
| **`--platform`** | A plataforma de hospedagem do aplicativo de funções. As opções válidas são `kubernetes` |
| **`--name`** | Nome do aplicativo de funções. |
| **`--max`**  | Opcionalmente, define o número máximo de instâncias do aplicativo de funções a serem implantadas. |
| **`--min`**  | Opcionalmente, define o número mínimo de instâncias do aplicativo de funções a serem implantadas. |
| **`--config`** | Define um arquivo de configuração de implantação opcional. |

## <a name="monitoring-functions"></a>Funções de monitoramento

The recommended way to monitor the execution of your functions is by integrating with Azure Application Insights. You can also stream execution logs to your local computer. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md).

### <a name="enable-application-insights-integration"></a>Habilitar a integração do Application Insights

When you create a function app in the Azure portal, the Application Insights integration is done for you by default. No entanto, quando você cria seu aplicativo de funções usando a CLI do Azure, não é feita a integração em seu aplicativo de funções no Azure.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Enable streaming logs

You can view a stream of log files being generated by your functions in a command-line session on your local computer. 

#### <a name="native-streaming-logs"></a>Native streaming logs

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

This type of streaming logs requires that you [enable Application Insights integration](#enable-application-insights-integration) for your function app.   


## <a name="next-steps"></a>Próximos passos

As principais ferramentas do Azure Functions são [Código-fonte aberto e hospedado no GitHub](https://github.com/azure/azure-functions-cli).  
Para arquivar uma solicitação de bug ou recurso, [abra um problema do GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Ferramentas básicas do Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
[extension bundles]: functions-bindings-register.md#extension-bundles
