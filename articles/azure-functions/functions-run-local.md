---
title: Trabalhar com o Azure Functions Core Tools
description: Saiba como codificar e testar o Azure Functions do prompt de comando ou terminal no computador local antes de executá-las no Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 19691a654162ee3855cb257fd42e29d2e1fc0157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276654"
---
# <a name="work-with-azure-functions-core-tools"></a>Trabalhar com o Azure Functions Core Tools

Azure Functions Core Tools permite desenvolver e testar as funções em seu computador local a partir do prompt de comando ou terminal. Suas funções locais podem se conectar a serviços do Azure em tempo real e você pode depurar as suas funções em seu computador local usando o runtime total do Functions. Você ainda pode implantar um aplicativo de funções para sua assinatura do Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Desenvolver funções em seu computador local e publicá-las no Azure usando ferramentas principais segue estas etapas básicas:

> [!div class="checklist"]
> * [Instale as Ferramentas principais e as dependências.](#v2)
> * [Crie um projeto de aplicativo de função a partir de um modelo específico de idioma.](#create-a-local-functions-project)
> * [Registre o gatilho e as extensões de ligação.](#register-extensions)
> * [Defina armazenamento e outras conexões.](#local-settings-file)
> * [Crie uma função a partir de um gatilho e modelo específico de linguagem.](#create-func)
> * [Execute a função localmente.](#start)
> * [Publique o projeto para o Azure.](#publish)

## <a name="core-tools-versions"></a>Versões de Core Tools

Existem três versões do Azure Functions Core Tools. A versão que você usa depende do seu ambiente de desenvolvimento local, [escolha do idioma](supported-languages.md)e nível de suporte necessário:

+ **Versão 1.x**: Suporta a versão 1.x do tempo de execução das funções do Azure. Esta versão das ferramentas só tem suporte em computadores Windows e é instalada por meio de um [pacote npm](https://www.npmjs.com/package/azure-functions-core-tools).

+ [**Versão 2.x/3.x**](#v2): Suporta a [versão 2.x ou 3.x do tempo de execução das funções do Azure](functions-versions.md). Essas versões suportam [Windows,](/azure/azure-functions/functions-run-local?tabs=windows#v2) [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)e [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) e usam gerentes de pacotes específicos da plataforma ou npm para instalação.

Salvo observação em contrário, os exemplos neste artigo são para a versão 3.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalação das ferramentas básicas do Azure Functions

O [Azure Functions Core Tools] é uma versão local do Azure Functions runtime que pode ser executada no computador local de desenvolvimento. Ele também fornece comandos para criar funções, se conectar ao Azure e implantar projetos de função.

>[!IMPORTANT]
>Você deve ter o [Azure CLI](/cli/azure/install-azure-cli) instalado localmente para poder publicar no Azure a partir do Azure Functions Core Tools.  

### <a name="version-2x-and-3x"></a><a name="v2"></a>Versão 2.x e 3.x

A versão 2.x/3.x das ferramentas usa o tempo de execução das funções do Azure que é construído no .NET Core. Esta versão é suportada em todas as plataformas com suporte .NET Core, incluindo [Windows,](/azure/azure-functions/functions-run-local?tabs=windows#v2) [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)e [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2). 

> [!IMPORTANT]
> Você pode ignorar o requisito para instalar o .NET Core SDK usando [pacotes de extensão].

# <a name="windows"></a>[Windows](#tab/windows)

As etapas a seguir usam npm para instalar ferramentas principais no Windows. Você também pode usar [Chocolatey](https://chocolatey.org/). Para obter mais informações, confira o [arquivo Leiame das Ferramentas Principais](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Instale o [Node.js], que inclui npm.
    - Para a versão 2.x das ferramentas, somente Node.js 8.5 e versões posteriores têm suporte.
    - Para a versão 3.x das ferramentas, apenas as versões Node.js 10 e posteriores são suportadas.

1. Instale o pacote de ferramentas principais:

    ##### <a name="v2x"></a>v2.x

    ```cmd
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```cmd
    npm install -g azure-functions-core-tools@3
    ```

   Pode levar alguns minutos para o npm baixar e instalar o pacote Core Tools.

1. Se você não planeja usar [pacotes de extensão,]instale o [.NET Core 2.x SDK para Windows](https://www.microsoft.com/net/download/windows).

# <a name="macos"></a>[macOS](#tab/macos)

As etapas a seguir usam o Homebrew para instalar as ferramentas principais em macOS.

1. Instale o [Homebrew](https://brew.sh/), se ele ainda não estiver instalado.

1. Instale o pacote de ferramentas principais:

    ##### <a name="v2x"></a>v2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="linux"></a>[Linux](#tab/linux)

As etapas a seguir usma [APT](https://wiki.debian.org/Apt) para instalar as ferramentas principais em sua distribuição Ubuntu/Debian Linux. Para outras distribuições do Linux, confira o [arquivo Leiame das ferramentas principais](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Instale a chave GPG do repositório do pacote Microsoft para validar a integridade do pacote:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Configure a lista de origem de desenvolvimento .NET antes de fazer uma atualização aPT.

   Para configurar a lista de origem do APT para ubuntu, execute este comando:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Para configurar a lista de origem do APT para Debian, execute este comando:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Verifique `/etc/apt/sources.list.d/dotnetdev.list` o arquivo de uma das strings de versão linux apropriadas listadas abaixo:

    | Distribuição do Linux | Versão |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Inicie a atualização de origem do APT:

    ```bash
    sudo apt-get update
    ```

1. Instale o pacote de ferramentas principais:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Se você não planeja usar [pacotes de extensão,]instale [o .NET Core 2.x SDK para Linux](https://www.microsoft.com/net/download/linux).

---

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Um diretório de projeto de funções contém os arquivos [host. JSON](functions-host-json.md) e [local.settings.json](#local-settings-file), ao longo de subpastas que contêm o código para funções individuais. Esse diretório é o equivalente ao de um aplicativo de funções no Azure. Para saber mais sobre a estrutura de pastas do Functions, consulte o [guia de desenvolvedores do Azure Functions](functions-reference.md#folder-structure).

A versão 2.x exige que você selecione um idioma padrão para o seu projeto quando ele for inicializado. Na versão 2.x, todas as funções adicionadas usam modelos de linguagem padrão. Na versão 1.x, você especifica a linguagem cada vez que você criar uma função.

Na janela do terminal ou em um prompt de comando, execute o seguinte comando para criar o projeto e o repositório Git local:

```
func init MyFunctionProj
```

Quando você fornece um nome de projeto, uma nova pasta com esse nome é criada e inicializada. Caso contrário, a pasta atual é inicializada.  
Na versão 2.x, quando você executar o comando você deve escolher um runtime para o seu projeto. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Use cima/para baixo de teclas de direção para escolher um idioma, em seguida, pressione Enter. Se você planeja desenvolver funções JavaScript ou TypeScript, escolha **nó**e selecione o idioma. TypeScript tem [alguns requisitos adicionais](functions-reference-node.md#typescript). 

A saída se parece com o seguinte exemplo para um projeto JavaScript:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init` dá suporte às seguintes opções, que são somente da versão 2.x, a menos que haja outro tipo de orientação:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Inicializa um [projeto de biblioteca de classe C# (.cs).](functions-dotnet-class-library.md) |
| **`--csx`** | Inicializa um [projeto de script C# (.csx).](functions-reference-csharp.md) Você precisam especificar `--csx` nos próximos comandos. |
| **`--docker`** | Crie um Dockerfile para um contêiner usando uma imagem base que se baseia no `--worker-runtime` escolhido. Use esta opção quando planejar publicar em um contêiner do Linux personalizado. |
| **`--docker-only`** |  Adiciona um arquivo Docker a um projeto existente. Solicita para o tempo de execução do trabalhador se não especificado ou definido em local.settings.json. Use essa opção quando planeja publicar um projeto existente em um contêiner Linux personalizado. |
| **`--force`** | Inicializa o projeto mesmo quando há arquivos existentes no projeto. Essa configuração substitui os arquivos existentes pelo mesmo nome. Os outros arquivos na pasta do projeto não serão afetados. |
| **`--java`**  | Inicia um [projeto Java](functions-reference-java.md). |
| **`--javascript`**<br/>**`--node`**  | Inicializa um [projeto JavaScript](functions-reference-node.md). |
| **`--no-source-control`**<br/>**`-n`** | Impede a criação padrão de um repositório Git na versão 1.x. Na versão 2.x, o repositório git não será criado por padrão. |
| **`--powershell`**  | Inicia um [projeto PowerShell](functions-reference-powershell.md). |
| **`--python`**  | Inicializa um [projeto Python](functions-reference-python.md). |
| **`--source-control`** | Controla se um repositório git é criado. Por padrão, não é criado um repositório. Quando `true`, um repositório é criado. |
| **`--typescript`**  | Inicializa um [projeto TypeScript](functions-reference-node.md#typescript). |
| **`--worker-runtime`** | Define o runtime de linguagem para o projeto. Os valores `csharp`suportados são: `dotnet`, `powershell`, `python` `java` `javascript`, `typescript`(`node` JavaScript), , e . Quando não estiver definido, você é solicitado a escolher seu tempo de execução durante a inicialização. |

> [!IMPORTANT]
> Por padrão, a versão 2.x das ferramentas do Core cria a função de projetos de aplicativo para o runtime do .NET como [projetos de classes C#](functions-dotnet-class-library.md) (. csproj). Esses projetos C#, que podem ser usados com o Visual Studio ou com Visual Studio Code, são compilados durante o teste e ao publicar no Azure. Se você deseja criar e trabalhar com o os mesmos arquivos de script C# (. CSx) criados na versão 1. x e no portal, você deve incluir o `--csx` parâmetro ao criar e implantar funções.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por padrão, essas configurações não são migradas automaticamente quando o projeto é publicado no Azure. Use a opção `--publish-local-settings`[quando publicar](#publish) para se certificar de que essas configurações serão adicionadas ao aplicativo de funções no Azure. Observe que os valores em **ConnectionStrings** nunca são publicados.

Os valores de configuração do aplicativo de funções também podem ser lidos em seu código como variáveis de ambiente. Para obter mais informações, confira a seção de variáveis de Ambiente desses tópicos de referência específicos de linguagem:

* [C# pré-compilado](functions-dotnet-class-library.md#environment-variables)
* [Script do C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

Quando nenhuma seqüência de [`AzureWebJobsStorage`] conexão de armazenamento válida é definida e o emulador não está sendo usado, a seguinte mensagem de erro é mostrada:

> Valor ausente para AzureWebJobsStorage em local.settings.json. Isso é necessário para todos os gatilhos diferentes de HTTP. É possível executar o func azure functionapp fetch-app-settings \<functionAppName\>' ou especificar uma cadeia de conexão em local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Obter suas cadeias de conexão de armazenamento

Mesmo ao usar o Emulador de Armazenamento Microsoft Azure para desenvolvimento, você pode querer testar com uma conexão de armazenamento real. Supondo que você já tenha [criado uma conta de armazenamento](../storage/common/storage-create-storage-account.md), será possível obter uma cadeia de conexão de armazenamento válida de uma destas maneiras:

- No [portal Azure,]procure e selecione **contas de armazenamento**. 
  ![Selecione contas de armazenamento no portal Azure](./media/functions-run-local/select-storage-accounts.png)
  
  Selecione sua conta de armazenamento, selecione **Teclas de acesso** em **Configurações**e copie um dos valores da **seqüência de string 'Conexão'.**
  ![Copiar cadeia de conexão do portal do Azure](./media/functions-run-local/copy-storage-connection-portal.png)

- Use o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para conectar-se à sua conta do Azure. No **Explorer,** expanda sua assinatura, expanda **contas de armazenamento,** selecione sua conta de armazenamento e copie a seqüência de conexões primária ou secundária.

  ![Copiar cadeia de conexão do Gerenciador de Armazenamento](./media/functions-run-local/storage-explorer.png)

+ Use as Ferramentas Básicas para baixar a cadeia de conexão do Azure com um dos seguintes comandos:

  + Baixe todas as configurações de um aplicativo de funções existente:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Obtenha a cadeia de conexão para uma conta de armazenamento específica:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Quando você ainda não está conectado ao Azure, você é solicitado a fazê-lo.

## <a name="create-a-function"></a><a name="create-func"></a>Criar uma função

Para criar uma função, execute o seguinte comando:

```
func new
```

Na versão 2.x, quando você executa `func new` você é solicitado a escolher um modelo no idioma padrão do seu aplicativo de funções, em seguida, você também precisará escolher um nome para sua função. Na versão 1.x, você também precisará escolher o idioma.

<pre>
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
</pre>

Código de função é gerado em uma subpasta com o nome da função fornecido, como você pode ver na seguinte saída do gatilho de fila:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Você também pode especificar essas opções no comando usando os seguintes argumentos:

| Argumento     | Descrição                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Versão 2. x) Gera os mesmos modelos C# (. CSx) de script usados na versão 1. x e no portal. |
| **`--language`**, **`-l`**| A linguagem de programação modelo, como C#, F# ou JavaScript. Essa opção é necessária na versão 1.x. Na versão 2.x, não use essa opção nem escolha uma linguagem que corresponda ao runtime do trabalho. |
| **`--name`**, **`-n`** | O nome da função. |
| **`--template`**, **`-t`** | Use o `func templates list` comando para ver a lista completa de modelos disponíveis para cada idioma com suporte.   |

Por exemplo, para criar um gatilho de HTTP de JavaScript em um único comando, execute:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Para criar uma função ativada por fila em um único comando, execute:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Executar funções localmente

Para executar um projeto de funções, execute o host de funções. O host habilita gatilhos para todas as funções do projeto. O comando start varia, dependendo da linguagem do projeto.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```
# <a name="javascript"></a>[Javascript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Este comando deve ser [executado em um ambiente virtual](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> A versão 1.x do tempo `host` de execução Functions requer o comando, como no exemplo a seguir:
>
> ```
> func host start
> ```

`func start` dá suporte para as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Não compile o projeto atual antes da execução. Somente para projetos dotnet. O padrão é definido como false. Não suportado para a versão 1.x. |
| **`--cert`** | O caminho para um arquivo .pfx que contém uma chave privada. Usado somente com `--useHttps`. Não suportado para a versão 1.x. |
| **`--cors-credentials`** | Permitir solicitações autenticadas de origem cruzada (ou seja, cookies e o cabeçalho de autenticação) Não suportados para a versão 1.x. |
| **`--cors`** | Uma lista separada por vírgulas de origens CORS, sem espaços. |
| **`--language-worker`** | Argumentos para configurar o trabalhador de idioma. Por exemplo, você pode habilitar a depuração para o trabalhador do idioma fornecendo [porta de depuração e outros argumentos necessários](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Não suportado para a versão 1.x. |
| **`--nodeDebugPort`**, **`-n`** | A porta para o depurador Node.js usar. Padrão: um valor de launch.json ou 5858. Versão 1.x somente. |
| **`--password`** | A senha ou um arquivo que contém a senha para um arquivo .pfx. Usado somente com `--cert`. Não suportado para a versão 1.x. |
| **`--port`**, **`-p`** | A porta local na qual escutar. Valor Padrão: 7071. |
| **`--pause-on-error`** | Pausar para entrada adicional antes de encerrar o processo. Usado apenas ao iniciar as Core Tools de um IDE (ambiente de desenvolvimento integrado).|
| **`--script-root`**, **`--prefix`** | Usado para especificar o caminho para a raiz do aplicativo de funções que deve ser executado ou implantado. Usado para projetos compilados que geram arquivos de projeto para uma subpasta. Por exemplo, quando você cria um projeto de biblioteca de classes C#, os arquivos host.json, local.settings.json e function.json são gerados em uma subpasta *raiz* com um caminho como `MyProject/bin/Debug/netstandard2.0`. Nesse caso, defina o prefixo como `--script-root MyProject/bin/Debug/netstandard2.0`. Essa é a raiz do aplicativo de funções durante a execução no Azure. |
| **`--timeout`**, **`-t`** | O tempo limite para o host de funções ser iniciado, em segundos. Padrão: 20 segundos.|
| **`--useHttps`** | Associar a `https://localhost:{port}` em vez de `http://localhost:{port}`. Por padrão, essa opção cria um certificado confiável no computador.|

Quando o host de funções é iniciado, ele gera as funções acionadas por URL de HTTP:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Ao ser executado localmente, a autorização não é aplicada para pontos finais HTTP. Isso significa que todas as solicitações HTTP locais são tratadas como `authLevel = "anonymous"`. Para obter mais informações, confira o [artigo de associação HTTP](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Transferência dos dados de teste para uma função

Para testar as funções localmente, [inicie o host do Functions](#start) e chame os pontos de extremidade no servidor local usando solicitações HTTP. O ponto de extremidade que você chama depende do tipo de função.

>[!NOTE]
> Os exemplos neste tópico usam a ferramenta cURL para enviar solicitações HTTP do terminal ou de um prompt de comando. Você pode usar uma ferramenta de sua escolha para enviar solicitações HTTP para o servidor local. A ferramenta cURL está disponível por padrão em sistemas baseados em Linux e windows 10 build 17063 e posterior. No Windows mais antigo, você deve primeiro baixar e instalar a [ferramenta cURL](https://curl.haxx.se/).

Para ter informações mais gerais sobre o teste de funções, confira [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funções disparadas por HTTP e webhook

Chame o seguinte ponto de extremidade para executar localmente funções disparadas por HTTP e webhook:

    http://localhost:{port}/api/{function_name}

Use o mesmo nome de servidor e porta no qual o host do Functions está escutando. Veja isso na saída gerada ao iniciar o host do Function. Chame essa URL usando qualquer método HTTP com suporte do disparador.

O seguinte comando cURL dispara a função de início rápido `MyHttpTrigger` de uma solicitação GET com o parâmetro _name_ transmitido na cadeia de consulta.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

O exemplo a seguir é a mesma função chamada a partir de uma solicitação POST passando _name_ no corpo da solicitação:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Você pode fazer solicitações GET de um navegador passando dados na cadeia de consulta. Para todos os outros métodos HTTP, você deve usar cURL, Fiddler, Postman ou uma ferramenta de teste HTTP semelhante.

#### <a name="non-http-triggered-functions"></a>Funções disparadas por algo diferente de HTTP

Para todos os tipos de funções que não os gatilhos HTTP e webhooks e gatilhos event grid, você pode testar suas funções localmente chamando um ponto final de administração. Chamar esse ponto de extremidade com uma solicitação HTTP POST no servidor local dispara a função. 

Para testar as funções desencadeadas no Event Grid localmente, consulte [testes locais com o aplicativo web viewer](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app).

Como alternativa, é possível passar dados de teste para a execução no corpo da solicitação POST. Essa funcionalidade é semelhante á guia **Teste** no Portal do Azure.

Chame o seguinte ponto de extremidade de administrador para disparar funções que não são HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Para passar dados de teste para o ponto de extremidade administrador de uma função, é necessário fornecer os dados no corpo de uma mensagem de solicitação POST. O corpo da mensagem deve ter o seguinte formato JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

O valor `<trigger_input>` contém dados em um formato esperado pela função. O exemplo de cURL a seguir é um POST para uma função `QueueTriggerJS`. Nesse caso, a entrada é uma cadeia de caracteres equivalente à mensagem esperada na fila.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Usando `func run` o comando (somente versão 1.x)

>[!IMPORTANT]
> O `func run` comando só é suportado na versão 1.x das ferramentas. Para saber mais, consulte o tópico [Como direcionar para versões do Azure Functions runtime](set-runtime-version.md).

Na versão 1.x, você também pode `func run <FunctionName>` invocar uma função diretamente usando e fornecer dados de entrada para a função. Esse comando é semelhante à execução de uma função usando a guia **Testar** no Portal do Azure.

`func run` dá suporte para as seguintes opções:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Conteúdo embutido. |
| **`--debug`**, **`-d`** | Anexe um depurador ao processo de host antes de executar a função.|
| **`--timeout`**, **`-t`** | Tempo de espera (em segundos) até que o host local de funções esteja pronto.|
| **`--file`**, **`-f`** | O nome do arquivo a ser usado como conteúdo.|
| **`--no-interactive`** | Não solicitará a entrada. Útil para cenários de automação.|

Por exemplo, para chamar uma função ativada por HTTP e passar o corpo do conteúdo, execute o seguinte comando:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Publicar no Azure

O Azure Functions Core Tools suporta dois tipos de implantação: implantando arquivos de projeto de função diretamente para o seu aplicativo de função via [Zip Deploy](functions-deployment-technologies.md#zip-deploy) e [implantando um contêiner Docker personalizado](functions-deployment-technologies.md#docker-container). Você já deve ter [criado um aplicativo de função em sua assinatura do Azure,](functions-cli-samples.md#create)para o qual você implantará seu código. Os projetos que exigem build devem ser compilados para que os binários possam ser implantados.

>[!IMPORTANT]
>Você deve ter o [Azure CLI](/cli/azure/install-azure-cli) instalado localmente para poder publicar no Azure a partir de Ferramentas Principais.  

Uma pasta de projeto pode conter arquivos e diretórios específicos do idioma que não devem ser publicados. Os itens excluídos estão listados em um arquivo .funcignore na pasta de projeto raiz.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Implantar arquivos de projeto

Para publicar seu código local em um aplicativo `publish` de função no Azure, use o comando:

```
func azure functionapp publish <FunctionAppName>
```

Esse comando publica a um aplicativo de funções existente no Azure. Você terá um erro se tentar publicar `<FunctionAppName>` em uma que não existe na sua assinatura. Para saber como criar um aplicativo de funções pelo prompt de comando ou pela janela do terminal usando a CLI do Azure, consulte [Criar um aplicativo de funções para execução sem servidor](./scripts/functions-cli-create-serverless.md). Por padrão, este comando usa [compilação remota](functions-deployment-technologies.md#remote-build) e implanta seu aplicativo para [executar a partir do pacote de implantação](run-functions-from-deployment-package.md). Para desativar este modo de implantação `--nozip` recomendado, use a opção.

>[!IMPORTANT]
> Quando você cria um aplicativo de funções no portal do Azure, ele usa a versão 2.x do runtime do Core Tools por padrão. Para que o aplicativo de funções use a versão 1.x do runtime, siga as instruções em [Executar na versão 1.x](functions-versions.md#creating-1x-apps).
> Você não pode alterar a versão do runtime de um aplicativo de funções que tenha funções existentes.

As seguintes opções de publicação se aplicam a ambas as versões, 1.x e 2.x:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Configurações de publicação em local.settings.json do Azure, a solicitação para substituir se a configuração já existe. Se você estiver usando o Emulador de armazenamento Microsoft Azure, primeiro altere a configuração do aplicativo para uma [conexão de armazenamento real](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Suprima o prompt para substituir as configurações do aplicativo quando `--publish-local-settings -i` for usado.|

Somente há suporte para as seguintes opções de publicação na versão 2.x:

| Opção     | Descrição                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Somente publicar as configurações e ignorar o conteúdo. O padrão é solicitado. |
|**`--list-ignored-files`** | Exibe uma lista de arquivos que são ignorados durante a publicação, que é baseada no arquivo .funcignore. |
| **`--list-included-files`** | Exibe uma lista de arquivos que são publicados, que é baseada no arquivo .funcignore. |
| **`--nozip`** | Transforma o padrão de `Run-From-Package` modo desativado. |
| **`--build-native-deps`** | Ignora a geração da pasta .wheels ao publicar aplicativos de função Python. |
| **`--build`**, **`-b`** | Executa a ação de compilação ao implantar em um aplicativo de função Linux. Aceita: `remote` e `local`. |
| **`--additional-packages`** | Lista de pacotes para instalar ao compilar dependências nativas. Por exemplo: `python3-dev libevent-dev`. |
| **`--force`** | Ignorar a verificação de pré-publicação em determinados cenários. |
| **`--csx`** | Publicar um projeto de script (.csx) C#. |
| **`--no-build`** | Não construa funções de biblioteca de classe .NET. |
| **`--dotnet-cli-params`** | Durante a publicação de funções C# (.csproj), o Core Tools chama 'dotnet build --output bin/publish'. Todos os parâmetros passados para isso serão acrescentados à linha de comando. |

### <a name="deploy-custom-container"></a>Implantar contêiner personalizado

O Azure Functions permite que você implante seu projeto de função em um [contêiner Docker personalizado](functions-deployment-technologies.md#docker-container). Para obter mais informações, confira [Criar uma função no Linux usando uma imagem personalizada](functions-create-function-linux-custom-image.md). Os contêineres personalizados precisam ter um Dockerfile. Para criar um aplicativo com um arquivo Docker, `func init`use a opção --dockerfile on .

```
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

A maneira recomendada de monitorar a execução de suas funções é integrando-se com o Azure Application Insights. Você também pode transmitir logs de execução para o seu computador local. Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md).

### <a name="application-insights-integration"></a>Integração de Insights de Aplicativos

A integração do Application Insights deve ser ativada quando você criar seu aplicativo de função no Azure. Se por algum motivo seu aplicativo de função não estiver conectado a uma instância do Application Insights, é fácil fazer essa integração no portal Azure. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Habilite logs de streaming

Você pode visualizar um fluxo de arquivos de log sendo gerados por suas funções em uma sessão de linha de comando em seu computador local. 

#### <a name="native-streaming-logs"></a>Logs de streaming nativos

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Esse tipo de logs de streaming requer que a integração do Application Insights seja ativada para o seu aplicativo de função.   


## <a name="next-steps"></a>Próximas etapas

Saiba como desenvolver, testar e publicar funções do Azure usando as funções do Azure Ferramentas principais [Microsoft aprender módulo](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools é de código aberto e hospedado no [GitHub](https://github.com/azure/azure-functions-cli).  
Para arquivar uma solicitação de bug ou recurso, [abra um problema do GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Ferramentas principais de funções do Azure]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
['AzureWebJobsStorage']: functions-app-settings.md#azurewebjobsstorage
[pacotes de extensão]: functions-bindings-register.md#extension-bundles
