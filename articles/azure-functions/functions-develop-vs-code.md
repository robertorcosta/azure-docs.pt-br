---
title: Desenvolva funções do Azure usando o Visual Studio Code
description: Aprenda a desenvolver e testar funções do Azure usando a extensão Funções do Azure para Visual Studio Code.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277161"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Desenvolva funções do Azure usando o Visual Studio Code

A [extensão Funções do Azure para o Visual Studio Code] permite que você desenvolva funções localmente e as implante no Azure. Se esta for sua primeira experiência com o Azure Functions, você pode aprender mais em [Uma introdução ao Azure Functions](functions-overview.md).

A extensão Funções do Azure oferece esses benefícios:

* Editar, criar e executar funções em seu computador de desenvolvimento local.
* Publicar seu projeto do Azure Functions diretamente no Azure.
* Escreva suas funções em vários idiomas enquanto aproveita os benefícios do Visual Studio Code.

A extensão pode ser usada com os seguintes idiomas, que são suportados pelo tempo de execução funções do Azure a partir da versão 2.x:

* [C# compilado](functions-dotnet-class-library.md)
* [C# script](functions-reference-csharp.md)<sup>*</sup>
* [Javascript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [Powershell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Requer que você [defina o script C# como seu idioma padrão do projeto](#c-script-projects).

Neste artigo, exemplos estão disponíveis atualmente apenas para funções de biblioteca de classe JavaScript (Node.js) e C#.  

Este artigo fornece detalhes sobre como usar a extensão Funções do Azure para desenvolver funções e publicá-las no Azure. Antes de ler este artigo, você deve [criar sua primeira função usando o Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Não misture desenvolvimento local e desenvolvimento de portal para um único aplicativo de função. Ao publicar a partir de um projeto local para um aplicativo de funções, o processo de implantação substitui quaisquer funções que você desenvolveu o portal.

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e executar a [extensão Azure Functions extensão][Azure Functions para Visual Studio Code,]você deve atender a esses requisitos:

* [Visual Studio Code](https://code.visualstudio.com/) instalado em uma das [plataformas suportadas.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Outros recursos que você precisa, como uma conta de armazenamento Do Zure, são criados em sua assinatura quando você [publica usando o Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> Você pode desenvolver funções localmente e publicá-las no Azure sem ter que começar e executá-las localmente. Para executar suas funções localmente, você precisará atender a alguns requisitos adicionais, incluindo um download automático do Azure Functions Core Tools. Para saber mais, consulte [Requisitos adicionais para executar um projeto localmente](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Criar um projeto do Azure Functions

A extensão Funções permite criar um projeto de aplicativo de função, juntamente com sua primeira função. As etapas a seguir mostram como criar uma função acionada por HTTP em um novo projeto Funções. [O gatilho HTTP](functions-bindings-http-webhook.md) é o modelo de gatilho de função mais simples a demonstrar.

1. No **Azure: Funções,** selecione o ícone **Criar função:**

    ![Criar uma função](./media/functions-develop-vs-code/create-function.png)

1. Selecione a pasta para o projeto do aplicativo de função e, em seguida, **Selecione um idioma para o projeto de função**.

1. Se você ainda não instalou as Ferramentas Principais, é solicitado que você **selecione uma versão** das Ferramentas Principais para instalar. Escolha a versão 2.x ou uma versão posterior. 

1. Selecione o modelo de função **de gatilho HTTP,** ou você pode selecionar **Pular por enquanto** para criar um projeto sem uma função. Você sempre pode [adicionar uma função ao seu projeto](#add-a-function-to-your-project) mais tarde.

    ![Escolher o modelo de gatilho HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Digite **HttpExample** para o nome da função e selecione Enter e, em seguida, selecione A autorização **de função.** Este nível de autorização exige que você forneça uma [chave de função](functions-bindings-http-webhook-trigger.md#authorization-keys) quando você chamar o ponto final da função.

    ![Selecionar autorização de função](./media/functions-develop-vs-code/create-function-auth.png)

    Uma função é criada no idioma escolhido e no modelo para uma função acionada por HTTP.

    ![Modelo de função acionada em HTTP no Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Arquivos de projeto gerados

O modelo de projeto cria um projeto no idioma escolhido e instala as dependências necessárias. Para qualquer idioma, o novo projeto tem esses arquivos:

* **host.json**: Permite que você configure o host do Functions. Essas configurações se aplicam quando você está executando funções localmente e quando você está executando-as no Azure. Para obter mais informações, consulte a [referência para host.json](functions-host-json.md).

* **local.settings.json**: Mantém as configurações usadas quando você está executando funções localmente. Essas configurações são usadas apenas quando você está executando funções localmente. Para obter mais informações, consulte [arquivo de configurações locais](#local-settings-file).

    >[!IMPORTANT]
    >Como o arquivo local.settings.json pode conter segredos, você precisa excluí-lo do controle de origem do projeto.

Dependendo do seu idioma, esses outros arquivos são criados:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs arquivo de biblioteca de classe](functions-dotnet-class-library.md#functions-class-library-project) que implementa a função.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

* Um arquivo package.json na pasta raiz.

* Uma pasta HttpExample que contém o [arquivo de definição function.json](functions-reference-node.md#folder-structure) e o [arquivo index.js](functions-reference-node.md#exporting-a-function), um arquivo Node.js que contém o código de função.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

Neste ponto, você pode adicionar vinculações de entrada e saída à sua função [modificando o arquivo function.json](#add-a-function-to-your-project) ou [adicionando um parâmetro a uma função de biblioteca de classe C#.](#add-a-function-to-your-project)

Você também pode [adicionar uma nova função ao seu projeto](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Instalar extensões de associação

Com exceção dos gatilhos HTTP e temporizador, as vinculações são implementadas em pacotes de extensão. Você deve instalar os pacotes de extensão para os gatilhos e amarras que precisam deles. O processo de instalação de extensões de vinculação depende da linguagem do seu projeto.

# <a name="c"></a>[C\#](#tab/csharp)

Execute o comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) in the Terminal window para instalar os pacotes de extensão que você precisa em seu projeto. O comando a seguir instala a extensão Azure Storage, que implementa vinculações para armazenamento Blob, Fila e Tabela.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[Javascript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Adicionar uma função ao projeto

Você pode adicionar uma nova função a um projeto existente usando um dos modelos de gatilho de funções predefinidos. Para adicionar um novo gatilho de função, selecione F1 para abrir a paleta de comando e, em seguida, pesquise e execute o comando **Funções Azure: Criar função**. Siga as instruções para escolher o tipo de gatilho e defina os atributos necessários do gatilho. Se o gatilho precisar de uma chave de acesso ou uma seqüência de conexão para se conectar a um serviço, prepare-o antes de criar o gatilho da função.

Os resultados desta ação dependem da linguagem do seu projeto:

# <a name="c"></a>[C\#](#tab/csharp)

Um novo arquivo c# class library (.cs) é adicionado ao seu projeto.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Uma nova pasta é criada no projeto. A pasta contém um novo arquivo function.json e o novo arquivo de código JavaScript.

---

## <a name="add-input-and-output-bindings"></a>Adicionar ligações de entrada e saída

Você pode expandir sua função adicionando ligações de entrada e saída. O processo de adição de vinculações depende da linguagem do seu projeto. Para saber mais sobre vinculações, consulte [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

Os exemplos a seguir se conectam `outqueue`a uma fila de armazenamento nomeada, `MyStorageConnection` onde a seqüência de conexão para a conta de armazenamento é definida na configuração do aplicativo em local.settings.json.

# <a name="c"></a>[C\#](#tab/csharp)

Atualize o método de função para `Run` adicionar o seguinte parâmetro à definição do método:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Este código exige que `using` você adicione a seguinte declaração:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

O parâmetro `msg` é um tipo `ICollector<T>`, que representa uma coleção de mensagens que são gravadas em uma associação de saída quando a função é concluída. Você adiciona uma ou mais mensagens à coleção. Essas mensagens são enviadas para a fila quando a função é concluída.

Para saber mais, consulte a [documentação de vinculação de saída de armazenamento da Fila.](functions-bindings-storage-queue-output.md)

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Visual Studio Code permite adicionar vinculações ao seu arquivo function.json seguindo um conjunto conveniente de solicitações. Para criar uma vinculação, clique com o botão direito do mouse (Ctrl+clique no macOS) o arquivo **function.json** em sua pasta de função e selecione **Adicionar vinculação:**

![Adicionar uma vinculação a uma função JavaScript existente ](media/functions-develop-vs-code/function-add-binding.png)

A seguir, os prompts de exemplo para definir uma nova vinculação de saída de armazenamento:

| Prompt | Valor | Descrição |
| -------- | ----- | ----------- |
| **Selecionar direção de associação** | `out` | A associação é uma associação de saída. |
| **Selecione vinculação com direção** | `Azure Queue Storage` | A associação é uma associação de fila do Armazenamento do Azure. |
| **O nome usado para identificar essa associação no seu código** | `msg` | Nome que identifica o parâmetro de associação referenciado em seu código. |
| **A fila à qual a mensagem será enviada** | `outqueue` | O nome da fila na qual a associação escreve. Quando o *queueName* não existe, a associação o cria no primeiro uso. |
| **Selecione a configuração de "local.settings.json"** | `MyStorageConnection` | O nome de uma configuração de aplicativo que contém a seqüência de conexão para a conta de armazenamento. A `AzureWebJobsStorage` configuração contém a seqüência de conexão para a conta de armazenamento que você criou com o aplicativo de função. |

Neste exemplo, a seguinte vinculação `bindings` é adicionada à matriz em seu arquivo function.json:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Você também pode adicionar a mesma definição de vinculação diretamente à sua função.json.

Em seu código `msg` de função, a `context`vinculação é acessada a partir do , como neste exemplo:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Para saber mais, consulte a referência [de vinculação de saída de saída de armazenamento da Fila.](functions-bindings-storage-queue-output.md)

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publicar no Azure

Visual Studio Code permite que você publique seu projeto Funções diretamente para o Azure. No processo, você criará um aplicativo de funções e recursos relacionados em sua assinatura do Azure. O aplicativo de funções fornece um contexto de execução para suas funções. O projeto é empacotado e implantado para o novo aplicativo de função em sua assinatura do Azure.

Quando você publica do Visual Studio Code para um novo aplicativo de função no Azure, você é oferecido tanto um aplicativo de função rápida criar caminho e um caminho avançado. 

Quando você publica no Visual Studio Code, você aproveita a tecnologia de [implantação zip.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Criação de aplicativo de função rápida

Quando você escolhe **+ Crie um novo aplicativo de função no Azure...**, a extensão gera automaticamente valores para os recursos do Azure necessários pelo seu aplicativo de função. Esses valores são baseados no nome do aplicativo de função que você escolher. Para um exemplo de uso de padrões para publicar seu projeto em um novo aplicativo de função no Azure, consulte o [artigo visual studio code quickstart](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Se você quiser fornecer nomes explícitos para os recursos criados, você deve escolher o caminho de criação avançado.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Publique um projeto para um novo aplicativo de função no Azure usando opções avançadas

As etapas a seguir publicam seu projeto para um novo aplicativo de função criado com opções avançadas de criação:

1. Na área **Azure: Functions,** selecione o ícone **Implantar para função app.**

    ![Configurações do aplicativo de funções](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Se você não está logado, você é solicitado a **entrar no Azure**. Você também pode **criar uma conta gratuita do Azure**. Depois de fazer login no navegador, volte para visual studio code.

1. Se você tiver várias assinaturas, **selecione uma assinatura** para o aplicativo de função e selecione **+ Crie um novo aplicativo de função no Azure... _Avançado_**. Esta opção _Advanced_ oferece mais controle sobre os recursos que você cria no Azure. 

1. Seguindo as instruções, forneça essas informações:

    | Prompt | Valor | Descrição |
    | ------ | ----- | ----------- |
    | Selecione o aplicativo de função no Azure | Criar novo aplicativo de função no Azure | No próximo prompt, digite um nome globalmente único que identifique seu novo aplicativo de função e, em seguida, selecione Enter. Caracteres válidos para um nome de aplicativo de funções são `a-z`, `0-9` e `-`. |
    | Selecione um sistema operacional | Windows | O aplicativo de função é executado no Windows. |
    | Selecione um plano de hospedagem | Plano de consumo | Uma [hospedagem de plano de consumo](functions-scale.md#consumption-plan) sem servidor é usada. |
    | Selecione um tempo de execução para o seu novo aplicativo | Sua linguagem de projeto | O tempo de execução deve coincidir com o projeto que você está publicando. |
    | Selecione um grupo de recursos para obter novos recursos | Criar novo grupo de recursos | No próximo prompt, digite um `myResourceGroup`nome de grupo de recursos, como , e, em seguida, selecione enter. Você também pode selecionar um grupo de recursos existente. |
    | Selecione uma conta de armazenamento | Criar nova conta de armazenamento | No próximo prompt, digite um nome globalmente único para a nova conta de armazenamento usada pelo aplicativo de função e, em seguida, selecione Enter. Os nomes das contas de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Você também pode selecionar uma conta existente. |
    | Selecione uma localização para novos recursos | region | Selecione um local em uma [região](https://azure.microsoft.com/regions/) próxima a você ou perto de outros serviços que suas funções acessam. |

    Uma notificação é exibida após a criação do aplicativo de função e a aplicação do pacote de implantação. Escolha **Exibir Saída** nessa notificação para exibir a criação e os resultados da implantação, incluindo os recursos do Azure que você criou.

## <a name="republish-project-files"></a>Republicar arquivos de projeto

Quando você configura [a implantação contínua,](functions-continuous-deployment.md)seu aplicativo de função no Azure é atualizado sempre que os arquivos de origem são atualizados no local de origem conectado. Recomendamos a implantação contínua, mas você também pode republicar as atualizações do arquivo do projeto do Visual Studio Code.

> [!IMPORTANT]
> Publicar em um aplicativo de funções existente substitui o conteúdo desse aplicativo no Azure.

1. Em Visual Studio Code, selecione F1 para abrir a paleta de comando. Na paleta de comando, procure e selecione **Funções do Azure: Implante para funcionar o aplicativo**.

1. Se você não está logado, você é solicitado a **entrar no Azure**. Depois de fazer login no navegador, volte para o Visual Studio Code. Se você tiver várias assinaturas, **selecione uma assinatura** que contenha seu aplicativo de função.

1. Selecione seu aplicativo de função existente no Azure. Quando você for avisado sobre a substituição de todos os arquivos no aplicativo de função, selecione **Implantar** para reconhecer o aviso e continuar.

O projeto é reconstruído, reembalado e carregado no Azure. O projeto existente é substituído pelo novo pacote, e o aplicativo de função é reiniciado.

## <a name="get-the-url-of-the-deployed-function"></a>Obtenha a URL da função implantada

Para chamar uma função acionada pelo HTTP, você precisa da URL da função quando ela for implantada no aplicativo de função. Esta URL inclui todas as [chaves de função necessárias](functions-bindings-http-webhook-trigger.md#authorization-keys). Você pode usar a extensão para obter esses URLs para suas funções implantadas.

1. Selecione F1 para abrir a paleta de comando e, em seguida, procure e execute o comando **Funções Azure: URL da função de cópia**.

1. Siga as instruções para selecionar seu aplicativo de função no Azure e, em seguida, o gatilho HTTP específico que você deseja invocar.

A URL da função é copiada para a área `code` de transferência, juntamente com quaisquer teclas necessárias passadas pelo parâmetro de consulta. Use uma ferramenta HTTP para enviar solicitações POST ou um navegador para solicitações GET para a função remota.  

## <a name="run-functions-locally"></a>Executar funções localmente

A extensão Funções do Azure permite executar um projeto Funções em seu computador de desenvolvimento local. O tempo de execução local é o mesmo tempo de execução que hospeda seu aplicativo de função no Azure. As configurações locais são lidas a partir do [arquivo local.settings.json](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Requisitos adicionais para executar um projeto localmente

Para executar seu projeto de funções localmente, você deve atender a esses requisitos adicionais:

* Instale a versão 2.x ou posterior do [Azure Functions Core Tools](functions-run-local.md#v2). O pacote Core Tools é baixado e instalado automaticamente quando você inicia o projeto localmente. O Core Tools inclui todo o tempo de execução das funções do Azure, então o download e a instalação podem levar algum tempo.

* Instale os requisitos específicos para a linguagem de programação escolhida:

    | Idioma | Requisito |
    | -------- | --------- |
    | **C #** | [Extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[Ferramentas da CLI do .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Depurador para extensão Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 ou posterior](https://maven.apache.org/) |
    | **Javascript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Extensão Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) recomendado|

    <sup>*</sup>Lts ativo e versões LTS de manutenção (8.11.1 e 10.14.1 recomendado).

### <a name="configure-the-project-to-run-locally"></a>Configure o projeto para ser executado localmente

O tempo de execução Functions usa uma conta de armazenamento Azure internamente para todos os tipos de gatilho que não seja HTTP e webhooks. Portanto, você precisa definir a chave **Values.AzureWebJobsStorage** para uma seqüência de conexão de conta Azure Storage válida.

Esta seção usa a [extensão Azure Storage para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) com o [Azure Storage Explorer](https://storageexplorer.com/) para conectar e recuperar a seqüência de conexão de armazenamento.

Para definir a cadeia de conexão da conta de armazenamento:

1. No Visual Studio, abra **o Cloud Explorer,** **expanda a conta** > de armazenamento**sua conta de armazenamento**e selecione **Propriedades** e copie o valor **da seqüência de conexões primárias.**

2. Em seu projeto, abra o arquivo local.settings.json e defina o valor da chave **AzureWebJobsStorage** na cadeia de conexão que você copiou.

3. Repita a etapa anterior para adicionar as chaves exclusivas para a matriz de **Valores** para todas as outras conexões necessárias para as suas funções.

Para obter mais informações, consulte [arquivo de configurações locais](#local-settings-file).

### <a name="debugging-functions-locally"></a>Funções de depuração localmente  

Para depurar suas funções, selecione F5. Se você ainda não baixou [as ferramentas principais]do Core Tools[Azure Functions Core Tools,]você é solicitado a fazê-lo. Quando as Ferramentas principais são instaladas e em execução, a saída é mostrada no Terminal. Isso é o mesmo `func host start` que executar o comando Core Tools a partir do Terminal, mas com tarefas adicionais de compilação e um depurador conectado.  

Quando o projeto estiver em execução, você pode ativar suas funções como faria quando o projeto for implantado no Azure. Quando o projeto está sendo executado no modo de depuração, os pontos de interrupção são atingidos no Visual Studio Code, como esperado.

A URL de solicitação de gatilhos HTTP é exibida na saída do Terminal. As teclas de função para gatilhos HTTP não são usadas quando um projeto está sendo executado localmente. Para saber mais informações, consulte [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md).  

Para saber mais, consulte [Trabalhe com as principais ferramentas do Azure, ferramentas][principais do Azure].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Por padrão, essas configurações não são migradas automaticamente quando o projeto é publicado no Azure. Após o término da publicação, você tem a opção de publicar configurações de local.settings.json para seu aplicativo de função no Azure. Para saber mais, consulte [Publicar configurações do aplicativo](#publish-application-settings).

Os valores em **ConnectionStrings** nunca são publicados.

Os valores de configurações do aplicativo de função também podem ser lidos em seu código como variáveis de ambiente. Para obter mais informações, consulte as seções de variáveis de ambiente desses artigos de referência específicos para o idioma:

* [C# pré-compilado](functions-dotnet-class-library.md#environment-variables)
* [Script do C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Configurações do aplicativo no Azure

As configurações no arquivo local.settings.json em seu projeto devem ser as mesmas que as configurações do aplicativo no aplicativo de função no Azure. Quaisquer configurações adicionadas ao local.settings.json também devem ser adicionadas ao aplicativo de função no Azure. Essas configurações não são enviadas automaticamente quando você publica o projeto. Da mesma forma, todas as configurações que você criar em seu aplicativo de função [no portal](functions-how-to-use-azure-function-app-settings.md#settings) devem ser baixadas para o seu projeto local.

### <a name="publish-application-settings"></a>Publicar configurações do aplicativo

A maneira mais fácil de publicar as configurações necessárias para o seu aplicativo de função no Azure é usar o link **'Upload settings'** que aparece após a publicação do seu projeto:

![Carregar configurações do aplicativo](./media/functions-develop-vs-code/upload-app-settings.png)

Você também pode publicar configurações usando o **comando Funções Do Azure: Carregar configuração local** na paleta de comando. Você pode adicionar configurações individuais às configurações do aplicativo no Azure usando o **comando Azure Functions: Add New Setting..**

> [!TIP]
> Certifique-se de salvar seu arquivo local.settings.json antes de publicá-lo.

Se o arquivo local for criptografado, ele será descriptografado, publicado e criptografado novamente. Se houver configurações que tenham valores conflitantes nos dois locais, você é solicitado a escolher como proceder.

Exibir as configurações existentes do aplicativo no **Azure: Área de funções** expandindo sua assinatura, seu aplicativo de função e **configurações de aplicativos**.

![Ver configurações do aplicativo de função em Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Baixar configurações do Azure

Se você criou as configurações do aplicativo no Azure, você pode baixá-las em seu arquivo local.settings.json usando o comando **Funções do Azure: Baixe configurações remotas.**

Como no upload, se o arquivo local for criptografado, ele será descriptografado, atualizado e criptografado novamente. Se houver configurações que tenham valores conflitantes nos dois locais, você é solicitado a escolher como proceder.

## <a name="monitoring-functions"></a>Funções de monitoramento

Quando você [executa funções localmente,](#run-functions-locally)os dados de registro são transmitidos para o console Terminal. Você também pode obter dados de log quando seu projeto Functions estiver sendo executado em um aplicativo de função no Azure. Você pode se conectar a logs de streaming no Azure para ver dados de log quase em tempo real ou pode habilitar o Application Insights para uma compreensão mais completa de como seu aplicativo de função está se comportando.

### <a name="streaming-logs"></a>Logs de streaming

Quando você está desenvolvendo um aplicativo, muitas vezes é útil ver o registro de informações em tempo quase real. Você pode visualizar um fluxo de arquivos de log sendo gerados por suas funções. Esta saída é um exemplo de logs de streaming para uma solicitação a uma função acionada pelo HTTP:

![Saída de logs de streaming para gatilho HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Para saber mais, consulte [Logs de streaming](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Os logs de streaming suportam apenas uma única instância do host Funções. Quando sua função é dimensionada para várias instâncias, os dados de outras instâncias não são mostrados no fluxo de log. [O Live Metrics Stream](../azure-monitor/app/live-stream.md) no Application Insights suporta várias instâncias. Embora também em tempo quase real, a análise de streaming é baseada em [dados amostrados](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Recomendamos que você monitore a execução de suas funções integrando seu aplicativo de função com o Application Insights. Quando você cria um aplicativo de função no portal Azure, essa integração ocorre por padrão. Quando você cria seu aplicativo de função durante a publicação do Visual Studio, você mesmo precisa integrar o Application Insights.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>Projetos\# de script C

Por padrão, todos os projetos C# são criados como [projetos de biblioteca de classe compilados C#](functions-dotnet-class-library.md). Se você preferir trabalhar com projetos de script C# em vez disso, você deve selecionar o script C# como o idioma padrão nas configurações de extensão Funções do Azure:

1. Selecione > **Configurações de preferências de** >  **File****arquivo**.

1. Ir para >  **extensões de configurações do usuário****Funções** > **do Azure**.

1. Selecione **C#Script** da **função Azure: Linguagem do projeto**.

Depois de concluir essas etapas, as chamadas `--csx` feitas para as Ferramentas Principais subjacentes incluem a opção, que gera e publica arquivos de projeto c# script (.csx). Quando você tem essa linguagem padrão especificada, todos os projetos que você cria padrão para projetos de script C#. Você não é solicitado a escolher um idioma de projeto quando um padrão é definido. Para criar projetos em outros idiomas, você deve alterar essa configuração ou removê-la do arquivo user settings.json. Depois de remover essa configuração, você é novamente solicitado a escolher seu idioma quando criar um projeto.

## <a name="command-palette-reference"></a>Referência da paleta de comando

A extensão Funções do Azure fornece uma interface gráfica útil na área para interagir com seus aplicativos de função no Azure. A mesma funcionalidade também está disponível como comandos na paleta de comandos (F1). Estes comandos Azure Functions estão disponíveis:

|Comando Funções do Azure  | Descrição  |
|---------|---------|
|**Adicionar novas configurações**  |  Cria uma nova configuração de aplicativo no Azure. Para saber mais, consulte [Publicar configurações do aplicativo](#publish-application-settings). Você também pode precisar [baixar esta configuração para suas configurações locais](#download-settings-from-azure). |
| **Configurar a fonte de implantação** | Conecta seu aplicativo de função no Azure a um repositório git local. Para saber mais, consulte [Implantação contínua para funções do Azure](functions-continuous-deployment.md). |
| **Conecte-se ao Repositório GitHub** | Conecta seu aplicativo de função a um repositório do GitHub. |
| **URL da função de cópia** | Obtém a URL remota de uma função acionada por HTTP que está sendo executado no Azure. Para saber mais, consulte [Obter a URL da função implantada](#get-the-url-of-the-deployed-function). |
| **Criar aplicativo de função no Azure** | Cria um novo aplicativo de função em sua assinatura no Azure. Para saber mais, consulte a seção sobre como [publicar em um novo aplicativo de função no Azure](#publish-to-azure).        |
| **Configurações de descriptografia** | Descriptografa [configurações locais](#local-settings-file) que foram criptografadas pelo **Azure Functions: Encrypt SettingSettings**.  |
| **Excluir aplicativo de função** | Remove um aplicativo de função de sua assinatura no Azure. Quando não há outros aplicativos no plano de Serviço de Aplicativo, você tem a opção de excluir isso também. Outros recursos, como contas de armazenamento e grupos de recursos, não são excluídos. Para remover todos os recursos, você deve [excluir o grupo de recursos](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Seu projeto local não foi afetado. |
|**Função de exclusão**  | Remove uma função existente de um aplicativo de função no Azure. Como essa exclusão não afeta seu projeto local, considere remover a função localmente e, em seguida, [republicar seu projeto](#republish-project-files). |
| **Excluir proxy** | Remove um proxy Funções do Azure do seu aplicativo de função no Azure. Para saber mais sobre proxies, consulte [Trabalhe com Proxies de Funções Azure](functions-proxies.md). |
| **Excluir configuração** | Exclui uma configuração de aplicativo de função no Azure. Essa exclusão não afeta as configurações do arquivo local.settings.json. |
| **Desconectar-se do Repo**  | Remove a conexão [de implantação contínua](functions-continuous-deployment.md) entre um aplicativo de função no Azure e um repositório de controle de origem. |
| **Baixar configurações remotas** | Baixa as configurações do aplicativo de função escolhido no Azure em seu arquivo local.settings.json. Se o arquivo local for criptografado, ele será descriptografado, atualizado e criptografado novamente. Se houver configurações que tenham valores conflitantes nos dois locais, você é solicitado a escolher como proceder. Certifique-se de salvar alterações no arquivo local.settings.json antes de executar este comando. |
| **Editar configurações** | Altera o valor de uma configuração de aplicativo de função existente no Azure. Este comando não afeta as configurações do arquivo local.settings.json.  |
| **Criptografar configurações** | Criptografa itens individuais `Values` na matriz nas [configurações locais](#local-settings-file). Neste arquivo, `IsEncrypted` também está `true`definido para , o que especifica que o tempo de execução local irá descriptografar as configurações antes de usá-las. Criptografe as configurações locais para reduzir o risco de vazamento de informações valiosas. No Azure, as configurações do aplicativo são sempre armazenadas criptografadas. |
| **Executar função agora** | Inicia manualmente uma [função acionada pelo temporizador](functions-bindings-timer.md) no Azure. Este comando é usado para testes. Para saber mais sobre como ativar funções não-HTTP no Azure, consulte [Execute manualmente uma função não acionada pelo HTTP](functions-manually-run-non-http.md). |
| **Inicializar projeto para uso com código VS** | Adiciona os arquivos de projeto visual studio code necessários a um projeto de Funções existente. Use este comando para trabalhar com um projeto que você criou usando ferramentas principais. |
| **Instalar ou atualizar as ferramentas principais das funções do Azure** | Instala ou atualiza [as funções do Azure Core Tools], que é usado para executar funções localmente. |
| **Reimplantar**  | Permite reimplantar arquivos de projeto de um repositório Git conectado para uma implantação específica no Azure. Para republicar atualizações locais do Visual Studio Code, [republique seu projeto](#republish-project-files). |
| **Renomear configurações** | Altera o nome-chave de uma configuração de aplicativo de função existente no Azure. Este comando não afeta as configurações do arquivo local.settings.json. Depois de renomear as configurações no Azure, você deve [baixar essas alterações no projeto local](#download-settings-from-azure). |
| **Reiniciar** | Reinicia o aplicativo de função no Azure. A implantação de atualizações também reinicia o aplicativo de função. |
| **Definir AzureWebJobsArmazenamento**| Define o valor `AzureWebJobsStorage` da configuração do aplicativo. Esta configuração é exigida pelas funções do Azure. É definido quando um aplicativo de função é criado no Azure. |
| **Início** | Inicia um aplicativo de função parada no Azure. |
| **Iniciar logs de streaming** | Inicia os logs de streaming para o aplicativo de função no Azure. Use logs de streaming durante a solução remota de problemas no Azure se você precisar ver informações de registro em tempo quase real. Para saber mais, consulte [Logs de streaming](#streaming-logs). |
| **Parar** | Interrompe um aplicativo de função que está sendo executado no Azure. |
| **Parar logs de streaming** | Interrompe os logs de streaming para o aplicativo de função no Azure. |
| **Alternar como configuração de ranhura** | Quando ativado, garante que uma configuração de aplicativo persista para um determinado slot de implantação. |
| **Desinstale as ferramentas principais das funções do Azure** | Remove as funções do Azure Core Tools, que é exigida pela extensão. |
| **Carregar configurações locais** | Faz upload das configurações do arquivo local.settings.json para o aplicativo de função escolhido no Azure. Se o arquivo local for criptografado, ele será descriptografado, carregado e criptografado novamente. Se houver configurações que tenham valores conflitantes nos dois locais, você é solicitado a escolher como proceder. Certifique-se de salvar alterações no arquivo local.settings.json antes de executar este comando. |
| **Exibir comprometer no GitHub** | Mostra-lhe o mais recente compromisso em uma implantação específica quando seu aplicativo de função está conectado a um repositório. |
| **Exibir logs de implantação** | Mostra os logs para uma implantação específica para o aplicativo de função no Azure. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as ferramentas principais de funções do Azure, consulte [Trabalhe com ferramentas principais de funções do Azure](functions-run-local.md).

Para saber mais sobre como desenvolver funções como bibliotecas de classes do .NET, consulte [Referência do desenvolvedor de C# do Azure Functions](functions-dotnet-class-library.md). Este artigo também fornece links para exemplos de como usar atributos para declarar os vários tipos de vinculações suportadas pelas Funções Azure.

[Extensão Azure Functions para Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Ferramentas principais de funções do Azure]: functions-run-local.md
