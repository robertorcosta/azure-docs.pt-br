---
title: Criar cliente para modelo implantado como serviço web
titleSuffix: Azure Machine Learning
description: Aprenda a chamar um ponto final de serviço web que foi gerado quando um modelo foi implantado a partir do Azure Machine Learning. O ponto final expõe uma API REST, que você pode chamar para realizar inferência com o modelo. Crie clientes para essa API usando a linguagem de programação de sua escolha.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 0222b63323c4e546628d790fabb881eba006494e
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383385"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Consumir um modelo de Azure Machine Learning implantado como um serviço web
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A implantação de um modelo de Aprendizado de Máquina do Azure como serviço web cria um ponto final da API REST. Você pode enviar dados para este ponto final e receber a previsão devolvida pelo modelo. Neste documento, saiba como criar clientes para o serviço da Web usando C#, Go, Java e Python.

Você cria um serviço web quando implanta um modelo no ambiente local, instâncias de contêiner do Azure, serviço Azure Kubernetes ou matrizes de portão programáveis em campo (FPGA). Você recupera o URI usado para acessar o serviço web usando o [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Se a autenticação estiver ativada, você também poderá usar o SDK para obter as chaves de autenticação ou tokens.

O fluxo de trabalho geral para a criação de um cliente que usa um serviço web machine learning é:

1. Use o SDK para obter as informações de conexão.
1. Determinar o tipo de dados de solicitação usados pelo modelo.
1. Crie um aplicativo que chame o serviço da web.

> [!TIP]
> Os exemplos neste documento são criados manualmente sem o uso de especificações OpenAPI (Swagger). Se você habilitou uma especificação OpenAPI para sua implantação, você pode usar ferramentas como [swagger-codegen](https://github.com/swagger-api/swagger-codegen) para criar bibliotecas de clientes para o seu serviço.

## <a name="connection-information"></a>informações de conexão

> [!NOTE]
> Use o SDK do Azure Machine Learning para obter as informações do serviço Web. Esse é um SDK de Python. Você pode usar qualquer linguagem para criar um cliente para o serviço.

A classe [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) fornece as informações necessárias para criar um cliente. As seguintes propriedades `Webservice` que são úteis para criar um aplicativo cliente:

* `auth_enabled`- Se a autenticação `True`da chave estiver habilitada; caso contrário, `False`.
* `token_auth_enabled`- Se a autenticação do `True`token estiver habilitada; caso contrário, `False`.
* `scoring_uri` -O endereço da API REST.
* `swagger_uri`- O endereço da especificação OpenAPI. Este URI está disponível se você habilitou a geração automática de esquemas. Para obter mais informações, consulte [Implantar modelos com O Azure Machine Learning](how-to-deploy-and-where.md).

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

### <a name="secured-web-service"></a>Serviço web protegido

Se você garantiu o serviço web implantado usando um certificado TLS/SSL, você pode usar [HTTPS](https://en.wikipedia.org/wiki/HTTPS) para se conectar ao serviço usando o URI de pontuação ou swagger. O HTTPS ajuda a proteger as comunicações entre um cliente e um serviço web, criptografando as comunicações entre os dois. A criptografia usa [TLS (Transport Layer Security, segurança de camada de transporte)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS às vezes ainda é referido como *Secure Sockets Layer* (SSL), que foi o antecessor do TLS.

> [!IMPORTANT]
> Os serviços web implantados pelo Azure Machine Learning só suportam a versão 1.2 do TLS. Ao criar um aplicativo cliente, certifique-se de que ele suporta essa versão.

Para obter mais informações, consulte [Usar TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Autenticação para serviços

O Azure Machine Learning fornece duas maneiras de controlar o acesso aos seus serviços web.

|Método de autenticação|Aci|AKS|
|---|---|---|
|Chave|Desabilitado por padrão| Habilitado por padrão|
|Token| Não disponível| Desabilitado por padrão |

Ao enviar uma solicitação a um serviço que é protegido com uma chave ou token, use o cabeçalho __autorização__ para passar a chave ou o token. A chave ou o token `Bearer <key-or-token>`devem `<key-or-token>` ser formatados como , onde está sua chave ou valor de token.

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
> Se você precisar regenerar uma [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)chave, use .

#### <a name="authentication-with-tokens"></a>Autenticação com tokens

Quando você habilita a autenticação de token para um serviço web, um usuário deve fornecer um token JWT de aprendizado de máquina do Azure para o serviço web para acessá-lo. 

* A autenticação do token é desativada por padrão quando você está implantando no Azure Kubernetes Service.
* A autenticação do token não é suportada quando você está implantando no Azure Container Instances.

Para controlar a autenticação `token_auth_enabled` do token, use o parâmetro quando estiver criando ou atualizando uma implantação.

Se a autenticação do token estiver `get_token` ativada, você poderá usar o método para recuperar um token do portador e esse tempo de expiração dos tokens:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Você precisará solicitar um novo token após `refresh_by` o tempo do token. 

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

Para obter informações sobre como habilitar o suporte a dados binários em seu serviço, consulte [dados binários](how-to-deploy-and-where.md#binary).

> [!TIP]
> A ativação do suporte para dados binários acontece no arquivo score.py usado pelo modelo implantado. A partir do cliente, use a funcionalidade HTTP da sua linguagem de programação. Por exemplo, o seguinte trecho envia o conteúdo de um arquivo JPG para um serviço web:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Compartilhamento de recursos de origem cruzada (CORS)

Para obter informações sobre como habilitar o suporte ao CORS em seu serviço, consulte [Compartilhamento de recursos de origem cruzada](how-to-deploy-and-where.md#cors).

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

## <a name="consume-the-service-from-power-bi"></a>Consumir o serviço usando o Power BI

O Power BI suporta o consumo de serviços web de Machine Learning do Azure para enriquecer os dados no Power BI com previsões. 

Para gerar um serviço web suportado para consumo em Power BI, o esquema deve suportar o formato exigido pelo Power BI. [Aprenda a criar um esquema suportado pelo Power BI](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script).

Uma vez que o serviço web é implantado, ele é consumível a partir de fluxos de dados do Power BI. [Aprenda a consumir um serviço web de Machine Learning do Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Próximas etapas

Para ver uma arquitetura de referência para pontuação em tempo real de Python e modelos de aprendizagem profunda, vá para o [centro de arquitetura Azure](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
