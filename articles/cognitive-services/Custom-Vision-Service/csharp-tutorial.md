---
title: 'Início Rápido: Criar um projeto de classificação de imagem com o SDK da Visão Personalizada para C#'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione marcas, faça upload de imagens, treine seu projeto e faça uma previsão usando o SDK do .NET com C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7c71077deff4a7c01a1c982dc41f477c4c19f69e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404204"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Início Rápido: Criar um projeto de classificação de imagem com o SDK de .NET para Visão Personalizada

Este artigo fornece informações e código de exemplo para ajudar você a começar a usar o SDK da Visão Personalizada com C# para criar um modelo de classificação de imagem. Depois de criada, você poderá adicionar marcas, carregar imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão do projeto padrão e usar o ponto de extremidade para testar programaticamente uma imagem. Use este exemplo como um modelo para criar seu próprio aplicativo .NET. Se você quiser passar pelo processo de criar e usar um modelo de classificação _sem_ código, confira as [diretrizes baseadas em navegador](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prerequisites

- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obter o SDK de Visão Personalizada e um exemplo de código

Para escrever um aplicativo .NET que usa a Visão Personalizada, você precisará dos pacotes NuGet da Visão Personalizada. Esses pacotes estão incluídos no projeto de exemplo que você baixará, mas é possível acessá-los individualmente aqui.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Clone ou baixe o projeto [Exemplos de .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navegue até a pasta **CustomVision/ImageClassification** e abra _ImageClassification.csproj_ no Visual Studio.

Este projeto do Visual Studio cria um novo projeto de Visão Personalizada denominado __My New Project__, que pode ser acessado pelo [site da Visão Personalizada](https://customvision.ai/). Ele então carrega as imagens para treinar e testar um classificador. Neste projeto, o classificador serve para determinar se uma árvore é uma __Cicuta__ ou uma __Cerejeira__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Compreender o código

Abra o arquivo _Program.cs_ e inspecione o código. [Crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para suas chaves de treinamento e previsão denominadas `CUSTOM_VISION_TRAINING_KEY` e `CUSTOM_VISION_PREDICTION_KEY`, respectivamente. O script procurará essas variáveis.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Além disso, obtenha sua URL de Ponto de Extremidade no painel Configurações do site da Visão Personalizada. Salve-o em uma variável de ambiente chamada `CUSTOM_VISION_ENDPOINT`. O script salva uma referência a ele na raiz de sua classe.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

As linhas de código a seguir executam a funcionalidade principal do projeto.

### <a name="create-a-new-custom-vision-service-project"></a>Criar um novo projeto do Serviço de Visão Personalizada

O projeto criado será exibido no [site da Visão Personalizada](https://customvision.ai/) visitado anteriormente. Confira o método [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) para especificar outras opções ao criar seu projeto (explicado no guia do portal da Web [Criar um classificador](getting-started-build-a-classifier.md)).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Criar marcas no projeto

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Carregar e marcar imagens

As imagens para este projeto estão incluídas. Elas são referenciadas no método **LoadImagesFromDisk** em _Program.cs_. Você pode carregar até 64 imagens em um único lote.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Treinar o classificador e publicar

Este código cria a primeira iteração do modelo de previsão e, em seguida, publica essa iteração no ponto de extremidade de previsão. Você pode usar o nome da iteração para enviar solicitações de previsão. Uma iteração não fica disponível no ponto de extremidade de previsão até ser publicada.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Definir o ponto de extremidade de previsão

O ponto de extremidade de previsão é a referência que você pode usar para enviar uma imagem para o modelo atual e obter uma previsão de classificação.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Enviar uma imagem ao ponto de extremidade de previsão padrão

Nesse script, a imagem de teste é carregada no método **LoadImagesFromDisk** e a saída de previsão do modelo é exibida no console. O valor da variável `publishedModelName` deve corresponder ao valor "Publicado como" encontrado na guia **Desempenho** do portal de Visão Personalizada. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Executar o aplicativo

Enquanto o aplicativo é executado, ele deve abrir uma janela do console e gravar a seguinte saída:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Em seguida, você pode verificar se a imagem de teste (encontrada em **imagens/teste/** ) foi marcada apropriadamente. Pressione qualquer tecla para sair do aplicativo. Você pode também voltar para o [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto recém-criado.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você viu como executar cada etapa do processo de classificação de imagens no código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](test-your-model.md)
