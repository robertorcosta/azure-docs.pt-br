---
title: Azure Functions manipuladores personalizados (versão prévia)
description: Aprenda a usar Azure Functions com qualquer linguagem ou versão de tempo de execução.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479248"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions manipuladores personalizados (versão prévia)

Cada aplicativo de funções é executado por um manipulador específico de idioma. Embora Azure Functions ofereça suporte a muitos [manipuladores de linguagem](./supported-languages.md) por padrão, há casos em que você pode desejar ter controle adicional sobre o ambiente de execução do aplicativo. Os manipuladores personalizados oferecem esse controle adicional.

Os manipuladores personalizados são servidores Web leves que recebem eventos do host do functions. Qualquer linguagem que ofereça suporte a primitivos HTTP pode implementar um manipulador personalizado.

Os manipuladores personalizados são mais adequados para situações em que você deseja:

- Implemente um aplicativo de funções em uma linguagem além dos idiomas oficialmente suportados
- Implementar um aplicativo de funções em uma versão de idioma ou tempo de execução sem suporte por padrão
- Ter controle granular sobre o ambiente de execução do aplicativo

Com manipuladores personalizados, todos os [gatilhos e associações de entrada e saída](./functions-triggers-bindings.md) têm suporte por meio de [pacotes de extensão](./functions-bindings-register.md).

## <a name="overview"></a>Visão geral

O diagrama a seguir mostra a relação entre o host do Functions e um servidor Web implementado como um manipulador personalizado.

