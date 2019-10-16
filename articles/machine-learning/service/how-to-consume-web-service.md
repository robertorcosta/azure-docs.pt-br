---
title: Criar o cliente para consumir o serviço Web implantado
titleSuffix: Azure Machine Learning
description: Saiba como consumir um serviço Web que foi gerado quando um modelo foi implantado com o modelo do Azure Machine Learning. O serviço web expõe uma API REST. Crie clientes para essa API usando a linguagem de programação de sua escolha.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: fda6c72504a75d600931185e224bb46db03e23ed
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374302"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Consumir um modelo de Azure Machine Learning implantado como um serviço web

A implantação de um modelo do Azure Machine Learning como um serviço da Web cria uma API REST. Você pode enviar dados para essa API e receber a previsão retornada pelo modelo. Neste documento, saiba como criar clientes para o serviço da Web usando C#, Go, Java e Python.

Você cria um serviço Web ao implantar uma imagem nas instâncias de contêiner do Azure, no serviço kubernetes do Azure ou em FPGA (matrizes de porta programável por campo). Você cria imagens de modelos registrados e arquivos de pontuação. Você recuperar o URI usado para acessar um serviço web usando o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Se a autenticação estiver habilitada, você também poderá usar o SDK para obter as chaves de autenticação ou os tokens.

O fluxo de trabalho geral para a criação de um cliente que usa um serviço web machine learning é:

1. Use o SDK para obter as informações de conexão.
1. Determinar o tipo de dados de solicitação usados pelo modelo.
1. Crie um aplicativo que chame o serviço da web.

> [!TIP]
> Os exemplos neste documento são criados manualmente sem o uso de especificações de OpenAPI (Swagger). Se você habilitou uma especificação OpenAPI para sua implantação, poderá usar ferramentas como o [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen) para criar bibliotecas de cliente para seu serviço.

## <a name="connection-information"></a>informações de conexão

> [!NOTE]
> Use o SDK do Azure Machine Learning para obter as informações do serviço Web. Esse é um SDK de Python. Você pode usar qualquer linguagem para criar um cliente para o serviço.

A classe [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) fornece as informações necessárias para criar um cliente. As seguintes propriedades `Webservice` que são úteis para criar um aplicativo cliente:

* `auth_enabled`-se a autenticação de chave estiver habilitada, `True`; caso contrário, `False`.
* `token_auth_enabled`-se a autenticação de token estiver habilitada, `True`; caso contrário, `False`.
* `scoring_uri` -O endereço da API REST.
* `swagger_uri`-o endereço da especificação OpenAPI. Esse URI estará disponível se você tiver habilitado a geração de esquema automática. Para obter mais informações, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md#schema).

Existem três maneiras de recuperar essas informações para serviços da Web implementados:

