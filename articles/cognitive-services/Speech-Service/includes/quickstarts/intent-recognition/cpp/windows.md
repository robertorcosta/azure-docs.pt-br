---
title: 'Início Rápido: Reconhecer fala, intenções e entidades, C++ – Serviço de Fala'
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
ms.openlocfilehash: 277548c859370a700973d9f60556fe38e94548b0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280378"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
>
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Criar um aplicativo do LUIS (Reconhecimento vocal) para obter uma chave de ponto de extremidade](../../../../quickstarts/create-luis.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="open-your-project-in-visual-studio"></a>Abra o projeto no Visual Studio

A primeira etapa é verificar se o projeto está aberto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue o projeto e abra o `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto. Lembre-se de que você criou um método assíncrono chamado `recognizeIntent()`.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,73-81)]

## <a name="create-a-speech-configuration"></a>Criar uma configuração de Fala

Antes de inicializar um objeto `IntentRecognizer`, é preciso criar uma configuração que use a região e a chave de ponto de extremidade LUIS. Insira esse código no método `recognizeIntent()`.

Esta amostra usa o método `FromSubscription()` para criar o `SpeechConfig`. Para ver uma lista completa dos métodos disponíveis, confira a [Classe SpeechConfig](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

> [!NOTE]
> É importante usar a chave de ponto de extremidade LUIS, e não as chaves de Início ou de Criação porque apenas a chave do ponto de extremidade é válida para o reconhecimento de conversão de fala em intenção. Confira o artigo [Criar um aplicativo LUIS e obter uma chave de ponto de extremidade](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) para ver instruções sobre como obter a chave correta.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

## <a name="initialize-an-intentrecognizer"></a>Inicializar um IntentRecognizer

Agora, vamos criar um `IntentRecognizer`. Insira esse código no método `recognizeIntent()`, logo abaixo da configuração de Fala.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Adicione um LanguageUnderstandingModel e as Intenções

Agora é preciso associar um `LanguageUnderstandingModel` ao reconhecedor de intenção e adicionar as intenções que você deseja reconhecer.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-34)]

## <a name="recognize-an-intent"></a>Reconhecer uma intenção

No objeto `IntentRecognizer`, chame o método `RecognizeOnceAsync()`. Esse método permite que o Serviço de Fala saiba que você está enviando uma única expressão para reconhecimento e permite parar o reconhecimento, assim que a frase é identificada.
Para simplificar, aguardaremos os resultados futuros retornarem para concluir.

Dentro da instrução using, adicione este código: [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=44)]

## <a name="display-the-recognition-results-or-errors"></a>Exibir os resultados do reconhecimento (ou os erros)

Quando o Serviço de Fala retornar o resultado do reconhecimento, você poderá utilizá-lo. Para manter a simplicidade, vamos imprimir o resultado no console.

Dentro da instrução using, abaixo de `RecognizeOnceAsync()`, adicione este código: [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=47-72)]

## <a name="check-your-code"></a>Verificar o código

Neste momento, seu código deverá ter a seguinte aparência:  
(Adicionamos alguns comentários nesta versão) [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-81)]

## <a name="build-and-run-your-app"></a>Compilar e executar o aplicativo

Agora está tudo pronto para você compilar o aplicativo e testar o reconhecimento de fala usando o serviço de fala.

1. **Compilar o código**: na barra de menus do Visual Studio, escolha **Compilar** > **Compilar Solução**.
2. **Iniciar o aplicativo**: na barra de menus, escolha **Depurar** > **Iniciar Depuração** ou pressione **F5**.
3. **Iniciar o reconhecimento**: ele solicitará que você fale uma frase em inglês. Sua fala é enviada ao Serviço de Fala, transcrita como texto e renderizada no console.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]