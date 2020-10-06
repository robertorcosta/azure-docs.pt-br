---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 107cc24cc03c7f8716f4ee0577fc2372668adcd9
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604811"
---
Este guia fornece informações e um código de exemplo para ajudar você a começar a usar a biblioteca de clientes da Visão Personalizada para Java, a fim de criar um modelo de detecção de objetos. Você criará um projeto, adicionará marcas, treinará o projeto e usará a URL do ponto de extremidade de previsão do projeto para testá-lo programaticamente. Use este exemplo como um modelo para criar o próprio aplicativo de reconhecimento de imagem.

> [!NOTE]
> Se desejar criar e treinar um modelo de detecção de objetos _sem_ escrever código, confira as [diretrizes baseadas em navegador](../../get-started-build-detector.md).

## <a name="prerequisites"></a>Pré-requisitos:

- Um Java IDE de sua preferência
- [JDK 7 ou 8](https://aka.ms/azure-jdks) instalado.
- [Maven](https://maven.apache.org/) instalado
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library"></a>Obter a biblioteca de clientes da Visão Personalizada

Para escrever um aplicativo de análise de imagem com a Visão Personalizada para Java, você precisará dos pacotes maven da Visão Personalizada. Esses pacotes estão incluídos no projeto de exemplo que você baixará, mas é possível acessá-los individualmente aqui.

Encontre a biblioteca de clientes da Visão Personalizada no repositório central do Maven:
- [SDK de treinamento](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [SDK de previsão](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clone ou baixe o projeto [Exemplos de SDK de Java dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). Navegue até a pasta **Vision/CustomVision/**.

Esse projeto Java cria um novo projeto de detecção de objeto da Visão Personalizada denominado __Sample Java OD Project__, que pode ser acessado no [site da Visão Personalizada](https://customvision.ai/). Ele então carrega as imagens para treinar e testar um classificador. Neste projeto, o classificador destina-se a determinar se um objeto é um **fork** ou uma **tesoura**.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

O programa está configurado para referenciar seus dados de chave como variáveis de ambiente. Navegue até a pasta **Vision/CustomVision** e insira os comandos do PowerShell a seguir para definir as variáveis de ambiente. 

> [!NOTE]
> Se estiver usando um sistema operacional não Windows, confira [Configurar variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) para obter instruções.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="examine-the-code"></a>Examinar o código

Carregue o projeto `Vision/CustomVision` em seu Java IDE e abra o arquivo _CustomVisionSamples.java_. Localize o método **runSample** e comente a chamada de método **ImageClassification_Sample** – esse método executa o cenário de classificação de imagens, que não é abordado neste guia. O método **ObjectDetection_Sample** implementa a funcionalidade principal desse início rápido; navegue até sua definição e inspecione o código. 

## <a name="create-a-new-custom-vision-project"></a>Criar um novo projeto de Visão Personalizada

Vá até o bloco de código que cria um cliente de treinamento e um projeto de detecção de objeto. O projeto criado será exibido no [site da Visão Personalizada](https://customvision.ai/) visitado anteriormente. Confira as sobrecargas do método [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) para especificar outras opções ao criar seu projeto (explicado no guia do portal da Web [Criar um detector](../../get-started-build-detector.md)).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>Adicionar marcas ao seu projeto

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Ao marcar imagens em projetos de detecção de objeto, você precisa especificar a região de cada objeto marcado usando coordenadas normalizadas. Vá até a definição do Mapa `regionMap`. Esse código associa cada uma das imagens de exemplo à região marcada.

> [!NOTE]
> Se você não tiver um utilitário do tipo "clicar e arrastar" para marcar as coordenadas das regiões, use a interface do usuário da Web em [Customvision.ai](https://www.customvision.ai/). Neste exemplo, as coordenadas já foram fornecidas.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Em seguida, vá até o bloco de código que adiciona as imagens ao projeto. As imagens são lidas da pasta **src/main/resources** do projeto e carregadas no serviço com as marcas e coordenadas de região pertinentes.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

O snippet de código anterior faz uso de duas funções auxiliares que recuperam as imagens como fluxos de recurso e as carrega no serviço (você pode carregar até 64 imagens em um único lote).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project-and-publish"></a>Treinar o projeto e publicar

Este código cria a primeira iteração do modelo de previsão e, em seguida, publica essa iteração no ponto de extremidade de previsão. O nome dado à iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não fica disponível no ponto de extremidade de previsão até ser publicada.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

## <a name="use-the-prediction-endpoint"></a>Usar o ponto de extremidade de previsão

O ponto de extremidade de previsão, representado aqui pelo objeto `predictor`, é a referência que você usa para enviar uma imagem para o modelo atual e obter uma previsão de classificação. Neste exemplo, `predictor` é definido em outro lugar usando a variável de ambiente de chave de previsão.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>Executar o aplicativo

Para compilar e executar a solução usando o Maven, navegue até o diretório do projeto (**Vision/CustomVision**) em um prompt de comando e execute o comando run:

```bash
mvn compile exec:java
```

Exiba a saída de console para registro em log e resultados de previsão. Em seguida, você pode verificar se a imagem de teste foi marcada corretamente e se a região da detecção está correta.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora, você concluiu cada etapa do processo de detecção de objetos no código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso. O guia a seguir lida com a classificação de imagens, mas seus princípios são semelhantes aos da detecção de objetos.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](../../test-your-model.md)

* O que é Visão Personalizada?
* [Documentação de referência do SDK](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/customvision?view=azure-java-stable)