* Quando você implanta um modelo, um objeto `Webservice` é retornado com informações sobre o serviço:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Você pode usar `Webservice.list` para recuperar uma lista de serviços da Web implementados para modelos em seu workspace. Você pode adicionar filtros para restringir a lista de informações retornadas. Para obter mais informações sobre o que pode ser filtrado, consulte a documentação de referência do [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Se você souber o nome do serviço implantado, você pode criar uma nova instância de `Webservice` e fornecer o nome do workspace e o serviço como parâmetros. O novo objeto contém informações sobre o serviço implantado.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="authentication-for-services"></a>Autenticação para serviços

O Azure Machine Learning fornece duas maneiras de controlar o acesso aos serviços Web.

|Método de autenticação|ACI|AKS|
|---|---|---|
|Chave|Desabilitado por padrão| Habilitado por padrão|
|restrição| Não disponível| Desabilitado por padrão |

Ao enviar uma solicitação para um serviço protegido com uma chave ou token, use o cabeçalho de __autorização__ para passar a chave ou o token. A chave ou o token deve ser formatado como `Bearer <key-or-token>`, em que `<key-or-token>` é a chave ou o valor do token.

#### <a name="authentication-with-keys"></a>Autenticação com chaves

Ao habilitar a autenticação para uma implantação, você cria automaticamente as chaves de autenticação.

* A autenticação é ativada por padrão ao implantar no Serviço de Kubernetes do Azure.
* A autenticação é desabilitada por padrão durante a implantação nas Instâncias de Contêiner do Azure.

Para controlar a autenticação, use o parâmetro `auth_enabled` ao criar ou atualizar uma implantação.

Se a autenticação estiver ativada, você poderá usar o método `get_keys` para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se você precisar regenerar uma chave, use [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Autenticação com tokens

Quando você habilita a autenticação de token para um serviço Web, um usuário deve fornecer um Azure Machine Learning token JWT para o serviço Web para acessá-lo. 

* A autenticação de token é desabilitada por padrão quando você está implantando no serviço kubernetes do Azure.
* Não há suporte para autenticação de token quando você está implantando em instâncias de contêiner do Azure.

Para controlar a autenticação de tokens, use o parâmetro `token_auth_enabled` ao criar ou atualizar uma implantação.

Se a autenticação de token estiver habilitada, você poderá usar o método `get_token` para recuperar um token de portador e o tempo de expiração dos tokens:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Você precisará solicitar um novo token após o tempo `refresh_by` do token. 

## <a name="request-data"></a>Dados de solicitação

A API REST espera que o corpo da solicitação seja um documento JSON com a seguinte estrutura:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> A estrutura dos dados precisa corresponder ao esperado pelo script e modelo de pontuação no serviço. O script de pontuação pode modificar os dados antes de transmiti-lo ao modelo.

Por exemplo, o modelo na [Train dentro do bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) exemplo espera uma matriz de números de 10. O script de pontuação deste exemplo cria uma matriz Numpy da solicitação e a transmite para o modelo. O exemplo a seguir mostra os dados espera que esse serviço:

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
```

O serviço da web pode aceitar vários conjuntos de dados em uma solicitação. Ele retorna um documento JSON contendo uma matriz de respostas.

### <a name="binary-data"></a>Dados binários

Para obter informações sobre como habilitar o suporte para dados binários em seu serviço, consulte [dados binários](how-to-deploy-and-where.md#binary).

### <a name="cross-origin-resource-sharing-cors"></a>CORS (compartilhamento de recursos entre origens)

Para obter informações sobre como habilitar o suporte a CORS em seu serviço, consulte [compartilhamento de recursos entre origens](how-to-deploy-and-where.md#cors).

## <a name="call-the-service-c"></a>Chamar o serviço (C#)

Este exemplo demonstra como usar o C # para chamar o serviço da Web criado a partir do exemplo [Treinar no caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Os resultados retornados são semelhantes ao seguinte documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Chamar o serviço (Ir)

Este exemplo demonstra como usar o recurso Go para chamar o serviço da Web criado a partir do exemplo [Train dentro do notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Os resultados retornados são semelhantes ao seguinte documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Ligue para o serviço (Java)

Este exemplo demonstra como usar o Java para chamar o serviço da Web criado a partir do exemplo [Trainar no notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Os resultados retornados são semelhantes ao seguinte documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Chamar o serviço (Python)

Este exemplo demonstra como usar o Python para chamar o serviço da Web criado a partir do exemplo [Traino no notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Os resultados retornados são semelhantes ao seguinte documento JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Consumir o serviço de Power BI

O Power BI dá suporte ao consumo de serviços Web Azure Machine Learning para enriquecer os dados em Power BI com previsões. 

Para gerar um serviço Web com suporte para consumo no Power BI, o esquema deve dar suporte ao formato exigido pelo Power BI. [Saiba como criar um esquema com suporte a Power bi](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#example-entry-script).

Depois que o serviço Web for implantado, ele será consumível de Power BI fluxos de os. [Saiba como consumir um serviço web Azure Machine Learning do Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Próximos passos

Para exibir uma arquitetura de referência para a pontuação em tempo real dos modelos de aprendizado profundo e Python, vá para o [centro de arquitetura do Azure](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
