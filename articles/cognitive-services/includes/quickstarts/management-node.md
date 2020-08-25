---
title: 'Início Rápido: biblioteca de clientes de gerenciamento do Azure para Node.js'
description: Neste início rápido, comece a usar a biblioteca de clientes de gerenciamento do Azure para Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 59063912b2976890976e3c27bc4f2d93b6392e37
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607600"
---
[Documentação de referência](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [Pacote (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [Exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure; [crie uma gratuitamente](https://azure.microsoft.com/free/).
* A versão atual do [Node.js](https://nodejs.org/)

## <a name="create-an-azure-service-principal"></a>Criar uma entidade de serviço do Azure

Para que o aplicativo interaja com sua conta do Azure, você precisa de uma entidade de serviço do Azure para gerenciar permissões. Siga as instruções em [Criar uma entidade de serviço do Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Ao criar uma entidade de serviço, você verá que ela tem um valor secreto, uma ID e uma ID de aplicativo. Salve a ID do aplicativo e o segredo em um local temporário para etapas posteriores.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar um recurso dos Serviços Cognitivos, sua conta precisa ter um grupo de recursos do Azure para conter o recurso. Se ainda não tiver um grupo de recursos, crie um no [portal do Azure](https://ms.portal.azure.com/).

## <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar um aplicativo do Node com um arquivo `package.json`. 

```console
npm init
```

Crie um arquivo chamado _index.js_ antes de continuar.

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Instale os seguintes pacotes NPM:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

O arquivo `package.json` do seu aplicativo será atualizado com as dependências.

### <a name="import-libraries"></a>Importar bibliotecas

Abra o script _index.js_ e importe as bibliotecas a seguir.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione os campos a seguir à raiz do script e preencha seus valores, usando a entidade de serviço que você criou e as informações de sua conta do Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Em seguida, adicione a função `quickstart` a seguir para cuidar do trabalho principal do programa. O primeiro bloco de código constrói um objeto **CognitiveServicesManagementClient** usando as variáveis de credencial que você inseriu acima. Esse objeto é necessário para todas as operações de gerenciamento do Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Chamar funções de gerenciamento

Adicione o código a seguir ao final de sua função `quickstart` para listar os recursos disponíveis, criar um recurso de exemplo, listar os recursos de sua propriedade e excluir o recurso de exemplo. Você definirá essas funções nas etapas a seguir.

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="choose-a-service-and-pricing-tier"></a>Escolher um serviço e um tipo de preço

Ao criar um recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) desejado. Você usará essa e outras informações como parâmetros ao criar o recurso. A função a seguir lista os "tipos" de Serviços Cognitivos disponíveis.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

Confira abaixo a lista de SKUs e informações de preços. 

#### <a name="multi-service"></a>Vários serviços

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Vários serviços. Para saber mais, consulte a página de [preços](https://azure.microsoft.com/pricing/details/cognitive-services/).            | `CognitiveServices`     |


#### <a name="vision"></a>Visão

| Serviço                    | Tipo                      |
|----------------------------|---------------------------|
| Pesquisa Visual Computacional            | `ComputerVision`          |
| Visão Personalizada – Previsão | `CustomVision.Prediction` |
| Visão Personalizada – Treinamento   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Reconhecimento de Formulários            | `FormRecognizer`          |
| Reconhecimento de Tinta Digital             | `InkRecognizer`           |

#### <a name="search"></a>Search

| Serviço            | Tipo                  |
|--------------------|-----------------------|
| Sugestão Automática do Bing   | `Bing.Autosuggest.v7` |
| Pesquisa Personalizada do Bing | `Bing.CustomSearch`   |
| Pesquisa de Entidade do Bing | `Bing.EntitySearch`   |
| Pesquisa do Bing        | `Bing.Search.v7`      |
| Verificação Ortográfica do Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Fala

| Serviço            | Tipo                 |
|--------------------|----------------------|
| Serviços de Fala    | `SpeechServices`     |
| Reconhecimento de fala | `SpeakerRecognition` |

#### <a name="language"></a>Idioma

| Serviço            | Tipo                |
|--------------------|---------------------|
| Compreensão de Formulário | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Análise de texto     | `TextAnalytics`     |
| Tradução de texto   | `TextTranslation`   |

#### <a name="decision"></a>Decisão

| Serviço           | Tipo               |
|-------------------|--------------------|
| Detector de Anomalias  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizador      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Tipos de preço e cobrança

Os tipos de preço (e o valor que é cobrado de você) são baseados no número de transações que você envia usando suas informações de autenticação. Cada tipo de preço especifica:
* o número máximo de transações permitidas por segundo (TPS).
* os recursos de serviço habilitados no tipo de preço.
* o custo de um número predefinido de transações. Ultrapassar esse número gera um encargo extra, conforme especificado nos [detalhes do preço](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) do serviço.

> [!NOTE]
> Muitos dos Serviços Cognitivos têm uma camada gratuita que você pode usar para experimentar o serviço. Para usar a camada gratuita, use `F0` como a SKU do recurso.

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

Para criar e assinar um recurso dos Serviços Cognitivos, use a função **Criar**. Essa função adiciona um novo recurso que pode ser cobrado ao grupo de recursos que você passa. Ao criar o recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o tipo de preço (ou SKU) desejado e um local do Azure. A função a seguir usa todos esses argumentos e cria um recurso.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>Exibir os recursos

Para exibir todos os recursos em sua conta do Azure (em todos os grupos de recursos), use a seguinte função:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Excluir um recurso

A função a seguir exclui o recurso especificado do grupo de recursos fornecido.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Executar o aplicativo

Adicione o código a seguir à parte inferior do script para chamar sua função `quickstart` principal com tratamento de erro.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Em seguida, na janela do console, execute o aplicativo com o comando `node`.

```console
node index.js
```

## <a name="see-also"></a>Veja também

* [Documentação de referência do SDK de Gerenciamento do Azure](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [O que são os Serviços Cognitivos do Azure?](../../Welcome.md)
* [Autenticar solicitações para os Serviços Cognitivos do Azure](../../authentication.md)
* [Criar um recurso usando o portal do Azure](../../cognitive-services-apis-create-account.md)