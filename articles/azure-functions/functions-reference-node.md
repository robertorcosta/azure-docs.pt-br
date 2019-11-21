---
title: JavaScript developer reference for Azure Functions
description: Entenda como desenvolver funções usando JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: b6b7db4c5f13a264b76dcab02dba51c464297307
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226716"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guia do desenvolvedor de JavaScript do Azure Functions

Este guia contém informações sobre as complexidades de escrever Azure Functions com JavaScript.

Uma função JavaScript é um `function` exportado que é executado quando acionado ([acionadores são configurados em function.json](functions-triggers-bindings.md)). The first argument passed to every function is a `context` object, which is used for receiving and sending binding data, logging, and communicating with the runtime.

Este artigo pressupõe que você já tenha lido a [Referência do desenvolvedor do Azure Functions](functions-reference.md). Complete the Functions quickstart to create your first function, using [Visual Studio Code](functions-create-first-function-vs-code.md) or [in the portal](functions-create-first-azure-function.md).

This article also supports [TypeScript app development](#typescript).

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas necessárias para um projeto JavaScript é semelhante à seguinte. Este padrão pode ser alterado. Para mais informações, consulte a seção [scriptArquivo](#using-scriptfile) abaixo.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

Na raiz do projeto, há um arquivo [host.json](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função tem uma pasta com seu próprio arquivo de código (.js) e o arquivo de configuração de associação (function.json). O nome do diretório pai de `function.json` é sempre o nome da sua função.

As extensões de associação necessárias na [versão 2.x](functions-versions.md) do runtime do Functions são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você precisa [registrar as extensões de associação](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

## <a name="exporting-a-function"></a>Exportando uma função

As funções JavaScript precisam ser exportadas por meio de [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (ou [`exports`](https://nodejs.org/api/modules.html#modules_exports)). Sua função exportada deve ser uma função JavaScript que é executada quando acionada.

Por padrão, o runtime de Funções procura sua função em `index.js`, onde `index.js` compartilha o mesmo diretório pai que o `function.json` correspondente. No caso padrão, sua função exportada deve ser a única exportação de seu arquivo ou a exportação denominada `run` ou `index`. Para configurar o local do arquivo e o nome de exportação da função, leia [Configurando o ponto de entrada da função](functions-reference-node.md#configure-function-entry-point) abaixo.

Sua função exportada é passada um número de argumentos na execução. O primeiro argumento é sempre um objeto `context`. If your function is synchronous (doesn't return a Promise), you must pass the `context` object, as calling `context.done` is required for correct use.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportando uma função assíncrona
Ao usar a declaração [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) ou o JavaScript simples [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) na versão 2.x do runtime Functions, não é necessário chamar explicitamente o [`context.done`](#contextdone-method) retorno de chamada para sinalizar que sua função foi concluída. Sua função é concluída quando a função assíncrona exportada / Promise é concluída. Para funções que visam o runtime da versão 1.x, você ainda deve chamar [`context.done`](#contextdone-method) quando seu código terminar de ser executado.

O exemplo a seguir é uma função simples que registra que foi acionada e imediatamente conclui a execução.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Ao exportar uma função assíncrona, você também pode configurar uma ligação de saída para obter o valor `return`. Isso é recomendado se você tiver apenas uma associação de saída.

Para atribuir uma saída usando `return`, altere o `name` propriedade a ser `$return` em `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

Nesse caso, sua função deve se parecer com o seguinte exemplo:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Associações 
Em JavaScript, [ligações](functions-triggers-bindings.md) são configuradas e definidas na função function.json. As funções interagem com as ligações de várias maneiras.

### <a name="inputs"></a>Entradas
As entradas são divididas em duas categorias no Azure Functions: uma é a entrada de gatilho e a outra é a entrada adicional. Trigger e outras ligações de entrada (ligações de `direction === "in"`) podem ser lidas por uma função de três maneiras:
 - **_[Recomendado]_ Como parâmetros passados para sua função.** Eles são passados para a função na mesma ordem em que são definidos *function.json*. The `name` property defined in *function.json* does not need to match the name of your parameter, although it should.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Como os membros de [`context.bindings`](#contextbindings-property) objeto.** Observe que a propriedade `name` definida em *function.json* não precisa corresponder ao nome do seu parâmetro, embora deva....
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Como entradas usando o [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx)objeto JavaScript.** Isso é essencialmente o mesmo que passar entradas como parâmetros, mas permite que você manipule dinamicamente entradas.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>outputs
As saídas (ligações de `direction === "out"`) podem ser gravadas por uma função de várias maneiras. Em todos os casos, a propriedade `name` da ligação, conforme definido em *function.json*, corresponde ao nome do membro do objeto gravado na sua função. 

You can assign data to output bindings in one of the following ways (don't combine these methods):

- **_[Recomendado para várias saídas]_ Retornando um objeto.** If you are using an async/Promise returning function, you can return an object with assigned output data. No exemplo abaixo, as ligações de saída são nomeadas "httpResponse" e "queueOutput" em *function.json*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Se você estiver usando uma função síncrona, você pode retornar este objeto usando [`context.done`](#contextdone-method) (veja o exemplo).
- **_[Recomendado para saída única]_ Retornando um valor diretamente e usando o nome de ligação $ return.** Isso funciona apenas para as funções de retorno assíncrono / Promessa. Veja o exemplo em [exportando uma função assíncrona](#exporting-an-async-function). 
- **Atribuindo valores a serem `context.bindings`**  você pode atribuir valores diretamente para Context. Bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Tipo de dados de associações

Para definir o tipo de dados para uma associação de entrada, use a propriedade `dataType` na definição de associação. Por exemplo, para ler o conteúdo de uma solicitação HTTP em formato binário, use o tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

As opções para `dataType` são: `binary`, `stream` e `string`.

## <a name="context-object"></a>objeto de contexto
O runtime usa um objeto `context` para passar dados de/para sua função e permitir que você se comunique com o runtime. O objeto de contexto pode ser usado para ler e definir os dados de associações, gravar logs e usando o `context.done` retorno de chamada quando a função exportada é síncrona.

O `context` objeto é sempre o primeiro parâmetro para uma função. Deve ser incluído porque tem métodos importantes, como `context.done` e `context.log`. Você pode nomear o objeto de acordo com a sua preferência (por exemplo, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Propriedade context.bindings

```js
context.bindings
```

Returns a named object that is used to read or assign binding data. Input and trigger binding data can be accessed by reading properties on `context.bindings`. Output binding data can be assigned by adding data to `context.bindings`

Por exemplo, as seguintes definições de ligação em sua função.json permitem acessar o conteúdo de uma fila de `context.bindings.myInput`e atribuir saídas a uma fila usando`context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Você pode optar por definir os dados de ligação de saída usando o método `context.done` em vez do objeto `context.binding` (veja abaixo).

### <a name="contextbindingdata-property"></a>Propriedade context.bindingData

```js
context.bindingData
```

Retorna um objeto nomeado que contém dados de invocação de função e os metadados do gatilho (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Para obter um exemplo de metadados de gatilho, confira este [exemplo de hubs de eventos](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Método context.done

```js
context.done([err],[propertyBag])
```

Permite que o runtime saiba que seu código foi concluído. Quando sua função usa a declaração [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function), você não precisa usar`context.done()`. O `context.done` retorno de chamada é chamado implicitamente. As funções assíncronas estão disponíveis no Nó 8 ou em uma versão posterior, que requer a versão 2.x do runtime de Funções.

Se a função não é uma função assíncrona, **você precisa chamar**`context.done` para informar ao runtime que a função está concluída. Os tempos de execução se está ausente.

O método `context.done` permite que você retorne um erro definido pelo usuário ao runtime e um objeto JSON que contém dados de associação de saída. As propriedades transmitidas para `context.done`sobrescrevem qualquer coisa definida no objeto`context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Método context.log  

```js
context.log(message)
```

Permite que você grave em logs de função de streaming no nível de rastreamento padrão. No `context.log`, há métodos de registro adicionais disponíveis para permitir que você grave logs de função em outros níveis de rastreamento:


| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Grava no registro em log no nível do erro, ou em um nível inferior.   |
| **warn(_message_)**    | Grava no registro em log no nível do aviso, ou em um nível inferior. |
| **info(_message_)**    | Grava no registro em log no nível da informação, ou em um nível inferior.    |
| **verbose(_message_)** | Grava no registro em log no nível detalhado.           |

O exemplo a seguir grava um log no nível de rastreamento de aviso:

```javascript
context.log.warn("Something has happened."); 
```

Você pode [configurar o limite do nível de rastreamento para registro em log](#configure-the-trace-level-for-console-logging) no arquivo host.json. Para obter mais informações sobre como gravar logs, confira [Gravando saídas de rastreamento](#writing-trace-output-to-the-console) abaixo.

Leia [Monitorado o Azure Functions](functions-monitoring.md) para saber mais sobre como exibir e consultar logs de função.

## <a name="writing-trace-output-to-the-console"></a>Gravar a saída de rastreamento no console 

No Functions, use os métodos `context.log` para gravar a saída de rastreamento no console. No Functions v2.x, as saídas de rastreio usando `console.log` são capturadas no nível do Aplicativo Function. This means that outputs from `console.log` are not tied to a specific function invocation and aren't displayed in a specific function's logs. Eles, no entanto, se propagam para o Application Insights. No Functions v1.x, não é possível usar `console.log` para gravar no console.

Quando você chama `context.log()`, sua mensagem é gravada no console no nível de rastreamento padrão, que é o nível de rastreamento de _informações_. O código a seguir grava no console no nível de rastreamento de informações:

```javascript
context.log({hello: 'world'});  
```

Esse código é equivalente ao código acima:

```javascript
context.log.info({hello: 'world'});  
```

Esse código grava no console no nível de erro:

```javascript
context.log.error("An error has occurred.");  
```

Como _erro_ é o nível de rastreamento mais alto, esse rastreamento é gravado na saída em todos os níveis de rastreamento enquanto o registro em log estiver habilitado.

Todos os métodos `context.log` dão suporte ao mesmo formato de parâmetro que o [método util.format](https://nodejs.org/api/util.html#util_util_format_format) de Node.js. Considere o código a seguir, que grava logs de função usando o nível de rastreamento padrão:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Você também pode escrever o mesmo código no formato a seguir:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurar o nível de rastreamento para o registro em log no console

O Functions 1.x permite a definição do nível de rastreamento de limite para gravar no console, o que facilita o controle do modo de gravação dos rastreamentos no console da sua função. Para definir o limite para todos os rastreamentos gravados no console, use a propriedade `tracing.consoleLevel` no arquivo host.json. Essa configuração se aplica a todas as funções em seu aplicativo de função. O exemplo a seguir define o limite de rastreamento para habilitar o registro em log detalhado:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Os valores de **consoleLevel** correspondem aos nomes dos métodos `context.log`. Para desabilitar todo o registro em log do rastreamento no console, defina **consoleLevel** como _off_. Para obter mais informações, consulte a [referência para host.json](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Gatilhos e associações HTTP

HTTP e gatilhos de webhook e associações de saída HTTP usam objetos de solicitação e resposta para representar as mensagens HTTP.  

### <a name="request-object"></a>Objeto da solicitação

O objeto `context.req` (solicitação) tem as seguintes propriedades:

| Propriedade      | Descrição                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Um objeto que contém o corpo da solicitação.               |
| _headers_     | Um objeto que contém os cabeçalhos da solicitação.                   |
| _method_      | O método HTTP da solicitação.                                |
| _originalUrl_ | A URL da solicitação.                                        |
| _params_      | Um objeto que contém os parâmetros de roteamento da solicitação. |
| _query_       | Um objeto que contém os parâmetros da consulta.                  |
| _rawBody_     | O corpo da mensagem como uma cadeia de caracteres.                           |


### <a name="response-object"></a>Objeto de resposta

O objeto `context.res` (resposta) tem as seguintes propriedades:

| Propriedade  | Descrição                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Um objeto que contém o corpo da resposta.         |
| _headers_ | Um objeto que contém os cabeçalhos da resposta.             |
| _isRaw_   | Indica que a formatação foi ignorada para a resposta.    |
| _status_  | O código de status HTTP da resposta.                     |

### <a name="accessing-the-request-and-response"></a>Acessar a solicitação e a resposta 

Ao trabalhar com gatilhos HTTP, há várias maneiras de acessar os objetos de solicitação e resposta HTTP:

+ **Partir `req` e `res` propriedades no `context` objeto.** Dessa forma, você pode usar o padrão convencional para acessar os dados HTTP a partir do objeto de contexto, em vez de usar o padrão `context.bindings.name` completo. O exemplo a seguir mostra como acessar os objetos `req` e `res` no `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Na entrada nomeada e associações de saída.** Dessa forma, o gatilho e as associações de HTTP funcionam da mesma forma que qualquer outra associação. O exemplo a seguir define o objeto de resposta usando uma associação chamada `response`: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Somente Resposta]_ Chamando`context.res.send(body?: any)`.** Uma resposta HTTP é criada com a entrada `body` como o corpo da resposta. `context.done()` é chamado implicitamente.

+ **_[Somente Resposta]_ Chamando`context.done()`.** A special type of HTTP binding returns the response that is passed to the `context.done()` method. A seguinte associação de saída HTTP define um parâmetro de saída `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Versão do nó

A tabela a seguir mostra a versão do Node.js usada por cada versão principal do runtime do Functions:

| Versão do Functions | Versão do Node.js | 
|---|---|
| 1.x | 6.11.2 (bloqueada pelo runtime) |
| 2. x  | _Active LTS_ and _Maintenance LTS_ Node.js versions (~10 recommended). Target the version in Azure by setting the WEBSITE_NODE_DEFAULT_VERSION [app setting](functions-how-to-use-azure-function-app-settings.md#settings) to `~10`.|

Veja versão atual que o runtime está usando verificando a configuração de aplicativo acima ou imprimindo `process.version` de qualquer função.

## <a name="dependency-management"></a>Gerenciamento de dependências
Para usar as bibliotecas da comunidade no código JavaScript, como é mostrado no exemplo abaixo, você precisa garantir que todas as dependências sejam instaladas no aplicativo de funções no Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Você deve definir um arquivo `package.json` na raiz do seu aplicativo de função. A definição de arquivo permite que todas as funções no aplicativo compartilhem os mesmos pacotes armazenados em cache, o que oferece o melhor desempenho. Se houver conflitos de versão, você poderá resolver o conflito adicionando um arquivo `package.json` na pasta de uma função específica.  

Ao implantar os aplicativos de função do controle de origem, qualquer arquivo `package.json` presente em seu repositório acionará um `npm install` em sua pasta durante a implantação. Mas ao implantar via Portal ou CLI, você terá que instalar manualmente os pacotes.

Há duas maneiras de instalar pacotes no aplicativo de funções: 

### <a name="deploying-with-dependencies"></a>Implantando com dependências
1. Instale todos os pacotes necessários localmente executando `npm install`.

2. Implante o código e verifique se a pasta `node_modules` está incluída na implantação. 


### <a name="using-kudu"></a>Usando o Kudu
1. Vá para `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **Console de Depuração** > **CMD**.

3. Acesse `D:\home\site\wwwroot`e arraste o arquivo package.json para a pasta **wwwroot** na metade superior da página.  
    Também há outras maneiras de carregar arquivos em seu aplicativo de função. Para saber mais, confira [Como atualizar os arquivos do aplicativo de função](functions-reference.md#fileupdate). 

4. Depois que o arquivo package.json é carregado, execute o comando `npm install` no **console de execução remota do Kudu**.  
    Essa ação baixa os pacotes indicados no arquivo package.json e reinicia o aplicativo de função.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [configurações do aplicativo](functions-app-settings.md), como conexão de serviço cadeias de caracteres, são expostas como variáveis de ambiente durante a execução. You can access these settings using `process.env`, as shown here in the second and third calls to `context.log()` where we log the `AzureWebJobsStorage` and `WEBSITE_SITE_NAME` environment variables:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as configurações do aplicativo são lidos a partir de [Settings](functions-run-local.md#local-settings-file) arquivo de projeto.

## <a name="configure-function-entry-point"></a>Configurar o ponto de entrada de função

As propriedades `scriptFile` e `entryPoint` do `function.json` podem ser usadas para configurar o local e o nome da função exportada. Essas propriedades podem ser importantes quando seu JavaScript é transferido.

### <a name="using-scriptfile"></a>Usando o `scriptFile`

Por padrão, uma função JavaScript é executada do `index.js`, um arquivo que compartilha o mesmo diretório pai que seu `function.json` correspondente.

`scriptFile` pode ser usado para obter uma estrutura de pastas semelhante ao exemplo a seguir:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

O `function.json` da `myNodeFunction` deve incluir uma propriedade `scriptFile` que aponte para o arquivo com a função exportada a ser executada.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Usando o `entryPoint`

No `scriptFile` (ou `index.js`), uma função precisa ser exportada usando `module.exports` para ser localizada e executada. Por padrão, a função que é executada quando disparada é a única exportação desse arquivo, a exportação denominada `run` ou a exportação denominada `index`.

Isso pode ser configurado usando `entryPoint` em `function.json`, como no exemplo a seguir:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

No Functions v2.x, que suporta o parâmetro `this` nas funções do usuário, o código da função poderia ser como no seguinte exemplo:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

In this example, it is important to note that although an object is being exported, there are no guarantees for preserving state between executions.

## <a name="local-debugging"></a>Local Debugging

When started with the `--inspect` parameter, a Node.js process listens for a debugging client on the specified port. In Azure Functions 2.x, you can specify arguments to pass into the Node.js process that runs your code by adding the environment variable or App Setting `languageWorkers:node:arguments = <args>`. 

To debug locally, add `"languageWorkers:node:arguments": "--inspect=5858"` under `Values` in your [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) file and attach a debugger to port 5858.

When debugging using VS Code, the `--inspect` parameter is automatically added using the `port` value in the project's launch.json file.

In version 1.x, setting `languageWorkers:node:arguments` will not work. The debug port can be selected with the [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parameter on Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

When you target version 2.x of the Functions runtime, both [Azure Functions for Visual Studio Code](functions-create-first-function-vs-code.md) and the [Azure Functions Core Tools](functions-run-local.md) let you create function apps using a template that support TypeScript function app projects. The template generates `package.json` and `tsconfig.json` project files that make it easier to transpile, run, and publish JavaScript functions from TypeScript code with these tools.

A generated `.funcignore` file is used to indicate which files are excluded when a project is published to Azure.  

TypeScript files (.ts) are transpiled into JavaScript files (.js) in the `dist` output directory. TypeScript templates use the [`scriptFile` parameter](#using-scriptfile) in `function.json` to indicate the location of the corresponding .js file in the `dist` folder. The output location is set by the template by using `outDir` parameter in the `tsconfig.json` file. If you change this setting or the name of the folder, the runtime is not able to find the code to run.

> [!NOTE]
> Experimental support for TypeScript exists version 1.x of the Functions runtime. The experimental version transpiles TypeScript files into JavaScript files when the function is invoked. In version 2.x, this experimental support has been superseded by the tool-driven method that does transpilation before the host is initialized and during the deployment process.

The way that you locally develop and deploy from a TypeScript project depends on your development tool.

### <a name="visual-studio-code"></a>Código do Visual Studio

The [Azure Functions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extension lets you develop your functions using TypeScript. The Core Tools is a requirement of the Azure Functions extension.

To create a TypeScript function app in Visual Studio Code, choose `TypeScript` as your language when you create a function app.

When you press **F5** to run the app locally, transpilation is done before the host (func.exe) is initialized. 

When you deploy your function app to Azure using the **Deploy to function app...** button, the Azure Functions extension first generates a production-ready build of JavaScript files from the TypeScript source files.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

There are several ways in which a TypeScript project differs from a JavaScript project when using the Core Tools.

#### <a name="create-project"></a>Criar projeto

To create a TypeScript function app project using Core Tools, you must specify the TypeScript language option when you create your function app. You can do this in one of the following ways:

- Run the `func init` command, select `node` as your language stack, and then select `typescript`.

- Execute o comando `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Run local

To run your function app code locally using Core Tools, use the following commands instead of `func host start`: 

```command
npm install
npm start
```

The `npm start` command is equivalent to the following commands:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publicar no Azure

Before you use the [`func azure functionapp publish`] command to deploy to Azure, you create a production-ready build of JavaScript files from the TypeScript source files. 

The following commands prepare and publish your TypeScript project using Core Tools: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

In this command, replace `<APP_NAME>` with the name of your function app.

## <a name="considerations-for-javascript-functions"></a>Considerações para funções em JavaScript

Ao trabalhar com funções JavaScript, lembre-se das considerações nas seções a seguir.

### <a name="choose-single-vcpu-app-service-plans"></a>Escolher Planos do Serviço de Aplicativo de vCPU único

Ao criar um aplicativo de funções que usa o Plano do Serviço de Aplicativo, recomendamos que você selecione um plano de vCPU único em vez de um plano com vários vCPUs. Atualmente, o Functions executa funções em JavaScript com mais eficiência em VMs de vCPU único, e o uso de VMs maiores não produz os aprimoramentos de desempenho esperados. When necessary, you can manually scale out by adding more single-vCPU VM instances, or you can enable autoscale. Para obter mais informações, consulte [Dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Inicialização a frio

No desenvolvimento de Azure Functions no modelo de hospedagem sem servidor, as inicializações a frio são uma realidade. *Partida a frio* refere-se ao fato de que, quando seu aplicativo de função é iniciado pela primeira vez após um período de inatividade, leva mais tempo para inicializar. Para funções JavaScript com árvores de dependência grandes em particular, o cold start pode ser significativo. Para acelerar o processo de inicialização a frio, [execute suas funções como um arquivo de pacote](run-functions-from-deployment-package.md) quando possível. Muitos métodos de implantação usam a execução do modelo de pacote por padrão, mas se você estiver experimentando grandes reinicializações a frio e não estiver sendo executado dessa maneira, essa alteração poderá oferecer uma melhoria significativa.

### <a name="connection-limits"></a>Connection Limits

When you use a service-specific client in an Azure Functions application, don't create a new client with every function invocation. Instead, create a single, static client in the global scope. For more information, see [managing connections in Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Use `async` and `await`

When writing Azure Functions in JavaScript, you should write code using the `async` and `await` keywords. Writing code using `async` and `await` instead of callbacks or `.then` and `.catch` with Promises helps avoid two common problems:
 - Throwing uncaught exceptions that [crash the Node.js process](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), potentially affecting the execution of other functions.
 - Unexpected behavior, such as missing logs from context.log, caused by asynchronous calls that are not properly awaited.

In the example below, the asynchronous method `fs.readFile` is invoked with an error-first callback function as its second parameter. This code causes both of the issues mentioned above. An exception that is not explicitly caught in the correct scope crashed the entire process (issue #1). Calling `context.done()` outside of the scope of the callback function means that the function invocation may end before the file is read (issue #2). In this example, calling `context.done()` too early results in missing log entries starting with `Data from file:`.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Using the `async` and `await` keywords helps avoid both of these errors. You should use the Node.js utility function [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) to turn error-first callback-style functions into awaitable functions.

In the example below, any unhandled exceptions thrown during the function execution only fail the individual invocation that raised an exception. The `await` keyword means that steps following `readFileAsync` only execute after `readFile` is complete. With `async` and `await`, you also don't need to call the `context.done()` callback.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Próximos passos

Para saber mais, consulte os recursos a seguir:

+ [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
+ [Referência do desenvolvedor do Azure Functions](functions-reference.md)
+ [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)

[`func azure functionapp publish`]: functions-run-local.md#project-file-deployment
