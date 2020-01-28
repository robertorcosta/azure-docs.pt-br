---
title: 'Início Rápido: Criar um projeto de classificação de imagem com o SDK de Visão Personalizada para Java'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione marcações, faça upload de imagens, treine seu projeto e faça uma previsão usando o SDK do Java.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: 2b3c6fc404e9a5fefc15aa7b7162ff5573c3be5a
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166135"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Início Rápido: Criar um projeto de classificação de imagem com o SDK de Visão Personalizada para Java

Este artigo mostra como começar a usar o SDK de Visão Personalizada para Java a fim de criar um modelo de classificação de imagens. Depois de criada, você poderá adicionar marcas, carregar imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão do projeto padrão e usar o ponto de extremidade para testar programaticamente uma imagem. Use este exemplo como modelo para criar seu próprio aplicativo Java. Se você quiser passar pelo processo de criar e usar um modelo de classificação _sem_ código, confira as [diretrizes baseadas em navegador](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prerequisites

- Um Java IDE de sua preferência
- [JDK 7 ou 8](https://aka.ms/azure-jdks) instalado.
- Maven instalado
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obter o SDK de Visão Personalizada e um exemplo de código

Para escrever um aplicativo Java que usa a Visão Personalizada, você precisará dos pacotes de maven da Visão Personalizada. Esses pacotes estão incluídos no projeto de exemplo que você baixará, mas é possível acessá-los individualmente aqui.

Você pode instalar o SDK de Visão Personalizada do repositório central do Maven:

- [SDK de treinamento](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [SDK de previsão](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clone ou baixe o projeto [Exemplos de SDK de Java dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Navegue até a pasta **Vision/CustomVision/** .

Esse projeto Java cria um novo projeto de classificação de imagem da Visão Personalizada denominado __Sample Java Project__, que pode ser acessado no [site da Visão Personalizada](https://customvision.ai/). Ele então carrega as imagens para treinar e testar um classificador. Neste projeto, o classificador serve para determinar se uma árvore é uma __Cicuta__ ou uma __Cerejeira__.

[!INCLUDE [get-keys](includes/get-keys.md)]

O programa está configurado para armazenar seus dados de chave como variáveis de ambiente. Defina essas variáveis navegando até a pasta **Vision/CustomVision** no PowerShell. Em seguida, insira os comandos:

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Compreender o código

Carregue o projeto `Vision/CustomVision` em seu Java IDE e abra o arquivo _CustomVisionSamples.java_. Localize o método **runSample** e comente a chamada de método **ObjectDetection_Sample** – esse método executa o cenário de detecção de objetos, que não é abordado neste guia. O método **ImageClassification_Sample** implementa a funcionalidade principal desse exemplo; navegue até sua definição e inspecione o código.

### <a name="create-a-custom-vision-service-project"></a>Criar um projeto de Serviço de Visão Personalizada

Essa primeira parte do código cria um projeto de classificação de imagem. O projeto criado será exibido no [site da Visão Personalizada](https://customvision.ai/) visitado anteriormente. Confira as sobrecargas do método [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) para especificar outras opções ao criar seu projeto (explicado no guia do portal da Web [Criar um classificador](getting-started-build-a-classifier.md)).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Criar marcas no projeto

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Carregar e marcar imagens

As imagens de exemplo estão incluídas na pasta **src/main/resources** do projeto. Elas são lidas a partir daí e carregadas para o serviço com as marcas apropriadas.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

O snippet de código anterior faz uso de duas funções auxiliares que recuperam as imagens como fluxos de recurso e as carrega no serviço (você pode carregar até 64 imagens em um único lote).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-classifier-and-publish"></a>Treinar o classificador e publicar

Este código cria a primeira iteração do modelo de previsão e, em seguida, publica essa iteração no ponto de extremidade de previsão. O nome dado à iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não fica disponível no ponto de extremidade de previsão até ser publicada.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

### <a name="use-the-prediction-endpoint"></a>Usar o ponto de extremidade de previsão

O ponto de extremidade de previsão, representado aqui pelo objeto `predictor`, é a referência que você usa para enviar uma imagem para o modelo atual e obter uma previsão de classificação. Neste exemplo, `predictor` é definido em outro lugar usando a variável de ambiente de chave de previsão.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Executar o aplicativo

Para compilar e executar a solução usando o maven, execute o seguinte comando no diretório do projeto no PowerShell:

```powershell
mvn compile exec:java
```

A saída de console do aplicativo deve ser semelhante ao seguinte texto:

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Em seguida, você pode verificar se a previsão de imagem de teste (as últimas linhas da saída) está correta.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você viu como cada etapa do processo de classificação de imagem pode ser executada em código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](test-your-model.md)