![Visão geral do manipulador personalizado de Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Eventos disparam uma solicitação enviada ao host do functions. O evento transporta um conteúdo HTTP bruto (para funções disparadas por HTTP sem associações) ou uma carga que contém dados de associação de entrada para a função.
- O host do Functions hospeda a solicitação para o servidor Web emitindo uma [carga de solicitação](#request-payload).
- O servidor Web executa a função individual e retorna uma carga de [resposta](#response-payload) para o host de funções.
- As funções hospedam os proxies da resposta como uma carga de associação de saída para o destino.

Um aplicativo Azure Functions implementado como um manipulador personalizado deve configurar os arquivos *host. JSON* e *Function. JSON* de acordo com algumas convenções.

## <a name="application-structure"></a>Estrutura de aplicativo

Para implementar um manipulador personalizado, você precisa dos seguintes aspectos para seu aplicativo:

- Um arquivo *host. JSON* na raiz do seu aplicativo
- Um arquivo *Function. JSON* para cada função (dentro de uma pasta que corresponde ao nome da função)
- Um comando, script ou executável, que executa um servidor Web

O diagrama a seguir mostra como esses arquivos examinam o sistema de arquivos em busca de uma função chamada "Order".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuração

O aplicativo é configurado por meio do arquivo *host. JSON* . Esse arquivo informa ao host do Functions onde enviar solicitações apontando para um servidor Web capaz de processar eventos HTTP.

Um manipulador personalizado é definido pela configuração do arquivo *host. JSON* com detalhes sobre como executar o servidor Web por meio da `httpWorker` seção.

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

A `httpWorker` seção aponta para um destino, conforme definido pelo `defaultExecutablePath`. O destino de execução pode ser um comando, executável ou arquivo no qual o servidor Web é implementado.

Para aplicativos com script, `defaultExecutablePath` aponta para o tempo de execução da linguagem `defaultWorkerPath` de script e aponta para o local do arquivo de script. O exemplo a seguir mostra como um aplicativo JavaScript no node. js é configurado como um manipulador personalizado.

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

Você também pode passar argumentos usando a `arguments` matriz:

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

Os argumentos são necessários para muitas configurações de depuração. Consulte a seção [depuração](#debugging) para obter mais detalhes.

> [!NOTE]
> O arquivo *host. JSON* deve estar no mesmo nível na estrutura de diretório que o servidor Web em execução. Alguns idiomas e cadeias podem não posicionar o arquivo na raiz do aplicativo por padrão.

#### <a name="bindings-support"></a>Suporte a associações

Os gatilhos padrão juntamente com as associações de entrada e saída estão disponíveis por meio da referência de [grupos de extensão](./functions-bindings-register.md) no arquivo *host. JSON* .

### <a name="function-metadata"></a>Metadados de função

Quando usado com um manipulador personalizado, o conteúdo *Function. JSON* não é diferente de como você definiria uma função em qualquer outro contexto. O único requisito é que os arquivos *Function. JSON* devem estar em uma pasta chamada para corresponder ao nome da função.

### <a name="request-payload"></a>Carga de solicitação

A carga de solicitação para funções HTTP puras é a carga de solicitação HTTP bruta. As funções HTTP puras são definidas como funções sem associações de entrada ou saída, que retornam uma resposta HTTP.

Qualquer outro tipo de função que inclua associações de entrada, saída ou é disparado por meio de uma origem de evento diferente de HTTP tem uma carga de solicitação personalizada.

O código a seguir representa um exemplo de carga de solicitação. A carga inclui uma estrutura JSON com dois membros: `Data` e `Metadata`.

O `Data` membro inclui chaves que correspondem a nomes de entrada e de gatilho conforme definido na matriz bindings no arquivo *Function. JSON* .

O `Metadata` membro inclui [metadados gerados da origem do evento](./functions-bindings-expressions-patterns.md#trigger-metadata).

Dadas as associações definidas no seguinte arquivo *Function. JSON* :

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

Um conteúdo de solicitação semelhante a este exemplo é retornado:

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

Por convenção, as respostas de função são formatadas como pares de chave/valor. As chaves com suporte incluem:

| <nobr>Chave de carga</nobr>   | Tipo de dados | Comentários                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Mantém valores de resposta, conforme definido `bindings` pela matriz, o arquivo *Function. JSON* .<br /><br />Por exemplo, se uma função for configurada com uma associação de saída de armazenamento de BLOBs `Outputs` chamada "blob" `blob`, ela conterá uma chave chamada, que é definida como o valor do blob. |
| `Logs`        | matriz     | As mensagens aparecem nos logs de invocação de funções.<br /><br />Ao executar no Azure, as mensagens aparecem no Application Insights. |
| `ReturnValue` | cadeia de caracteres    | Usado para fornecer uma resposta quando uma saída é configurada como `$return` no arquivo *Function. JSON* . |

Consulte o [exemplo de uma carga de exemplo](#bindings-implementation).

## <a name="examples"></a>Exemplos

Os manipuladores personalizados podem ser implementados em qualquer linguagem que dê suporte a eventos HTTP. Embora Azure Functions [suporte total ao JavaScript e ao node. js](./functions-reference-node.md), os exemplos a seguir mostram como implementar um manipulador personalizado usando JavaScript no node. js para fins de instrução.

> [!TIP]
> Embora seja um guia para aprender a implementar um manipulador personalizado em outras linguagens, os exemplos baseados em node. js mostrados aqui também podem ser úteis se você quisesse executar um aplicativo do Functions em uma versão sem suporte do node. js.

## <a name="http-only-function"></a>Função somente HTTP

O exemplo a seguir demonstra como configurar uma função disparada por HTTP sem associações ou saídas adicionais. O cenário implementado neste exemplo apresenta uma função chamada `http` que aceita um `GET` ou. `POST`

O trecho a seguir representa como uma solicitação para a função é composta.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementação

Em uma pasta chamada *http*, o arquivo *Function. JSON* configura a função disparada por http.

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

A função é configurada para `GET` aceitar `POST` as solicitações e e o valor do resultado é fornecido por `res`meio de um argumento chamado.

Na raiz do aplicativo, o arquivo *host. JSON* é configurado para executar o Node. js e apontar o `server.js` arquivo.

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

O arquivo *Server. js* do arquivo implementa um servidor Web e uma função http.

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

Neste exemplo, o Express é usado para criar um servidor Web para manipular eventos HTTP e é definido para escutar solicitações por meio do `FUNCTIONS_HTTPWORKER_PORT`.

A função é definida no caminho de `/hello`. `GET`as solicitações são tratadas retornando um objeto JSON simples `POST` , e as solicitações têm acesso ao corpo `req.body`da solicitação via.

A rota para a função Order aqui é `/hello` e não `/api/hello` porque o host do Functions está contratando o proxy da solicitação para o manipulador personalizado.

>[!NOTE]
>O `FUNCTIONS_HTTPWORKER_PORT` não é a porta voltada para o público usada para chamar a função. Essa porta é usada pelo host do Functions para chamar o manipulador personalizado.

## <a name="function-with-bindings"></a>Função com associações

O cenário implementado neste exemplo apresenta uma função chamada `order` que aceita um `POST` com uma carga que representa uma ordem de produto. Como um pedido é Postado na função, uma mensagem de armazenamento de fila é criada e uma resposta HTTP é retornada.

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

Em uma pasta chamada *Order*, o arquivo *Function. JSON* configura a função disparada por http.

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

Essa função é definida como uma [função disparada por http](./functions-bindings-http-webhook-trigger.md) que retorna uma [resposta http](./functions-bindings-http-webhook-output.md) e gera uma mensagem de [armazenamento de fila](./functions-bindings-storage-queue-output.md) .

Na raiz do aplicativo, o arquivo *host. JSON* é configurado para executar o Node. js e apontar o `server.js` arquivo.

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

O arquivo *Server. js* do arquivo implementa um servidor Web e uma função http.

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

Neste exemplo, o Express é usado para criar um servidor Web para manipular eventos HTTP e é definido para escutar solicitações por meio do `FUNCTIONS_HTTPWORKER_PORT`.

A função é definida no caminho de `/order` .  A rota para a função Order aqui é `/order` e não `/api/order` porque o host do Functions está contratando o proxy da solicitação para o manipulador personalizado.

À `POST` medida que as solicitações são enviadas para essa função, os dados são expostos por meio de alguns pontos:

- O corpo da solicitação está disponível por meio de`req.body`
- Os dados postados na função estão disponíveis por meio de`req.body.Data.req.Body`

A resposta da função é formatada em um par chave/valor em que `Outputs` o membro mantém um valor JSON em que as chaves correspondem às saídas, conforme definido no arquivo *Function. JSON* .

Ao definir `message` igual à mensagem que veio da solicitação e `res` para a resposta http esperada, essa função gera uma mensagem para o armazenamento de fila e retorna uma resposta http.

## <a name="debugging"></a>Depuração

Para depurar o aplicativo de manipulador personalizado functions, você precisa adicionar argumentos apropriados para a linguagem e o tempo de execução para habilitar a depuração.

Por exemplo, para depurar um aplicativo node. js, o `--inspect` sinalizador é passado como um argumento no arquivo *host. JSON* .

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
> A configuração de depuração faz parte do seu arquivo *host. JSON* , o que significa que talvez seja necessário remover alguns argumentos antes de implantá-los na produção.

Com essa configuração, você pode iniciar o processo de host da função usando o seguinte comando:

```bash
func host start
```

Depois que o processo for iniciado, você poderá anexar um depurador e pontos de interrupção de acesso.

### <a name="visual-studio-code"></a>Visual Studio Code

O exemplo a seguir é uma configuração de exemplo que demonstra como você pode configurar o arquivo *Launch. JSON* para conectar seu aplicativo ao depurador de Visual Studio Code.

Este exemplo é para node. js, portanto, talvez seja necessário alterar este exemplo para outros idiomas ou tempos de execução.

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

Um manipulador personalizado pode ser implantado em quase todos os Azure Functions opção de hospedagem (consulte [restrições](#restrictions)). Se o seu manipulador exigir dependências personalizadas (como um tempo de execução de linguagem), talvez seja necessário usar um [contêiner personalizado](./functions-create-function-linux-custom-image.md).

## <a name="restrictions"></a>Restrições

- Não há suporte para manipuladores personalizados em planos de consumo do Linux.
- O servidor Web precisa começar dentro de 60 segundos.

## <a name="samples"></a>Exemplos

Consulte o [repositório do GitHub de exemplos de manipulador personalizado](https://github.com/Azure-Samples/functions-custom-handlers) para obter exemplos de como implementar funções em uma variedade de idiomas diferentes.
