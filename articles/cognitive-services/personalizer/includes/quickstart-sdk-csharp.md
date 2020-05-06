---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: 0b09f15eb05a94bb62f4484ac362467fbdb0af8b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188835"
---
[Documentação de referência](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Prerequisites

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Como usar este início rápido

Há várias etapas para usar este início rápido:

* No portal do Azure, crie um recurso do Personalizador
* No portal do Azure, para o recurso Personalizador, na página **Configuração**, altere a frequência de atualização do modelo para um intervalo muito curto
* Em um editor de códigos, crie um arquivo de código e edite-o
* Na linha de comando ou no terminal, instale o SDK usando a linha de comando
* Na linha de comando ou no terminal, execute o arquivo de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Crie um novo aplicativo .NET Core em seu IDE ou editor preferido.

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `new` para criar um novo aplicativo do console com o nome `personalizer-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: `Program.cs`.

```console
dotnet new console -n personalizer-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
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

## <a name="install-the-sdk"></a>Instalar o SDK

Dentro do diretório do aplicativo, instale a biblioteca de clientes do Personalizador para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.

## <a name="object-model"></a>Modelo de objeto

O cliente do Personalizador é um objeto [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) que se autentica no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave.

Para solicitar o melhor item do conteúdo, crie uma [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview) e, em seguida, transmita-a para o método [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview). O método Rank retornará uma RankResponse.

Para enviar uma pontuação de recompensa ao Personalizador, crie uma [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview) e, em seguida, transmita-a para método [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview).

Determinar a pontuação de recompensa neste início rápido é trivial. Em um sistema de produção, a determinação do que afeta a [pontuação de recompensa](../concept-rewards.md) e em que medida pode ser um processo complexo; você pode decidir alterá-lo ao longo do tempo. Essa decisão de design deve ser uma das principais decisões na arquitetura do Personalizador.

## <a name="code-examples"></a>Exemplos de código

Esses snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes do Personalizador para .NET:

* [Criar um cliente do Personalizador](#create-a-personalizer-client)
* [API de Classificação](#request-the-best-action)
* [API de Recompensa](#send-a-reward)

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo **Program.cs** no IDE ou no editor de sua preferência. Substitua o código `using` existente pelas seguintes diretivas de `using`:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recursos do Personalizador

Na classe **Program**, crie variáveis para a chave e o ponto de extremidade do Azure extraídos das variáveis de ambiente, denominadas `PERSONALIZER_RESOURCE_KEY` e `PERSONALIZER_RESOURCE_ENDPOINT`. Se você tiver criado as variáveis de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável. Os métodos serão criados posteriormente neste início rápido.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente do Personalizador

Em seguida, crie um método para retornar um cliente do Personalizador. O parâmetro para o método é o `PERSONALIZER_RESOURCE_ENDPOINT` e a ApiKey é o `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Obter itens de alimentos como ações classificáveis

As ações representam as opções de conteúdo entre as quais você deseja que o Personalizador selecione o melhor item de conteúdo. Adicione os métodos a seguir à classe Program para representar o conjunto de ações e os recursos. 

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Obter preferências do usuário para o contexto

Adicione os seguintes métodos à classe Program para obter a entrada de um usuário da linha de comando para a hora do dia e a preferência de alimentos atual. Eles serão usados como recursos de contexto.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Os dois métodos usam o método `GetKey` para ler a seleção do usuário na linha de comando.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizado

O loop de aprendizado do Personalizador é um ciclo de chamadas de [Classificação](#request-the-best-action) e [Recompensa](#send-a-reward). Neste início rápido, cada chamada de Classificação, para personalizar o conteúdo, é seguida por uma chamada de Recompensa para informar ao Personalizador o nível de desempenho do serviço.

O código a seguir executa um loop em um ciclo que inclui perguntas sobre as preferências do usuário na linha de comando, envio dessas informações ao Personalizador para seleção da melhor ação, apresentação da seleção ao cliente para escolha de uma opção na lista e, em seguida, envio de uma pontuação de recompensa ao Personalizador, sinalizando o nível de desempenho do serviço na seleção.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Adicione os seguintes métodos, que [obtêm as opções de conteúdo](#get-food-items-as-rankable-actions), antes de executar o arquivo de código:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Solicitar a melhor ação

Para concluir a solicitação de Classificação, o programa pergunta as preferências do usuário para criar um `currentContent` das opções de conteúdo. O processo pode criar o conteúdo a ser excluído das ações, mostrado como `excludeActions`. A solicitação de Classificação precisa das ações e dos recursos delas, dos recursos do currentContext, das excludeActions e de uma ID exclusiva do evento para receber a resposta.

Este início rápido tem recursos de contexto simples da hora do dia e da preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e recursos](../concepts-features.md) pode ser uma questão não trivial.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa

Para obter a pontuação de recompensa a ser enviada na solicitação de Recompensa, o programa obtém a seleção do usuário na linha de comando, atribui um valor numérico a cada seleção e, em seguida, envia uma ID exclusiva do evento e a pontuação de recompensa como o valor numérico para a API de Recompensa.

Este início rápido atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [Recompensa](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo em seu próprio diretório com o comando `run` de dotnet.

```console
dotnet run
```

![O programa de Início Rápido faz algumas perguntas para coletar as preferências do usuário, conhecidas como recursos e, em seguida, fornece a ação principal.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

O [código-fonte deste início rápido](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) está disponível no repositório GitHub de exemplos do Personalizador.
