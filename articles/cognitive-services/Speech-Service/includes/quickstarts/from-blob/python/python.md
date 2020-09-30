---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: d69cb782b3be7035b7571653dcb434e44d22da93
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377362"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Criar um recurso de Fala do Azure](../../../../overview.md#try-the-speech-service-for-free)
> * [Carregar um arquivo de origem em um blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>Baixar e instalar a biblioteca de clientes da API

Para executar a amostra, é necessário gerar a biblioteca Python para a API REST gerada por meio de [Swagger](https://swagger.io).

Siga estas etapas para a instalação:

1. Ir para https://editor.swagger.io.
1. Clique em **Arquivo** e, em seguida, em **Importar URL**.
1. Insira a URL do Swagger, incluindo a região da assinatura do serviço de Fala: `https://<your-region>.cris.ai/docs/v2.0/swagger`.
1. Clique em **Gerar Cliente** e selecione **Python**.
1. Salve a biblioteca de clientes.
1. Extraia o python-client-generated.zip baixado em algum lugar no sistema de arquivos.
1. Instale o módulo python-client extraído em seu ambiente Python usando o pip: `pip install path/to/package/python-client`.
1. O nome do pacote instalado é `swagger_client`. Verifique se a instalação funcionou usando o comando `python -c "import swagger_client"`.

> [!NOTE]
> Devido a um [bug conhecido na geração automática do Swagger](https://github.com/swagger-api/swagger-codegen/issues/7541), talvez haja erros ao importar o pacote `swagger_client`.
> É possível corrigi-los excluindo a linha com o conteúdo
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> do arquivo `swagger_client/models/model.py` e a linha com o conteúdo
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> do arquivo `swagger_client/models/inner_error.py` dentro do pacote instalado. A mensagem de erro informará o local desses arquivos na instalação.

## <a name="install-other-dependencies"></a>Instalar outras dependências

O exemplo usa a biblioteca `requests`. É possível instalá-la com o comando

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Comece com código de texto clichê

Vamos adicionar um código que funciona como um esqueleto para o projeto.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Criar e configurar um Cliente Http
A primeira coisa que precisaremos é de um Cliente Http que tenha uma URL base correta e um conjunto de autenticação.
Insira este código em `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Gerar uma solicitação de transcrição
Em seguida, geraremos a solicitação de transcrição. Adicione este código a `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Enviar a solicitação e verificar seu status
Agora postaremos a solicitação no serviço de Fala e verificaremos o código de resposta inicial. Esse código de resposta simplesmente indicará se o serviço recebeu a solicitação. O serviço retornará uma Url nos cabeçalhos de resposta, que é o local em que ele armazenará o status da transcrição.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Aguarde a conclusão da transcrição
Como o serviço processa a transcrição de maneira assíncrona, precisamos investigar seu status periodicamente. Verificaremos a cada 5 segundos.

Enumeraremos todas as transcrições que esse recurso do serviço de Fala está processando e procuraremos o que criamos.

Aqui está o código de sondagem com o status exibição para tudo, exceto uma conclusão bem-sucedida. Faremos isso em seguida.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Exibir os resultados da transcrição
Depois que o serviço tiver concluído a transcrição com êxito, os resultados serão armazenados em outra Url que podemos obter da resposta de status.

Aqui, obtemos o resultado JSON e o exibimos.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Verificar o código
Neste momento, seu código deverá ter a seguinte aparência: (Adicionamos alguns comentários nesta versão) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Compilar e executar o aplicativo

Agora está tudo pronto para você compilar o aplicativo e testar o reconhecimento de fala usando o serviço de fala.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
