---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-js, cog-serv-seo-aug-2020
ms.openlocfilehash: f81ec647eefd8b3bd59a60855c0383a32caf8acb
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94574075"
---
Use as bibliotecas de clientes do LUIS (Reconhecimento vocal) para Node.js para:

* Criar um aplicativo
* Adicionar uma intenção e uma entidade com machine learning com um exemplo de enunciado
* Treinar e publicar o aplicativo
* Runtime de previsão da consulta

[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) |  Código-fonte da biblioteca de [Criação](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) e [Previsão](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | NPM de [Criação](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring) e [Previsão](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Amostras](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org)
* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Quando você tiver sua assinatura do Azure, [crie um recurso de criação do Reconhecimento vocal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) no portal do Azure para obter a chave e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você [criar](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) para conectar seu aplicativo à criação do Reconhecimento vocal. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido. Você pode usar o tipo de preço gratuito (`F0`) para experimentar o serviço.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-javascript-application"></a>Criar um aplicativo JavaScript

1. Em uma janela do console, crie um diretório para seu aplicativo e mova-o para esse diretório.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Inicialize o diretório como um aplicativo JavaScript criando um arquivo `package.json`.

    ```console
    npm init -y
    ```

1. Crie um arquivo chamado `index.js` para o código JavaScript.

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>Instalar as bibliotecas npm

No diretório do aplicativo, instale as dependências com os seguintes comandos, executados uma linha por vez:

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring
npm install @azure/cognitiveservices-luis-runtime
```

O `package.json` será semelhante a:

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>Modelo de Objeto de Criação

O cliente de criação do LUIS (Reconhecimento Vocal) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) que se autentica no Azure, que contém sua chave de criação.

## <a name="code-examples-for-authoring"></a>Exemplos de código para criação

Depois que o cliente for criado, use-o para acessar a funcionalidade, incluindo:

* Aplicativos: [adicionar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-), [excluir](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-), [publicar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Exemplo de enunciados – [Adicionar por lote](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [excluir por ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Recursos – Gerenciar [listas de frases](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Modelo – Gerenciar [intenções](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) e entidades
* Padrão – Gerenciar [padrões](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Treinar – [Treinar](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) o aplicativo e sondar o [status de treinamento](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Versões](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) – Gerenciar com clonar, exportar e excluir

## <a name="prediction-object-model"></a>Modelo de Objeto de Previsão

O cliente de criação do LUIS (Reconhecimento Vocal) é um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) que se autentica no Azure, que contém sua chave de criação.

## <a name="code-examples-for-prediction-runtime"></a>Exemplos de código para runtime de previsão

Depois que o cliente for criado, use-o para acessar a funcionalidade, incluindo:

* [Previsão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) por slot de `staging` ou `production`
* [Previsão por versão](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>Adicionar as dependências

Abra o arquivo `index.js` no seu editor preferido ou IDE chamado e adicione as dependências a seguir.

[!code-javascript[Add NPM libraries to code file](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Adicionar o código clichê

1. Adicione o método `quickstart` e a respectiva chamada. Esse método armazena a maior parte do código restante. Esse método é chamado no final do arquivo.

    ```javascript
    const quickstart = async () => {

        // add calls here


    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. Adicione o código restante ao método do guia de início rápido, a menos que especificado de outro modo.

## <a name="create-variables-for-the-app"></a>Criar variáveis para o aplicativo

Crie dois conjuntos de variáveis: o primeiro conjunto, você vai alterar; o segundo, você vai deixar como ele aparece no exemplo de código. 

1. Crie variáveis para manter a chave de criação e os nomes do recurso.

    [!code-javascript[Variables you need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouChange)]

1. Crie variáveis para armazenar os pontos de extremidade, o nome do aplicativo, a versão e o nome da intenção.

    [!code-javascript[Variables you don't need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um objeto [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) com a sua chave e use-o com o ponto de extremidade para criar um objeto [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest).

[!code-javascript[Authenticate the client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Criar um aplicativo LUIS

Um aplicativo LUIS contém o modelo de NLP (processamento em idioma natural), incluindo intenções, entidades e exemplos de enunciados.

Crie um [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) objeto [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) para criar o aplicativo. O nome e a cultura do idioma são propriedades obrigatórias.

[!code-javascript[Create a LUIS app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Criar intenção para o aplicativo
O objeto principal no modelo de um aplicativo do LUIS é a intenção. A intenção se alinha com um agrupamento de _intenções_ de enunciado do usuário. Um usuário pode fazer uma pergunta ou uma declaração procurando uma resposta _desejada_ específica de um bot (ou outro aplicativo cliente). Exemplos de intenções são: reservar um voo, perguntar sobre o tempo em uma cidade de destino e solicitar informações de contato para o atendimento ao cliente.

Use o método [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) com o nome da intenção exclusiva e, em seguida, passe a ID do aplicativo, a ID da versão e o nome da nova tentativa.

O valor de `intentName` é embutido em código em `OrderPizzaIntent` como parte das variáveis da seção [Criar variáveis para o aplicativo](#create-variables-for-the-app).

[!code-javascript[Create intent for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Criar entidades para o aplicativo

Embora as entidades não sejam necessárias, são encontradas na maioria dos aplicativos. A entidade extrai informações do enunciado do usuário, necessárias para atender à intenção do usuário. Há vários tipos de entidades [predefinidas](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) e personalizadas, cada uma com seus próprios modelos de DTO (objeto de transformação de dados).  Entidades predefinidas comuns para adicionar ao seu aplicativo [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md).

É importante saber que as entidades não estão marcadas com uma intenção. Em geral, elas podem se aplicar a muitas intenções. Somente os exemplos de enunciados de usuário são marcados para uma única intenção específica.

Os métodos de criação para as entidades fazem parte da classe do [Modelo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest). Cada tipo de entidade tem seu próprio modelo de DTO (objeto de transformação de dados).

O código de criação de entidade cria uma entidade de machine learning com as subentidades e os recursos aplicados às subentidades `Quantity`.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Captura de tela parcial do portal mostrando a entidade criada, uma entidade de machine learning com as subentidades e os recursos aplicados às subentidades `Quantity`.":::

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddEntities)]

Coloque o método a seguir acima do método `quickstart` para localizar a ID da subentidade Quantity, a fim de atribuir os recursos a ela.

[!code-javascript[Find subentity id](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Adicionar exemplo de enunciado para intenção

A fim de determinar a intenção de um enunciado e extrair entidades, o aplicativo precisa de exemplos de enunciados. Os exemplos precisam ter como destino uma única intenção específica e devem marcar todas as entidades personalizadas. As entidades predefinidas não precisam ser marcadas.

Adicione exemplos de enunciado ao criar uma lista de objetos [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest), um objeto para cada exemplo de enunciado. Cada exemplo deve marcar todas as entidades com um dicionário de pares de nome/valor do nome da entidade e do valor da entidade. O valor da entidade deve exatamente o mesmo que aparece no texto do exemplo de enunciado.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Captura de tela parcial mostrando o exemplo de enunciado rotulado no portal. ":::

Chame [examples.add](https://docs.microsoft.com//javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#add-string--string--examplelabelobject--models-examplesaddoptionalparams-) com a ID do aplicativo, a ID da versão e o exemplo.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>Treinar o aplicativo

Depois que o modelo for criado, o aplicativo LUIS precisará ser treinado para esta versão do modelo. Um modelo treinado pode ser usado em um [contêiner](../luis-container-howto.md) ou [publicado](../luis-how-to-publish-app.md) nos slots de preparo ou de produto.

O método [train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) precisa da ID do aplicativo e da ID da versão.

Um modelo muito pequeno, como este guia de início rápido mostra, será treinado muito rapidamente. Para aplicativos de nível de produção, o treinamento do aplicativo deve incluir uma chamada de sondagem para o método [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) para determinar quando ou se o treinamento foi bem-sucedido. A resposta é uma lista de objetos [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) com um status separado para cada objeto. Todos os objetos devem ter êxito para que o treinamento seja considerado concluído.

[!code-javascript[Train the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>Publicar aplicativo no slot de produção

Publique o aplicativo do LUIS usando o método [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-). Isso publica a versão treinada atual no slot especificado no ponto de extremidade. Seu aplicativo cliente usa esse ponto de extremidade para enviar enunciados de usuário para previsão de extração de intenção e entidade.

[!code-javascript[Publish app to production slot](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PublishVersion)]


## <a name="authenticate-the-prediction-runtime-client"></a>Autenticar o cliente de runtime de previsão

Use um objeto msRest.ApiKeyCredentials com a sua chave e use-o com o ponto de extremidade para criar um objeto [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest).

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-javascript [Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Obter uma previsão do runtime

Adicione o código a seguir para criar a solicitação para o runtime de previsão. O enunciado do usuário faz parte do objeto [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest).

O método **[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** precisa de vários parâmetros, como a ID do aplicativo, o nome do slot e o objeto de solicitação de previsão, para atender à solicitação. As outras opções, como detalhada, mostram todas as intenções e log são opcionais.

[!code-javascript [Get prediction from runtime](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node index.js` no seu arquivo de início rápido.

```console
node index.js
```
