---
title: Azure Funções manipuladores personalizados (visualização)
description: Aprenda a usar funções do Azure com qualquer idioma ou versão em tempo de execução.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479248"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Funções manipuladores personalizados (visualização)

Cada aplicativo Functions é executado por um manipulador específico do idioma. Embora o Azure Functions suporte muitos [manipuladores de idiomas](./supported-languages.md) por padrão, há casos em que você pode querer controle adicional sobre o ambiente de execução do aplicativo. Os manipuladores personalizados dão-lhe este controle adicional.

Os manipuladores personalizados são servidores web leves que recebem eventos do host Functions. Qualquer linguagem que suporte os primitivos HTTP pode implementar um manipulador personalizado.

Os manipuladores personalizados são mais adequados para situações em que você deseja:

- Implementar um aplicativo Functions em um idioma além dos idiomas oficialmente suportados
- Implementar um aplicativo Functions em uma versão de idioma ou tempo de execução não suportado por padrão
- Tenha controle granular sobre o ambiente de execução do aplicativo

Com manipuladores personalizados, todos os [gatilhos e ligações de entrada e saída](./functions-triggers-bindings.md) são suportados por [pacotes de extensão](./functions-bindings-register.md).

## <a name="overview"></a>Visão geral

O diagrama a seguir mostra a relação entre o host Functions e um servidor web implementado como um manipulador personalizado.

