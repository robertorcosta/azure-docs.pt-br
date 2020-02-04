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
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 58ee74f7b01738e67f9fddd39eb4eee59cdb65b6
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774218"
---
Use a biblioteca de clientes de criação do LUIS (Reconhecimento vocal) para Node.js com o objetivo de:

* Criar um aplicativo.
* Adicionar intenções, entidades e enunciados.
* Adicionar recursos, como uma lista de frases.
* Treinar e publicar um aplicativo.
* Excluir aplicativo

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [Pacote de Criação (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring), [Pacote de Runtime (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js)

## <a name="prerequisites"></a>Prerequisites

* Recurso de criação do Reconhecimento vocal: [Criar um no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Configurando

### <a name="get-your-language-understanding-luis-starter-key"></a>Obtenha sua chave inicial do LUIS (Reconhecimento vocal)

Obtenha sua [chave inicial](../luis-how-to-azure-subscription.md#starter-key) criando um recurso de criação do LUIS. Mantenha sua chave e o ponto de extremidade da chave para a próxima etapa.

### <a name="create-an-environment-variable"></a>Criar uma variável de ambiente

Usando a chave e a região da chave, crie duas variáveis de ambiente para autenticação:

* `LUIS_AUTHORING_KEY` – a chave de recurso para autenticar as solicitações.
* `LUIS_AUTHORING_ENDPOINT`: o ponto de extremidade associado à chave.

Use as instruções para seu sistema operacional.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Depois de adicionar a variável de ambiente, reinicie a janela do console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela de console para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite seu `.bash_profile` e adicione a variável de ambiente:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela de console para que as alterações entrem em vigor.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Instalar a biblioteca do npm para criação do LUIS

No diretório do aplicativo, instale as dependências com o seguinte comando:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Modelo de objeto

O cliente de criação do LUIS (Reconhecimento Vocal) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) que se autentica no Azure, que contém sua chave de criação.

Depois que o cliente for criado, use-o para acessar a funcionalidade, incluindo:

* Aplicativos: [adicionar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [excluir](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publicar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Exemplo de enunciados – [Adicionar por lote](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [excluir por ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Recursos – Gerenciar [listas de frases](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Modelo – Gerenciar [intenções](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) e entidades
* Padrão – Gerenciar [padrões](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Treinar – [Treinar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) o aplicativo e sondar o [status de treinamento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versões](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) – Gerenciar com clonar, exportar e excluir


## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como fazer o seguinte com a biblioteca de clientes de criação do LUIS (Reconhecimento vocal) para Node.js:

* [Criar um aplicativo](#create-a-luis-app)
* [Adicionar entidades](#create-entities-for-the-app)
* [Adicionar tentativas](#create-intent-for-the-app)
* [Adicionar declarações de exemplo](#add-example-utterance-to-intent)
* [Treine o aplicativo](#train-the-app)
* [Publique o aplicativo](#publish-a-language-understanding-app)
* [Excluir o aplicativo](#delete-a-language-understanding-app)
* [Listar aplicativos](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Crie um arquivo de texto em seu editor ou seu IDE preferido chamado `luis_authoring_quickstart.js`. Em seguida, adicione as dependências a seguir.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

Crie variáveis para a chave e o ponto de extremidade do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) com a sua chave e use-o com o ponto de extremidade para criar um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest).

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar um aplicativo LUIS

1. Crie um aplicativo LUIS para conter o modelo de NLP (processamento de idioma natural) que contém tentativas, entidades e exemplo de enunciados.

1. Crie um [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) objeto [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) para criar o aplicativo. O nome e a cultura do idioma são propriedades obrigatórias.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Criar intenção para o aplicativo
O objeto principal no modelo de um aplicativo do LUIS é a intenção. A intenção se alinha com um agrupamento de _intenções_ de enunciado do usuário. Um usuário pode fazer uma pergunta ou uma declaração procurando uma resposta _desejada_ específica de um bot (ou outro aplicativo cliente). Exemplos de intenções são: reservar um voo, perguntar sobre o tempo em uma cidade de destino e solicitar informações de contato para o atendimento ao cliente.

Use o método [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) com o nome da intenção exclusiva e, em seguida, passe a ID do aplicativo, a ID da versão e o nome da nova tentativa.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Criar entidades para o aplicativo

Embora as entidades não sejam necessárias, são encontradas na maioria dos aplicativos. A entidade extrai informações do enunciado do usuário, necessárias para atender à intenção do usuário. Há vários tipos de entidades [predefinidas](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) e personalizadas, cada uma com seus próprios modelos de DTO (objeto de transformação de dados).  Entidades predefinidas comuns para adicionar ao seu aplicativo [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

Esse método **add_entities** criou uma entidade simples `Location` com duas funções, uma entidade simples `Class`, uma entidade composta `Flight` e adiciona várias entidades predefinidas.

É importante saber que as entidades não estão marcadas com uma intenção. Em geral, elas podem se aplicar a muitas intenções. Somente os exemplos de enunciados de usuário são marcados para uma única intenção específica.

Os métodos de criação para as entidades fazem parte da classe do [Modelo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest). Cada tipo de entidade tem seu próprio modelo de DTO (objeto de transformação de dados).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Adicionar exemplo de enunciado para intenção

A fim de determinar a intenção de um enunciado e extrair entidades, o aplicativo precisa de exemplos de enunciados. Os exemplos precisam ter como destino uma única intenção específica e devem marcar todas as entidades personalizadas. As entidades predefinidas não precisam ser marcadas.

Adicione exemplos de enunciado ao criar uma lista de objetos [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest), um objeto para cada exemplo de enunciado. Cada exemplo deve marcar todas as entidades com um dicionário de pares de nome/valor do nome da entidade e do valor da entidade. O valor da entidade deve exatamente o mesmo que aparece no texto do exemplo de enunciado.

Chame [examples.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) com a ID do aplicativo, a ID da versão e a lista de exemplos. A chamada responde com uma lista de resultados. Você precisa verificar o resultado de cada exemplo para verificar se ele foi adicionado ao modelo com êxito.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Treinar o aplicativo

Depois que o modelo for criado, o aplicativo LUIS precisará ser treinado para esta versão do modelo. Um modelo treinado pode ser usado em um [contêiner](../luis-container-howto.md) ou [publicado](../luis-how-to-publish-app.md) nos slots de preparo ou de produto.

O método [train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) precisa da ID do aplicativo e da ID da versão.

Um modelo muito pequeno, como este guia de início rápido mostra, será treinado muito rapidamente. Para aplicativos de nível de produção, o treinamento do aplicativo deve incluir uma chamada de sondagem para o método [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) para determinar quando ou se o treinamento foi bem-sucedido. A resposta é uma lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) com um status separado para cada objeto. Todos os objetos devem ter êxito para que o treinamento seja considerado concluído.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

O treinamento de todos os modelos leva tempo. Use o operationResult para verificar o status do treinamento.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Publicar um aplicativo de Reconhecimento Vocal

Publique o aplicativo do LUIS usando o método [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-). Isso publica a versão treinada atual no slot especificado no ponto de extremidade. Seu aplicativo cliente usa esse ponto de extremidade para enviar enunciados de usuário para previsão de extração de intenção e entidade.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Excluir um aplicativo de Reconhecimento vocal

Publique o aplicativo LUIS usando o método [app.deleteMethod](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-). Isso excluirá o aplicativo atual.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Listar os aplicativos de Reconhecimento vocal

Obter uma lista de aplicativos associados à chave do Reconhecimento vocal

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node luis_authoring_quickstart.js` no seu arquivo de início rápido.

```console
node luis_authoring_quickstart.js
```

A saída da linha de comando do aplicativo é:

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
