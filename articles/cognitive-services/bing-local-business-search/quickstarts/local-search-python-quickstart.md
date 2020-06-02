---
title: Início Rápido – enviar uma consulta para a API no Python – Pesquisa de Negócios Locais do Bing
titleSuffix: Azure Cognitive Services
description: Use este início rápido para começar a usar a API de Pesquisa de Negócios Locais do Bing em Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: 3a90d5455c0664ceabf80647fc94a37ad0c716b5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873019"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Início Rápido: Enviar uma consulta para a API de Pesquisa de Empresas Locais do Bing no Python

Use este início rápido para aprender a enviar solicitações para a API de Pesquisa de Negócios Locais do Bing, que é um Serviço Cognitivo do Azure. Embora esse aplicativo simples seja escrito em Python, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer solicitações HTTP e analisar JSON.

Este aplicativo de exemplo obtém dados de resposta local da API para uma consulta de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

* [Python](https://www.python.org/) 2.x ou 3.x.
* Uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com APIs de Pesquisa do Bing. Para este início rápido, a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente. Salve a chave de API fornecida quando você ativa sua avaliação gratuita. Para obter mais informações, confira [Preço dos Serviços Cognitivos – API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="run-the-complete-application"></a>Execute o aplicativo concluído

O seguinte exemplo obtém os resultados localizados, que são implementados nas seguintes etapas:
1. Declare variáveis para especificar o ponto de extremidade por host e caminho.
2. Especifique o parâmetro de consulta. 
3. Defina a função de pesquisa que cria a solicitação e adiciona o cabeçalho `Ocp-Apim-Subscription-Key`.
4. Defina o cabeçalho `Ocp-Apim-Subscription-Key`. 
5. Faça a conexão e envie a solicitação.
6. Imprima os resultados JSON.

O código completo dessa demonstração é a seguinte:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Próximas etapas
- [Início Rápido da Pesquisa Java em empresas locais](local-search-java-quickstart.md)
- [Início rápido do C# de Pesquisa de negócios local](local-quickstart.md)
- [Início rápido do Node.js de Pesquisa de Negócios Locais](local-search-node-quickstart.md)
