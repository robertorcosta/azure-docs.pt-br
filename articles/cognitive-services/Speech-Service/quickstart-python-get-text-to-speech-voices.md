---
title: Listar vozes de texto em fala, Python – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a obter a lista completa de vozes neurais e padrão para uma região ou um ponto de extremidade usando o Python. A lista é retornada como JSON, e a disponibilidade de voz varia por região.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: b388c8d8b61e2fc638ae2bce5bc6d9eeb25ee0d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401011"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>Obter a lista de vozes da conversão de texto em fala usando Python

Neste artigo, você aprenderá a obter a lista completa de vozes neurais e padrão para uma região ou um ponto de extremidade usando o Python. A lista é retornada como JSON, e a disponibilidade de voz varia por região. Para obter uma lista das regiões com suporte, confira [regiões](regions.md).

Este artigo exige uma conta dos [Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com o recurso do serviço de Fala. Se não tiver uma conta, você poderá usar a [avaliação gratuita](get-started.md) para obter uma chave de assinatura.

## <a name="prerequisites"></a>Pré-requisitos

* Python 2.7.x ou 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>ou seu editor de texto favorito
* Uma chave de assinatura do Azure para o serviço de Fala

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos necessários

Crie um novo projeto Python usando seu IDE ou editor favorito. Em seguida, copie esse snippet de código para seu projeto em um arquivo denominado `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Se você nunca usou esses módulos, você precisará instalá-los antes de executar o programa. Para instalar esses pacotes, execute: `pip install requests`.

Solicitações é usado para solicitações HTTP para o serviço de conversão de texto em fala.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Defina a chave de assinatura e crie um prompt para o TTS

Nas próximas seções, você criará métodos para manipular a autorização, chamar a API de conversão de texto em fala e validar a resposta. Vamos começar criando uma classe. É onde colocaremos nossos métodos para troca de tokens e chamaremos a API text-to-speech.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

A `subscription_key` é sua chave exclusiva do portal do Azure.

## <a name="get-an-access-token"></a>Obter um token de acesso

Esse ponto de extremidade requer um token de acesso para autenticação. Para obter um token de acesso, é necessária uma troca. Este exemplo troca sua chave de assinatura do serviço de Fala por um token de acesso usando o ponto de extremidade `issueToken`.

Este exemplo pressupõe que a sua assinatura do serviço de Fala esteja na região Oeste dos EUA. Se você estiver usando uma região diferente, atualize o valor para `fetch_token_url`. Para uma lista completa, consulte [Regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copie este código para a classe `GetVoices`:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Para obter mais informações sobre autenticação, consulte [Autenticar com um token de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Faça uma solicitação e salve a resposta

Aqui você criará a solicitação e salvará a lista de vozes retornadas. Primeiro, você precisa definir `base_url` e `path`. Este exemplo supõe que você esteja usando o endpoint do West US. Se o seu recurso estiver registrado em uma região diferente, atualize o `base_url`. Para obter mais informações, confira [Regiões do serviço de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, adicione cabeçalhos obrigatórios para a solicitação. Finalmente, você fará uma solicitação ao serviço. Se a solicitação for bem-sucedida e um código de status 200 for retornado, a resposta será gravada no arquivo.

Copie este código para a classe `GetVoices`:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

Você está quase lá. Se a solicitação for bem-sucedida e um código de status 200 for retornado, a resposta de fala será gravada em um arquivo com registro de data e hora.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

E, pronto, você já pode executar o exemplo. Na linha de comando (ou sessão de terminal), navegue até o diretório do projeto e execute:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Limpar os recursos

Remova todas as informações confidenciais do código-fonte do seu aplicativo de exemplo, como as chaves de assinatura.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar exemplos do Python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Confira também

* [Referência de API de texto em fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar fontes de voz personalizadas](how-to-customize-voice-font.md)
* [Grave amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
