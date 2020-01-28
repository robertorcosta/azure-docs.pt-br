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
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 786efcb712557da4363384c9d05c33f4f16d6731
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122725"
---
[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Pacote (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Prerequisites

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js](https://nodejs.org) e do NPM.

## <a name="using-this-quickstart"></a>Como usar este início rápido


Há várias etapas para usar este início rápido:

* No portal do Azure, crie um recurso do Personalizador
* No portal do Azure, para o recurso Personalizador, na página **Configuração**, altere a frequência de atualização do modelo para um intervalo muito curto
* Em um editor de códigos, crie um arquivo de código e edite-o
* Na linha de comando ou no terminal, instale o SDK usando a linha de comando
* Na linha de comando ou no terminal, execute o arquivo de código

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele.

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init -y` para criar um arquivo `package.json`.

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Instalar a biblioteca do Node.js para o Personalizador

Instale a biblioteca de clientes do Personalizador para Node.js com o seguinte comando:

```console
npm install @azure/cognitiveservices-personalizer --save
```

Instale os pacotes npm restantes para este início rápido:

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="object-model"></a>Modelo de objeto

O cliente do Personalizador é um objeto [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) que se autentica no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave.

Para solicitar o melhor item do conteúdo, crie uma [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest) e, em seguida, transmita-a para o método [client.Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-). O método Rank retornará uma RankResponse.

Para enviar uma recompensa ao Personalizador, crie uma [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest) e, em seguida, transmita-a para método [Reward](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) na classe Events.

Determinar a recompensa neste início rápido é trivial. Em um sistema de produção, a determinação do que afeta a [pontuação de recompensa](../concept-rewards.md) e em que medida pode ser um processo complexo; você pode decidir alterá-lo ao longo do tempo. Isso deve ser uma das principais decisões de design na arquitetura do Personalizador.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como fazer o seguinte com a biblioteca de clientes do Personalizador para Node.js:

* [Criar um cliente do Personalizador](#create-a-personalizer-client)
* [API de Classificação](#request-the-best-action)
* [API de Recompensa](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Crie um aplicativo Node.js em seu IDE ou editor preferido chamado `sample.js`.

## <a name="add-the-dependencies"></a>Adicionar as dependências

Abra o arquivo **sample.js** em seu IDE ou editor preferido. Adicione o seguinte `requires` para adicionar os pacotes npm:

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Adicionar informações de recursos do Personalizador

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso extraídos das variáveis de ambiente, chamadas `PERSONALIZER_KEY` e `PERSONALIZER_ENDPOINT`. Se você tiver criado as variáveis de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável. Os métodos serão criados posteriormente neste início rápido.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Criar um cliente do Personalizador

Em seguida, crie um método para retornar um cliente do Personalizador. O parâmetro para o método é o `PERSONALIZER_RESOURCE_ENDPOINT` e a ApiKey é o `PERSONALIZER_RESOURCE_KEY`.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>Obter opções de conteúdo representadas como ações

As ações representam as opções de conteúdo entre as quais você deseja que o Personalizador selecione o melhor item de conteúdo. Adicione os métodos a seguir à classe Program para representar o conjunto de ações e os recursos delas.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizado

O loop de aprendizado do Personalizador é um ciclo de chamadas de [Classificação](#request-the-best-action) e [Recompensa](#send-a-reward). Neste início rápido, cada chamada de Classificação, para personalizar o conteúdo, é seguida por uma chamada de Recompensa para informar ao Personalizador o nível de desempenho do serviço.

O código a seguir executa um loop em um ciclo que inclui perguntas sobre as preferências do usuário na linha de comando, envio dessas informações ao Personalizador para seleção da melhor ação, apresentação da seleção ao cliente para escolha de uma opção na lista e, em seguida, envio de uma recompensa ao Personalizador, sinalizando o nível de desempenho do serviço na seleção.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Examine mais detalhadamente as chamadas de classificação e de recompensa nas seções a seguir.

Adicione os seguintes métodos, que [obtêm as opções de conteúdo](#get-content-choices-represented-as-actions), antes de executar o arquivo de código:

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>Solicitar a melhor ação

Para concluir a solicitação de Classificação, o programa pergunta as preferências do usuário para criar as opções de conteúdo. O processo pode criar o conteúdo a ser excluído das ações, mostrado como `excludeActions`. A solicitação de Classificação precisa das [ações](../concepts-features.md#actions-represent-a-list-of-options) e dos recursos delas, dos recursos do currentContext, das excludeActions e de uma ID exclusiva do evento de classificação para receber a resposta classificada.

Este início rápido tem recursos de contexto simples da hora do dia e da preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e recursos](../concepts-features.md) pode ser uma questão não trivial.

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa


Para obter a pontuação de recompensa a ser enviada na solicitação de Recompensa, o programa obtém a seleção do usuário na linha de comando, atribui um valor numérico a cada seleção e, em seguida, envia uma ID exclusiva do evento e a pontuação de recompensa como o valor numérico para a API de Recompensa.

Este início rápido atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [Recompensa](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo com o Node.js no diretório do aplicativo.

```console
node sample.js
```

![O programa de Início Rápido faz algumas perguntas para coletar as preferências do usuário, conhecidas como recursos e, em seguida, fornece a ação principal.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
