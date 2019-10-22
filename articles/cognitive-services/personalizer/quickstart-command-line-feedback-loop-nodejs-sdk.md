---
title: 'Início Rápido: Biblioteca de clientes do Personalizador para Node.js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Introdução à biblioteca de clientes do Personalizador para Node.js usando um loop de aprendizado.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: ee647668e8b5826706e8d9bb8a82acaf53fd3d8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515211"
---
# <a name="quickstart-personalizer-client-library-for-nodejs"></a>Início Rápido: Biblioteca de clientes do Personalizador para Node.js

Exiba um conteúdo personalizado neste início rápido do Node.js com o serviço Personalizador.

Introdução à biblioteca de clientes do Personalizador para Node.js. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas.

 * Classificar uma lista de ações para personalização.
 * Pontuação de recompensa do relatório indicando êxito da ação de classificação mais alta.

[Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [Pacote (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [Amostras](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [Node.js](https://nodejs.org) e do NPM.

## <a name="using-this-quickstart"></a>Como usar este início rápido


Há várias etapas para usar este início rápido:

* No portal do Azure, crie um recurso do Personalizador
* No portal do Azure, para o recurso do Personalizador, na página **Configurações**, altere a frequência de atualização do modelo
* Em um editor de códigos, crie um arquivo de código e edite-o
* Na linha de comando ou no terminal, instale o SDK usando a linha de comando
* Na linha de comando ou no terminal, execute o arquivo de código


## <a name="create-a-personalizer-azure-resource"></a>Criar um recurso do Azure do Personalizador

Os Serviços Cognitivos do Azure são representados por recursos do Azure que você assina. Crie um recurso para o Personalizador usando o [portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou a [CLI do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) em seu computador local. Você também pode:

* Obter uma [chave de avaliação](https://azure.microsoft.com/try/cognitive-services) válida por 7 dias gratuitamente. Depois de se inscrever, ela estará disponível no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Exibir esse recurso no [portal do Azure](https://portal.azure.com/).

Depois de obter uma chave do recurso ou da assinatura de avaliação, crie duas [variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_KEY` para a chave do recurso.
* `PERSONALIZER_ENDPOINT` para o ponto de extremidade do recurso.

No portal do Azure, os valores da chave e do ponto de extremidade estão disponíveis na página **Início Rápido**.


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

## <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No portal do Azure, no recurso do Personalizador na página **Configurações**, altere a **Frequência de atualização do modelo** para 10 segundos. Isso treinará o serviço rapidamente, permitindo que você veja como a ação superior é alterada a cada iteração.

![Alterar a frequência de atualização do modelo](./media/settings/configure-model-update-frequency-settings.png)

Quando o loop do Personalizador é instanciado pela primeira vez, não há modelo, pois não houve nenhuma chamada à API de Recompensa com base na qual treinar. Chamadas de classificação retornarão probabilidades iguais para cada item. Seu aplicativo ainda deve classificar sempre o conteúdo usando a saída de RewardActionId.

## <a name="object-model"></a>Modelo de objeto

O cliente do Personalizador é um objeto PersonalizerClient que se autentica no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave.

Para solicitar uma classificação do conteúdo, crie um RankRequest e, em seguida, passe-o para o método client.Rank. O método Rank retorna um RankResponse, que contém o conteúdo classificado. 

Para enviar uma recompensa ao Personalizador, crie um RewardRequest e, em seguida, passe-o para método client.Reward. 

Determinar a recompensa neste início rápido é trivial. Em um sistema de produção, a determinação do que afeta a [pontuação de recompensa](concept-rewards.md) e em que medida pode ser um processo complexo; você pode decidir alterá-lo ao longo do tempo. Isso deve ser uma das principais decisões de design na arquitetura do Personalizador. 

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como fazer o seguinte com a biblioteca de clientes do Personalizador para Node.js:

* [Criar um cliente do Personalizador](#create-a-personalizer-client)
* [Solicitar uma classificação](#request-a-rank)
* [Enviar uma recompensa](#send-a-reward)

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

As ações representam as opções de conteúdo que você deseja que o Personalizador classifique. Adicione os seguintes métodos à classe Program para obter a entrada de um usuário da linha de comando para a hora do dia e a preferência de alimentos atual.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizado

O loop de aprendizado do Personalizador é um ciclo de chamadas de [classificação](#request-a-rank) e de [recompensa](#send-a-reward). Neste início rápido, cada chamada de classificação, para personalizar o conteúdo, é seguida por uma chamada de recompensa para informar ao Personalizador quão bem o serviço classificou o conteúdo. 

O código de loop a seguir executa um loop em um ciclo que inclui perguntas sobre as preferências do usuário na linha de comando, envio dessas informações ao Personalizador para classificação, apresentação da seleção classificada ao cliente para escolher uma opção da lista e, em seguida, envio de uma recompensa ao Personalizador, sinalizando o nível de qualidade do serviço na classificação da seleção.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

Examine mais detalhadamente as chamadas de classificação e de recompensa nas seções a seguir.

Adicione os seguintes métodos, que [obtêm as opções de conteúdo](#get-content-choices-represented-as-actions), antes de executar o arquivo de código:

* getActionsList
* getContextFeaturesList

## <a name="request-a-rank"></a>Solicitar uma classificação

Para concluir a solicitação de classificação, o programa pergunta as preferências do usuário para criar as opções de conteúdo. O processo pode criar conteúdo a ser excluído da classificação, mostrado como `excludeActions`. A solicitação de classificação precisa das [ações](concepts-features.md#actions-represent-a-list-of-options), do currentContext, das excludeActions e de uma ID exclusiva do evento de classificação (como um GUID), para receber a resposta classificada. 

Este início rápido tem recursos de contexto simples da hora do dia e da preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](concept-feature-evaluation.md) [ações e recursos](concepts-features.md) pode ser uma questão não trivial.  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>Enviar uma recompensa

Para concluir a solicitação de recompensa, o programa obtém a seleção do usuário da linha de comando, atribui um valor numérico a cada seleção e, em seguida, envia uma ID exclusiva de evento de classificação e o valor numérico para o método de recompensa.

Este início rápido atribui um número simples como uma recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [recompensa](concept-rewards.md) pode ser uma questão não trivial, dependendo de suas necessidades específicas. 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo com o Node.js no diretório do aplicativo.

```console
node sample.js
```

![O programa de Início Rápido faz algumas perguntas para coletar as preferências do usuário, conhecidas como recursos e, em seguida, fornece a ação principal.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Como funciona o Personalizador](how-personalizer-works.md)

* [O que é Personalizador?](what-is-personalizer.md)
* [Onde você pode usar o Personalizador?](where-can-you-use-personalizer.md)
* [Solução de problemas](troubleshooting.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js).
