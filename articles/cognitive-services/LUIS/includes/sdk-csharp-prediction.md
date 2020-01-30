---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/14/2020
ms.author: diberry
ms.openlocfilehash: fa0537b4009cf38b95cedff93850e5dac61baf34
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772438"
---
Use a biblioteca de clientes de previsão de LUIS (Reconhecimento Vocal) para .NET com o objetivo de:

* Obter previsão por slot

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Pacote do runtime de previsão (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [Exemplos de C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Prerequisites

* Conta do portal do LUIS (Reconhecimento Vocal) – [Crie uma gratuitamente](https://www.luis.ai)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

Procurando mais documentação?

 * [Documentação de referência do SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>Configurando

### <a name="create-an-environment-variable"></a>Criar uma variável de ambiente

Usando sua chave e o nome de seu recurso, crie duas variáveis de ambiente para autenticação:

* `LUIS_PREDICTION_KEY` – a chave de recurso para autenticar as solicitações.
* `LUIS_ENDPOINT_NAME` – o nome do recurso associado à sua chave.

Use as instruções para seu sistema operacional.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

Depois de adicionar a variável de ambiente, reinicie a janela do console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela de console para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite seu `.bash_profile` e adicione a variável de ambiente:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela de console para que as alterações entrem em vigor.

---

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Crie um novo aplicativo .NET Core em seu IDE ou editor preferido.

1. Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `new` para criar um novo aplicativo do console com o nome `language-understanding-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: `Program.cs`.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Altere o diretório para a pasta do aplicativo recém-criado.

1. É possível criar o aplicativo com:

    ```dotnetcli
    dotnet build
    ```

    A saída de compilação não deve conter nenhum aviso ou erro.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-sdk"></a>Instalar o SDK

Dentro do diretório do aplicativo, instale a biblioteca de clientes de runtime de previsão do LUIS (Reconhecimento Vocal) para .NET com o seguinte comando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.

## <a name="object-model"></a>Modelo de objeto

O cliente de runtime de previsão do LUIS (Reconhecimento Vocal) é um objeto [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) que se autentica no Azure, que contém sua chave de recurso.

Depois que o cliente for criado, use-o para acessar a funcionalidade, incluindo:

* Previsão por [slot de produto ou preparo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Previsão por [versão](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como fazer o seguinte com a biblioteca de clientes de runtime de previsão do LUIS (Reconhecimento Vocal) para .NET:

* [Previsão por slot](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo *Program.cs* no IDE ou no editor de sua preferência. Substitua o código `using` existente pelas seguintes diretivas de `using`:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie variáveis para a chave, o nome e a ID do aplicativo:

    variáveis para gerenciar sua chave de previsão extraída de uma variável de ambiente chamada `LUIS_PREDICTION_KEY`. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável. Os métodos serão criados posteriormente.

    Crie uma variável para conter o nome do recurso `LUIS_ENDPOINT_NAME`.

    Crie uma variável para a ID do aplicativo como uma variável de ambiente chamada `LUIS_APP_ID`. Defina a variável de ambiente para o aplicativo de IoT público:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Crie um objeto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave e use-o com o ponto de extremidade para criar um objeto [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet).

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Obter uma previsão do runtime

Adicione o método a seguir para criar a solicitação para o runtime de previsão.

O enunciado do usuário faz parte do objeto [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet).

O método **GetSlotPredictionAsync** precisa de vários parâmetros, como a ID do aplicativo, o nome do slot e o objeto de solicitação de previsão, para atender à solicitação. As outras opções, como detalhada, mostram todas as intenções e log são opcionais.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Código principal para a previsão

Use o método principal a seguir para vincular as variáveis e os métodos para obter a previsão.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `dotnet run` do seu próprio diretório de aplicativo.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Ao concluir suas previsões, limpe o trabalho deste guia de início rápido excluindo o arquivo program.cs e seus subdiretórios.
