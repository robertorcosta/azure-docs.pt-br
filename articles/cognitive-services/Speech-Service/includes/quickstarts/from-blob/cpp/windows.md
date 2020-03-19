---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 20dfc49acdaa30bea6f0652640e007c16f08c572
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925638"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?tabs=linux&pivots=programmming-language-cpp)
> * [Criar um recurso de Fala do Azure](../../../../get-started.md)
> * [Carregar um arquivo de origem em um blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Abra o projeto no Visual Studio

A primeira etapa é verificar se o projeto está aberto no Visual Studio.

1. Inicie o Visual Studio 2019.
2. Carregue o projeto e abra o `helloworld.cpp`.

## <a name="add-a-references"></a>Adicionar referências

Para acelerar nosso desenvolvimento de código, usaremos alguns componentes externos:
* [SDK REST do CPP](https://github.com/microsoft/cpprestsdk) Uma biblioteca de clientes para fazer chamadas REST a um serviço REST.
* [nlohmann/json](https://github.com/nlohmann/json) Biblioteca prática de Análise/serialização/desserialização de JSON.

Ambos podem ser instalados usando [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Wrappers JSON

Como as APIs REST usam solicitações no formato JSON e também retornam resultados em JSON, podemos interagir com elas usando apenas cadeias de caracteres, mas isso não é recomendado.
Para facilitar o gerenciamento das solicitações e das respostas, declararemos algumas classes a serem usadas para serializar/desserializar o JSON e alguns métodos para auxiliar nlohmann/json.

Vá em frente e coloque suas declarações antes de `recognizeSpeech`.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um Cliente Http
A primeira coisa que precisaremos é de um Cliente Http que tenha uma URL base correta e um conjunto de autenticação.
Insira este código em `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Gerar uma solicitação de transcrição
Em seguida, geraremos a solicitação de transcrição. Adicione este código a `recognizeSpeech`

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Enviar a solicitação e verificar seu status
Agora postaremos a solicitação no serviço de Fala e verificaremos o código de resposta inicial. Esse código de resposta simplesmente indicará se o serviço recebeu a solicitação. O serviço retornará uma Url nos cabeçalhos de resposta, que é o local em que ele armazenará o status da transcrição.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde a conclusão da transcrição
Como o serviço processa a transcrição de maneira assíncrona, precisamos investigar seu status periodicamente. Verificaremos a cada 5 segundos.

Podemos verificar o status recuperando o conteúdo na Url que obtivemos quando postamos a solicitação. Quando recebermos o conteúdo de volta, nós o desserializaremos em uma de nossa classe auxiliar para facilitar a interação com ele.

Aqui está o código de sondagem com o status exibição para tudo, exceto uma conclusão bem-sucedida. Faremos isso em seguida.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>Exibir os resultados da transcrição
Depois que o serviço tiver concluído a transcrição com êxito, os resultados serão armazenados em outra Url que podemos obter da resposta de status.

Baixaremos o conteúdo dessa URL, desserializaremos o JSON e faremos um loop pelos resultados imprimindo o texto de exibição conforme avançamos.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Verificar o código
Neste momento, seu código deverá ter a seguinte aparência: (Adicionamos alguns comentários a esta versão)

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Compilar e executar o aplicativo

Agora está tudo pronto para você compilar o aplicativo e testar o reconhecimento de fala usando o serviço de fala.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
