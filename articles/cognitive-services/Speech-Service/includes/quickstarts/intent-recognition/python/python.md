---
title: 'Início Rápido: Reconhecer uma fala, intenções e entidades, Python – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 9a4c7f24a2e28743679e312e3dce0bc605db6749
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815966"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
>
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Criar um aplicativo do LUIS (Reconhecimento vocal) para obter uma chave de ponto de extremidade](../../../../quickstarts/create-luis.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>Abrir o projeto

Abra Quickstart.py no editor do Python.

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Antes de inicializar um objeto `IntentRecognizer`, é preciso criar uma configuração que use a região e a chave de ponto de extremidade LUIS. Em seguida, insira este código.

A amostra constrói o objeto `SpeechConfig` usando a chave e a região LUIS. Para ver uma lista completa dos métodos disponíveis, confira a [Classe SpeechConfig](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).
O SDK de Fala usará como padrão o reconhecimento do uso de en-us como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

> [!NOTE]
> É importante usar a chave de ponto de extremidade LUIS, e não as chaves de Início ou de Criação porque apenas a chave do ponto de extremidade é válida para o reconhecimento de conversão de fala em intenção. Confira o artigo [Criar um aplicativo LUIS e obter uma chave de ponto de extremidade](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) para ver instruções sobre como obter a chave correta.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar um `IntentRecognizer`. Insira esse código logo abaixo da configuração de fala.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicione um LanguageUnderstandingModel e as intenções

Agora é preciso associar um `LanguageUnderstandingModel` ao reconhecedor de intenção e adicionar as intenções que você deseja reconhecer.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

No objeto `IntentRecognizer`, chame o método `recognize_once()`. Esse método permite que o Serviço de Fala saiba que você está enviando uma única expressão para reconhecimento e permite parar o reconhecimento, assim que a frase é identificada.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados do reconhecimento (ou os erros)

Quando o Serviço de Fala retornar o resultado do reconhecimento, você poderá utilizá-lo. Para manter a simplicidade, vamos imprimir o resultado no console.

Dentro da instrução using, abaixo da chamada à `recognize_once()`, adicione este código: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Verificar o código

Neste momento, seu código deverá ter a seguinte aparência:  
(Adicionamos alguns comentários nesta versão) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Compilar e executar o aplicativo

Execute a amostra no console ou no IDE:

    ````
    python quickstart.py
    ````

Os próximos 15 segundos de entrada de fala do microfone serão reconhecidos e registrados na janela do console.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