![Visão geral do manipulador personalizado do Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Os eventos desencadeiam uma solicitação enviada ao host Functions. O evento carrega uma carga HTTP bruta (para funções acionadas por HTTP sem vinculações) ou uma carga que contém dados de ligação de entrada para a função.
- O host Functions então proxies a solicitação para o servidor web, emitindo uma [carga útil de solicitação](#request-payload).
- O servidor web executa a função individual e retorna uma [carga de resposta](#response-payload) ao host Funções.
- O host Functions proxies a resposta como uma carga de ligação de saída ao alvo.

Um aplicativo Azure Functions implementado como um manipulador personalizado deve configurar os arquivos *host.json* e *function.json* de acordo com algumas convenções.

## <a name="application-structure"></a>Estrutura de aplicativo

Para implementar um manipulador personalizado, você precisa dos seguintes aspectos para o seu aplicativo:

- Um arquivo *host.json* na raiz do seu aplicativo
- Um arquivo *function.json* para cada função (dentro de uma pasta que corresponde ao nome da função)
- Um comando, script ou executável, que executa um servidor web

O diagrama a seguir mostra como esses arquivos ficam no sistema de arquivos para uma função chamada "ordem".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuração

O aplicativo é configurado através do arquivo *host.json.* Este arquivo informa ao host Funções onde enviar solicitações apontando para um servidor web capaz de processar eventos HTTP.

Um manipulador personalizado é definido configurando o arquivo *host.json* com detalhes sobre como executar o servidor web através da seção. `httpWorker`

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

A `httpWorker` seção aponta para um `defaultExecutablePath`alvo definido pelo . O alvo de execução pode ser um comando, executável ou arquivo onde o servidor web é implementado.

Para aplicativos roteirizados, `defaultExecutablePath` aponta para `defaultWorkerPath` o tempo de execução do idioma do script e aponta para a localização do arquivo de script. O exemplo a seguir mostra como um aplicativo JavaScript no Node.js é configurado como um manipulador personalizado.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Você também pode passar `arguments` argumentos usando a matriz:

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Os argumentos são necessários para muitas configurações de depuração. Consulte a seção [Depuração](#debugging) para obter mais detalhes.

> [!NOTE]
> O arquivo *host.json* deve estar no mesmo nível na estrutura do diretório que o servidor web em execução. Alguns idiomas e cadeias de ferramentas podem não colocar esse arquivo na raiz do aplicativo por padrão.

#### <a name="bindings-support"></a>Suporte a vinculações

Gatilhos padrão, juntamente com as vinculações de entrada e saída, estão disponíveis fazendo referência a [pacotes de extensão](./functions-bindings-register.md) no arquivo *host.json.*

### <a name="function-metadata"></a>Metadados de função

Quando usado com um manipulador personalizado, o conteúdo *function.json* não é diferente de como você definiria uma função em qualquer outro contexto. O único requisito é que os arquivos *function.json* devem estar em uma pasta nomeada para corresponder ao nome da função.

### <a name="request-payload"></a>Solicitar carga útil

A carga útil de solicitação para funções HTTP puras é a carga útil de solicitação HTTP bruta. As funções HTTP puras são definidas como funções sem ligações de entrada ou saída, que retornam uma resposta HTTP.

Qualquer outro tipo de função que inclua entrada, vinculações de saída ou é acionada através de uma fonte de evento diferente do HTTP tem uma carga de solicitação personalizada.

O código a seguir representa uma carga útil de solicitação de amostra. A carga inclui uma estrutura JSON `Data` com `Metadata`dois membros: e .

O `Data` membro inclui teclas que correspondem a nomes de entrada e de gatilho, conforme definido na matriz de vinculações no arquivo *function.json.*

O `Metadata` membro inclui [metadados gerados a partir da fonte do evento](./functions-bindings-expressions-patterns.md#trigger-metadata).

Dadas as vinculações definidas no seguinte arquivo *function.json:*

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Uma carga útil de solicitação semelhante a este exemplo é devolvida:

```json
{
    "Data": {
        "myQueueItem": "{ message: \"Message sent\" }"
    },
    "Metadata": {
        "DequeueCount": 1,
        "ExpirationTime": "2019-10-16T17:58:31+00:00",
        "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
        "InsertionTime": "2019-10-09T17:58:31+00:00",
        "NextVisibleTime": "2019-10-09T18:08:32+00:00",
        "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
        "sys": {
            "MethodName": "QueueTrigger",
            "UtcNow": "2019-10-09T17:58:32.2205399Z",
            "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
        }
    }
}
```

### <a name="response-payload"></a>Carga de resposta

Por convenção, as respostas de função são formatadas como pares de chave/valor. As chaves suportadas incluem:

| <nobr>Chave de carga</nobr>   | Tipo de dados | Comentários                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Mantém os valores `bindings` de resposta definidos pela matriz do arquivo *function.json.*<br /><br />Por exemplo, se uma função for configurada com uma vinculação `Outputs` de saída `blob`de armazenamento blob chamada "blob", então contém uma chave nomeada , que é definida como o valor da bolha. |
| `Logs`        | matriz     | As mensagens aparecem nos registros de invocação de Funções.<br /><br />Ao ser executado no Azure, as mensagens aparecem no Application Insights. |
| `ReturnValue` | string    | Usado para fornecer uma resposta quando `$return` uma saída é configurada como no arquivo *function.json.* |

Veja o [exemplo de uma carga útil amostral](#bindings-implementation).

## <a name="examples"></a>Exemplos

Os manipuladores personalizados podem ser implementados em qualquer idioma que suporte eventos HTTP. Enquanto as funções do Azure [suportam totalmente JavaScript e Node.js,](./functions-reference-node.md)os exemplos a seguir mostram como implementar um manipulador personalizado usando JavaScript no Node.js para fins de instrução.

> [!TIP]
> Embora seja um guia para aprender como implementar um manipulador personalizado em outros idiomas, os exemplos baseados no Node.js mostrados aqui também podem ser úteis se você quiser executar um aplicativo Functions em uma versão não suportada do Node.js.

## <a name="http-only-function"></a>Função somente HTTP

O exemplo a seguir demonstra como configurar uma função acionada pelo HTTP sem ligações ou saídas adicionais. O cenário implementado neste exemplo `http` apresenta uma `GET` função `POST` nomeada que aceita a ou .

O trecho a seguir representa como uma solicitação à função é composta.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementação

Em uma pasta chamada *http,* o arquivo *function.json* configura a função acionada pelo HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

A função é configurada `GET` `POST` para aceitar tanto e solicitações e `res`o valor de resultado é fornecido através de um argumento chamado .

Na raiz do aplicativo, o arquivo *host.json* é configurado para executar `server.js` Node.js e apontar o arquivo.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

O arquivo *server.js* implementa um servidor web e uma função HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

Neste exemplo, o Express é usado para criar um servidor web para `FUNCTIONS_HTTPWORKER_PORT`lidar com eventos HTTP e está definido para ouvir solicitações através do .

A função é definida `/hello`no caminho de . `GET`as solicitações são tratadas retornando um `POST` simples objeto JSON, `req.body`e as solicitações têm acesso ao órgão de solicitação via .

A rota para a `/hello` função `/api/hello` de pedido aqui é e não porque o host Funções está proxying a solicitação para o manipulador personalizado.

>[!NOTE]
>O `FUNCTIONS_HTTPWORKER_PORT` não é o porta de frente para o público usado para chamar a função. Esta porta é usada pelo host Funções para chamar o manipulador personalizado.

## <a name="function-with-bindings"></a>Função com ligações

O cenário implementado neste exemplo `order` apresenta uma `POST` função nomeada que aceita um com uma carga representando um pedido de produto. À medida que um pedido é postado na função, uma mensagem de armazenamento na fila é criada e uma resposta HTTP é retornada.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Implementação

Em uma pasta chamada *ordem,* o arquivo *function.json* configura a função acionada pelo HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}

```

Esta função é definida como uma [função acionada HTTP](./functions-bindings-http-webhook-trigger.md) que retorna uma resposta [HTTP](./functions-bindings-http-webhook-output.md) e produz uma mensagem de armazenamento [na Fila.](./functions-bindings-storage-queue-output.md)

Na raiz do aplicativo, o arquivo *host.json* é configurado para executar `server.js` Node.js e apontar o arquivo.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

O arquivo *server.js* implementa um servidor web e uma função HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

Neste exemplo, o Express é usado para criar um servidor web para `FUNCTIONS_HTTPWORKER_PORT`lidar com eventos HTTP e está definido para ouvir solicitações através do .

A função é definida `/order` no caminho de .  A rota para a `/order` função `/api/order` de pedido aqui é e não porque o host Funções está proxying a solicitação para o manipulador personalizado.

À `POST` medida que as solicitações são enviadas para esta função, os dados são expostos através de alguns pontos:

- O órgão de solicitação está disponível via`req.body`
- Os dados postados na função estão disponíveis via`req.body.Data.req.Body`

A resposta da função é formatada em `Outputs` um par de teclas/valores onde o membro mantém um valor JSON onde as teclas correspondem às saídas definidas no arquivo *function.json.*

Ao `message` definir igual à mensagem que veio `res` da solicitação e à resposta HTTP esperada, esta função produz uma mensagem para o Queue Storage e retorna uma resposta HTTP.

## <a name="debugging"></a>Depuração

Para depurar seu aplicativo de manipulador personalizado functions, você precisa adicionar argumentos apropriados para o idioma e tempo de execução para ativar a depuração.

Por exemplo, para depurar um aplicativo `--inspect` Node.js, o sinalizador é passado como um argumento no arquivo *host.json.*

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> A configuração de depuração faz parte do arquivo *host.json,* o que significa que você pode precisar remover alguns argumentos antes de implantar na produção.

Com esta configuração, você pode iniciar o processo host da função usando o seguinte comando:

```bash
func host start
```

Uma vez iniciado o processo, você pode anexar um depurador e acertar pontos de interrupção.

### <a name="visual-studio-code"></a>Visual Studio Code

O exemplo a seguir é uma configuração de exemplo que demonstra como você pode configurar seu arquivo *launch.json* para conectar seu aplicativo ao depurador visual studio code.

Este exemplo é para Node.js, então você pode ter que alterar este exemplo para outros idiomas ou tempos de execução.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Implantando

Um manipulador personalizado pode ser implantado em quase todas as opções de hospedagem funções do Azure (ver [restrições](#restrictions)). Se o manipulador precisar de dependências personalizadas (como um tempo de execução do idioma), talvez seja necessário usar um [recipiente personalizado](./functions-create-function-linux-custom-image.md).

## <a name="restrictions"></a>Restrições

- Os manipuladores personalizados não são suportados em planos de consumo Linux.
- O servidor web precisa começar dentro de 60 segundos.

## <a name="samples"></a>Exemplos

Consulte o [manipulador personalizado amostras GitHub repo](https://github.com/Azure-Samples/functions-custom-handlers) para exemplos de como implementar funções em uma variedade de idiomas diferentes.
