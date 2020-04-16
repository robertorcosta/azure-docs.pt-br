---
title: 'Início Rápido: Criar um projeto de detecção de objetos com o SDK para C# – Visão Personalizada'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione marcas, faça upload de imagens, treine seu projeto e detecte objetos usando o SDK do .NET com C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: d56953932a92eb4d9e39c8fe5f0d709a83ba3eaa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404192"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Início Rápido: Criar um projeto de detecção de objeto com o SDK do .NET para Visão Personalizada

Este artigo mostra como começar a usar o SDK da Visão Personalizada com C# para criar um modelo de detecção de objetos. Depois de criada, você poderá adicionar regiões marcadas, fazer upload de imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão do projeto padrão e usar o ponto de extremidade para testar programaticamente uma imagem. Use este exemplo como um modelo para criar seu próprio aplicativo .NET. 

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obter o SDK de Visão Personalizada e um exemplo de código

Para escrever um aplicativo .NET que usa a Visão Personalizada, você precisará dos pacotes NuGet da Visão Personalizada. Esses pacotes estão incluídos no projeto de exemplo que você baixará, mas é possível acessá-los individualmente aqui.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clone ou baixe o projeto [Exemplos de .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navegue até a pasta **CustomVision/ObjectDetection** e abra _ObjectDetection.csproj_ no Visual Studio.

Este projeto do Visual Studio cria um novo projeto de Visão Personalizada denominado __My New Project__, que pode ser acessado pelo [site da Visão Personalizada](https://customvision.ai/). Ele carrega as imagens para treinar e testar um modelo de detecção de objeto. Neste projeto, o modelo é treinado para detectar garfos e tesouras nas imagens.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Compreender o código

Abra o arquivo _Program.cs_ e inspecione o código. [Crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para suas chaves de treinamento e previsão denominadas `CUSTOM_VISION_TRAINING_KEY` e `CUSTOM_VISION_PREDICTION_KEY`, respectivamente. O script procurará essas variáveis.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Além disso, obtenha sua URL de Ponto de Extremidade no painel Configurações do site da Visão Personalizada. Salve-o em uma variável de ambiente chamada `CUSTOM_VISION_ENDPOINT`. O script salva uma referência a ele na raiz de sua classe.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto do Serviço de Visão Personalizada

Este próximo trecho de código cria um projeto de detecção de objeto. O projeto criado será exibido no [site da Visão Personalizada](https://customvision.ai/) visitado anteriormente. Confira o método [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) para especificar outras opções ao criar seu projeto (explicado no guia do portal da Web [Criar um detector](get-started-build-detector.md)).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Adicionar marcas ao projeto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Ao marcar imagens em projetos de detecção de objeto, você precisa especificar a região de cada objeto marcado usando coordenadas normalizadas. O código a seguir associa cada uma das imagens de exemplo à região marcada.

> [!NOTE]
> Se você não tiver um utilitário do tipo "clicar e arrastar" para marcar as coordenadas das regiões, use a interface do usuário da Web em [Customvision.ai](https://www.customvision.ai/). Neste exemplo, as coordenadas já foram fornecidas.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Em seguida, esse mapa de associações é usado para carregar cada imagem de exemplo com suas coordenadas de região. Você pode carregar até 64 imagens em um único lote.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Neste ponto, você carregou todas as imagens de amostras e marcou cada uma (**bifurcação** ou **tesoura**) com um retângulo de pixel associado.

### <a name="train-the-project"></a>Treinar o projeto

Esse código cria a primeira iteração de treinamento no projeto.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Publicar a iteração atual

O nome dado à iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não fica disponível no ponto de extremidade de previsão até ser publicada.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Criar um ponto de extremidade de previsão

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Usar o ponto de extremidade de previsão

Essa parte do script carrega a imagem de teste, consulta o ponto de extremidade do modelo e gera dados de previsão para o console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Executar o aplicativo

Enquanto o aplicativo é executado, ele deve abrir uma janela do console e gravar a seguinte saída:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Em seguida, você pode verificar se a imagem de teste (encontrada em **Images/Test/** ) foi marcada corretamente e se a região da detecção está correta. Aqui você pode pressionar qualquer tecla para sair do aplicativo.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você viu como executar cada etapa do processo de detecção de objetos no código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso. O guia a seguir lida com a classificação de imagens, mas seus princípios são semelhantes aos da detecção de objetos.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](test-your-model.md)
