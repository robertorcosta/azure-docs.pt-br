---
title: 'Início Rápido: biblioteca de clientes de gerenciamento do Azure para .NET'
description: Neste início rápido, comece a usar a biblioteca de clientes de gerenciamento do Azure para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 93ea0ffc94b78e014b30ab1d45d589eba50fe524
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607596"
---
[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/) | [Exemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura válida do Azure; [crie uma gratuitamente](https://azure.microsoft.com/free/).
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="create-an-azure-service-principal"></a>Criar uma entidade de serviço do Azure

Para que o aplicativo interaja com sua conta do Azure, você precisa de uma entidade de serviço do Azure para gerenciar permissões. Siga as instruções em [Criar uma entidade de serviço do Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Ao criar uma entidade de serviço, você verá que ela tem um valor secreto, uma ID e uma ID de aplicativo. Salve a ID do aplicativo e o segredo em um local temporário para etapas posteriores.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar um recurso dos Serviços Cognitivos, sua conta precisa ter um grupo de recursos do Azure para conter o recurso. Se ainda não tiver um grupo de recursos, crie um no [portal do Azure](https://ms.portal.azure.com/) antes de continuar.

## <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Criar um aplicativo .NET Core. Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `azure-management-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
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

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

No diretório do aplicativo, instale a biblioteca de clientes de Gerenciamento do Azure para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.

### <a name="import-libraries"></a>Importar bibliotecas

Abra *program.cs* e adicione as seguintes instruções `using` na parte superior do arquivo:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione os campos a seguir à raiz de *program.cs* e preencha seus valores, usando a entidade de serviço que você criou e as informações de sua conta do Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Em seguida, no método **Main**, use esses valores para construir um objeto **CognitiveServicesManagementClient**. Esse objeto é necessário para todas as operações de gerenciamento do Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Chamar métodos gerenciamento

Adicione o código a seguir ao método **Main** para listar os recursos disponíveis, criar um recurso de exemplo, listar os recursos de sua propriedade e excluir o recurso de exemplo. Você definirá esses métodos nas etapas a seguir.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>Criar um recurso dos Serviços Cognitivos

### <a name="choose-a-service-and-pricing-tier"></a>Escolher um serviço e um tipo de preço

Ao criar um recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) desejado. Você usará essa e outras informações como parâmetros ao criar o recurso. Você pode encontrar uma lista dos "tipos" de Serviços Cognitivos disponíveis chamando o seguinte método em seu script:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

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

Para criar e assinar um recurso dos Serviços Cognitivos, use o método **Criar**. Esse método adiciona um novo recurso que pode ser cobrado ao grupo de recursos que você passa. Ao criar o recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o tipo de preço (ou SKU) desejado e um local do Azure. O método a seguir usa todos esses argumentos e cria um recurso.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

## <a name="view-your-resources"></a>Exibir os recursos

Para exibir todos os recursos em sua conta do Azure (em todos os grupos de recursos), use o seguinte método:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Excluir um recurso

O método a seguir exclui o recurso especificado do grupo de recursos fornecido.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="see-also"></a>Veja também

* [Documentação de referência do SDK de Gerenciamento do Azure](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)
* [O que são os Serviços Cognitivos do Azure?](../../Welcome.md)
* [Autenticar solicitações para os Serviços Cognitivos do Azure](../../authentication.md)
* [Criar um recurso usando o portal do Azure](../../cognitive-services-apis-create-account.md)