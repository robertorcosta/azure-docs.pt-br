---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: ff4c33aea3d3ce604f44c38e6e3856242388b0e9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371734"
---
Use a biblioteca de clientes de previsão de LUIS (Reconhecimento vocal) para Python com o objetivo de:

* Obter previsão por slot
* Obter previsão por versão

[Documentação de referência](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Pacote do runtime de previsão (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [ Exemplos](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Prerequisites

* Conta do portal do LUIS (Reconhecimento Vocal) – [Crie uma gratuitamente](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configurando

### <a name="get-your-language-understanding-luis-runtime-key"></a>Obtenha sua chave de runtime do LUIS (Reconhecimento vocal)

Obtenha sua [chave de runtime](../luis-how-to-azure-subscription.md) criando um recurso de runtime do LUIS. Mantenha sua chave e o ponto de extremidade da chave para a próxima etapa.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Criar um arquivo Python

Crie um arquivo Python no IDE ou no editor de sua preferência, chamado `prediction_quickstart.py`.

### <a name="install-the-sdk"></a>Instalar o SDK

Dentro do diretório do aplicativo, instale a biblioteca de clientes de runtime de previsão do LUIS (Reconhecimento vocal) para Python com o seguinte comando:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Modelo de objeto

O cliente de runtime de previsão do LUIS (Reconhecimento Vocal) é um objeto [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) que se autentica no Azure, que contém sua chave de recurso.

Depois que o cliente for criado, use-o para acessar a funcionalidade, incluindo:

* Previsão por [slot de preparo ou produção](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Previsão por [versão](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como fazer o seguinte com a biblioteca de clientes de runtime de previsão do LUIS (Reconhecimento vocal) para Python:

* [Previsão por slot](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Adicionar as dependências

No diretório do projeto, abra o arquivo `prediction_quickstart.py` no IDE ou no editor de sua preferência. Adicione as seguintes dependências:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

1. Crie variáveis para suas informações necessárias do LUIS:

    Adicione variáveis para gerenciar sua chave de previsão extraída de uma variável de ambiente chamada `LUIS_RUNTIME_KEY`. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e recarregar o editor, o IDE ou o shell em execução para acessar a variável. Os métodos serão criados posteriormente.

    Crie uma variável para conter o nome do recurso `LUIS_RUNTIME_ENDPOINT`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Crie uma variável para a ID do aplicativo como uma variável de ambiente chamada `LUIS_APP_ID`. Defina a variável de ambiente para o aplicativo de IoT público, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Crie uma variável para definir o slot publicado `production`.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Crie um objeto de credenciais com sua chave e use-o com o ponto de extremidade para criar um objeto [LUISRuntimeClientConfiguration]https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python().

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Obter uma previsão do runtime

Adicione o método a seguir para criar a solicitação para o runtime de previsão.

O enunciado do usuário faz parte do objeto [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python).

O método **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** precisa de vários parâmetros, como a ID do aplicativo, o nome do slot e o objeto de solicitação de previsão, para atender à solicitação. As outras opções, como detalhada, mostram todas as intenções e log são opcionais. A solicitação retorna um objeto [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python).

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Código principal para a previsão

Use o método principal a seguir para vincular as variáveis e os métodos para obter a previsão.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `python prediction_quickstart.py` do seu próprio diretório de aplicativo.

```console
python prediction_quickstart.py
```

O console de início rápido exibe a saída:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Ao concluir suas previsões, limpe o trabalho deste início rápido excluindo o arquivo e os subdiretórios dele.
