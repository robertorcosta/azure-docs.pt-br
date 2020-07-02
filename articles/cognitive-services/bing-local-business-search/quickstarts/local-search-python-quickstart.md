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
ms.custom: tracking-python
ms.openlocfilehash: c821df0e7cb00c73899a2694dd0b2eb6823b1d9e
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611189"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Início Rápido: Enviar uma consulta para a API de Pesquisa de Empresas Locais do Bing no Python

Use este início rápido para aprender a enviar solicitações para a API de Pesquisa de Negócios Locais do Bing, que é um Serviço Cognitivo do Azure. Embora esse aplicativo simples seja escrito em Python, a API é um serviço Web RESTful compatível com qualquer linguagem de programação capaz de fazer solicitações HTTP e analisar JSON.

Este aplicativo de exemplo obtém dados de resposta local da API para uma consulta de pesquisa.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2.x ou 3.x.
* Depois de obter a assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Criar um recurso de Pesquisa do Bing"  target="_blank">crie um recurso de Pesquisa do Bing <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.

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
