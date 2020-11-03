---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 5b39e1988a798adde8a07a39d7724f50d7a4f851
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755472"
---
Introdução à biblioteca de clientes da Visão Personalizada para Python. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para criar um modelo de classificação de imagem. Você criará um projeto, adicionará marcas, treinará o projeto e usará a URL do ponto de extremidade de previsão do projeto para testá-lo programaticamente. Use este exemplo como um modelo para criar o próprio aplicativo de reconhecimento de imagem.

> [!NOTE]
> Se desejar criar e treinar um modelo de classificação _sem_ escrever código, confira as [diretrizes baseadas em navegador](../../getting-started-build-a-classifier.md).

Use a biblioteca de clientes da Visão Personalizada para Python a fim de:

* Criar um novo projeto de Visão Personalizada
* Adicionar marcas ao projeto
* Carregar e marcar imagens
* Treinar o projeto
* Publicar a iteração atual
* Testar o ponto de extremidade de previsão

[Documentação de referência](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customvision?view=azure-python) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision) | [Pacote (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/) | [Amostras](https://docs.microsoft.com/samples/browse/?products=azure&term=vision&terms=vision&languages=python)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Após obter a assinatura do Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Criar um recurso da Visão Personalizada"  target="_blank">crie um recurso da Visão Personalizada <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para criar um recurso de treinamento e previsão e obter as chaves e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade dos recursos criados para conectar seu aplicativo à Visão Personalizada. Você colará as chaves e o ponto de extremidade no código abaixo mais adiante no início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Para escrever um aplicativo de análise de imagem com a Visão Personalizada para Python, você precisará da biblioteca de clientes da Visão Personalizada. Depois de instalar o Python, execute o seguinte comando no PowerShell ou em uma janela de console:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

Crie um arquivo do Python e importe as bibliotecas a seguir.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.cs), que contém os exemplos de código neste início rápido.

Crie variáveis para as chaves de assinatura e o ponto de extremidade do Azure do recurso.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_creds)]

> [!IMPORTANT]
> Acesse o portal do Azure. Se os recursos da Visão Personalizada que você criou na seção **Pré-requisitos** tiverem sido implantados com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre as chaves e o ponto de extremidade nas páginas de **chave e ponto de extremidade** dos recursos, em **gerenciamento de recursos**. Você precisará obter as chaves de treinamento e de previsão.
>
> Encontre o valor da ID de recurso de previsão na guia **Visão geral** do recurso, listada como **ID da Assinatura**.
>
> Lembre-se de remover as chaves do código quando terminar e nunca poste-as publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) de Serviços Cognitivos para obter mais informações.

## <a name="object-model"></a>Modelo de objeto

|Nome|Descrição|
|---|---|
|[CustomVisionTrainingClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) | Essa classe lida com a criação, o treinamento e a publicação de seus modelos. |
|[CustomVisionPredictionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python)| Essa classe lida com a consulta de seus modelos para obter previsões de classificação de imagem.|
|[ImagePrediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-python)| Essa classe define uma previsão de objeto em uma imagem. Ela inclui propriedades para a ID e o nome do objeto, a localização da caixa delimitadora do objeto e uma pontuação de confiança.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes da Visão Personalizada para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Criar um novo projeto de Visão Personalizada](#create-a-new-custom-vision-project)
* [Adicionar marcas ao projeto](#add-tags-to-the-project)
* [Carregar e marcar imagens](#upload-and-tag-images)
* [Treinar o projeto](#train-the-project)
* [Publicar a iteração atual](#publish-the-current-iteration)
* [Testar o ponto de extremidade de previsão](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie um cliente de treinamento e previsão usando seu ponto de extremidade e suas chaves. Crie objetos **ApiKeyServiceClientCredentials** com suas chaves e use-os com seu ponto de extremidade para criar um objeto [CustomVisionTrainingClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient?view=azure-python) e [CustomVisionPredictionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Criar um novo projeto de Visão Personalizada

Adicione o código a seguir ao seu script para criar um novo projeto do Serviço de Visão Personalizada. 

Confira o método [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) para especificar outras opções ao criar seu projeto (explicado no guia do portal da Web [Criar um classificador](../../getting-started-build-a-classifier.md)).  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Adicionar marcas ao projeto

Para adicionar marcas de classificação ao projeto, adicione o seguinte código:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Primeiro, baixe as imagens de exemplo para este projeto. Salve o conteúdo da [pasta de imagens de exemplo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) em seu dispositivo local.

Para adicionar imagens de exemplo ao projeto, insira o código a seguir após a criação da marca. Esse código carrega cada imagem com sua marca correspondente. Você pode carregar até 64 imagens em um único lote.

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]

> [!NOTE]
> Você precisará alterar o caminho para as imagens com base na localização em que baixou o repositório Amostras do SDK do Python dos Serviços Cognitivos.

## <a name="train-the-project"></a>Treinar o projeto

Este código cria a primeira iteração do modelo de previsão. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> Fazer o treinamento com marcas selecionadas
>
> Opcionalmente, você pode treinar apenas um subconjunto de suas marcas aplicadas. Talvez seja interessante fazer isso se você ainda não tiver aplicado um número suficiente de determinadas marcas, mas tiver outras delas. Na chamada **[train_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** , defina o parâmetro opcional *selected_tags* como uma lista das cadeias de caracteres de ID das marcas que você deseja usar. O modelo será treinado para reconhecer apenas as marcas nessa lista.

## <a name="publish-the-current-iteration"></a>Publicar a iteração atual

Uma iteração não fica disponível no ponto de extremidade de previsão até ser publicada. O código a seguir disponibiliza a iteração atual do modelo para consulta. 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testar o ponto de extremidade de previsão

Para enviar uma imagem para o ponto de extremidade de previsão e recuperar a previsão, adicione o seguinte código ao final do arquivo:

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_test)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute *CustomVisionQuickstart.py*.

```powershell
python CustomVisionQuickstart.py
```

A saída do aplicativo deve ser semelhante ao seguinte texto:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Em seguida, você poderá verificar se a imagem de teste (encontrada em **<base_image_location>/images/Test/** ) foi marcada apropriadamente. Você pode também voltar para o [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto recém-criado.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você viu como cada etapa do processo de classificação de imagem pode ser executada em código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](../../test-your-model.md)

* O que é Visão Personalizada?
* O código-fonte dessa amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.cs)
* [Documentação de referência do SDK](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)