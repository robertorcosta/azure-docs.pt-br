---
title: Referência de desenvolvedor do Python para o Azure Functions
description: Saiba como desenvolver funções usando Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: ea128fc7c68b49fc14d796e9a3b91a9dbddd9b26
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780038"
---
# <a name="azure-functions-python-developer-guide"></a>Guia do desenvolvedor de Python para o Azure Functions

Este artigo é uma introdução ao desenvolvimento do Azure Functions usando Python. O conteúdo abaixo pressupõe que você já tenha lido o [Guia de desenvolvedores do Azure Functions](functions-reference.md). 

Para projetos de exemplo de função autônoma no Python, consulte os [exemplos de funções do Python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Modelo de programação

Azure Functions espera que uma função seja um método sem estado em seu script Python que processa a entrada e produz a saída. Por padrão, o tempo de execução espera que o método seja implementado como um método `main()` global chamado `__init__.py` no arquivo. Você também pode [especificar um ponto de entrada alternativo](#alternate-entry-point).

Os dados de gatilhos e associações são associados à função por meio de atributos de `name` método usando a propriedade definida no arquivo *Function. JSON* . Por exemplo, a _Function. JSON_ abaixo descreve uma função simples disparada por uma solicitação `req`http denominada:

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Com base nessa definição, o `__init__.py` arquivo que contém o código da função pode ser semelhante ao exemplo a seguir:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Você também pode declarar explicitamente os tipos de atributo e o tipo de retorno na função usando anotações do tipo Python. Isso ajuda você a usar o IntelliSense e os recursos de preenchimento automático fornecidos por muitos editores de código Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Use as anotações do Python incluídas no pacote [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) para associar a entrada e as saídas a seus métodos.

## <a name="alternate-entry-point"></a>Ponto de entrada alternativo

Você pode alterar o comportamento padrão de uma função especificando opcionalmente as `scriptFile` Propriedades e `entryPoint` no arquivo *Function. JSON* . Por exemplo, a _função. JSON_ a seguir informa o tempo de execução `customentry()` para usar o método no arquivo _Main.py_ , como o ponto de entrada para sua função do Azure.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas recomendada para um projeto de funções do Python é semelhante ao exemplo a seguir:

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 | - Dockerfile
 tests
```
A pasta principal do projeto\_\_(\_\_aplicativo) pode conter os seguintes arquivos:

* *local. Settings. JSON*: usado para armazenar configurações de aplicativo e cadeias de conexão ao executar localmente. Esse arquivo não é publicado no Azure. Para saber mais, consulte [local. Settings. File](functions-run-local.md#local-settings-file).
* *requirements. txt*: contém a lista de pacotes que o sistema instala ao publicar no Azure.
* *host. JSON*: contém opções de configuração global que afetam todas as funções em um aplicativo de funções. Esse arquivo é publicado do Azure. Nem todas as opções têm suporte ao serem executadas localmente. Para saber mais, consulte [host. JSON](functions-host-json.md).
* *. funcignore*: (opcional) declara os arquivos que não devem ser publicados no Azure.
* *. gitignore*: (opcional) declara os arquivos que são excluídos de um repositório git, como local. Settings. JSON.
* *Dockerfile*: (opcional) usado ao publicar seu projeto em um [contêiner personalizado](functions-create-function-linux-custom-image.md).

Cada função possui seu próprio arquivo de código e arquivo de configuração de associação (function.json). 

Ao implantar seu projeto em um aplicativo de funções no Azure, todo o conteúdo da pasta principal do projeto*\_\_(\_aplicativo*) deve ser incluído no pacote, mas não na própria pasta. É recomendável que você mantenha os testes em uma pasta separada da pasta do projeto, neste exemplo `tests`. Isso impede que você implante o código de teste com seu aplicativo. Para obter mais informações, consulte [testes de unidade](#unit-testing).

## <a name="import-behavior"></a>Comportamento de importação

Você pode importar módulos em seu código de função usando referências absolutas e relativas absolutos. Com base na estrutura de pastas mostrada acima, as importações a seguir funcionam de dentro * \_ \_do\_\_aplicativo\_de\_arquivo\\de\_função\_\_\Meus primeira função _ init. py*:

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

As importações a seguir *não funcionam* de dentro do mesmo arquivo:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

O código compartilhado deve ser mantido em uma pasta separada * \_ \_no\_aplicativo*. Para fazer referência a módulos na pasta de *código compartilhado\_* , você pode usar a seguinte sintaxe:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Gatilhos e entradas

As entradas são divididas em duas categorias no Azure Functions: entrada do gatilho e entrada adicional. Embora sejam diferentes no arquivo, `function.json` o uso é idêntico no código Python.  Cadeias de conexão ou segredos para fontes de entrada e gatilho são mapeados para valores no `local.settings.json` arquivo ao serem executados localmente e as configurações do aplicativo durante a execução no Azure. 

Por exemplo, o código a seguir demonstra a diferença entre os dois:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Quando a função é invocada, a solicitação HTTP é transmitida para a função como `req`. Uma entrada será recuperada do armazenamento de BLOBs do Azure com base na _ID_ na URL de rota e disponibilizada como `obj` no corpo da função.  Aqui, a conta de armazenamento especificada é a cadeia de conexão encontrada na configuração do aplicativo AzureWebJobsStorage, que é a mesma conta de armazenamento usada pelo aplicativo de funções.


## <a name="outputs"></a>outputs

A saída pode ser expressa em parâmetros de saída e em valores retornados. Se houver apenas uma saída, recomendamos usar o valor retornado. Para múltiplas saídas, você precisará usar parâmetros de saída.

Para usar o valor retornado de uma função como o valor de uma associação de saída, a propriedade `name` da associação deve ser definida como `$return` em `function.json`.

Para produzir várias saídas, use o `set()` método fornecido pela [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) interface para atribuir um valor à associação. Por exemplo, a função a seguir pode enviar uma mensagem para uma fila e também retornar uma resposta HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Registrando em log

O acesso ao agente de tempo de execução do Azure Functions está [`logging`](https://docs.python.org/3/library/logging.html#module-logging) disponível por meio de um manipulador raiz em seu aplicativo de funções. Esse agente é vinculado ao Application Insights e permite sinalizar avisos e erros encontrados durante a execução da função.

O exemplo a seguir registra uma mensagem de informações quando a função é invocada por meio de um gatilho HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Há outros métodos de registro em log disponíveis que permitem a gravação no console em níveis de rastreamento diferentes:

| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Grava uma mensagem com nível CRÍTICO no agente raiz.  |
| **`error(_message_)`**   | Grava uma mensagem com nível ERRO no agente raiz.    |
| **`warning(_message_)`**    | Grava uma mensagem com nível AVISO no agente raiz.  |
| **`info(_message_)`**    | Grava uma mensagem com nível INFORMAÇÕES no agente raiz.  |
| **`debug(_message_)`** | Grava uma mensagem com nível DEPURAR no agente raiz.  |

Para saber mais sobre registro em log, consulte [monitorar Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Gatilhos e associações HTTP

O gatilho HTTP é definido no arquivo function. Jon. O `name` da associação deve corresponder ao parâmetro nomeado na função. Nos exemplos anteriores, é usado um nome `req` de associação. Esse parâmetro é um objeto [HttpRequest] e um objeto [HttpResponse] é retornado.

No objeto [HttpRequest] , você pode obter cabeçalhos de solicitação, parâmetros de consulta, parâmetros de rota e o corpo da mensagem. 

O exemplo a seguir é do [modelo de gatilho http para Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

Nessa função, o valor do parâmetro de `name` consulta é obtido do `params` parâmetro do objeto [HttpRequest] . O corpo da mensagem codificada em JSON é lido `get_json` usando o método. 

Da mesma forma, você pode `status_code` definir `headers` e para a mensagem de resposta no objeto [HttpResponse] retornado.

## <a name="scaling-and-concurrency"></a>Dimensionamento e simultaneidade

Por padrão, o Azure Functions monitora automaticamente a carga em seu aplicativo e cria instâncias de host adicionais para Python, conforme necessário. O Functions usa limites internos (não configuráveis pelo usuário) para diferentes tipos de gatilhos para decidir quando adicionar instâncias, como a idade das mensagens e o tamanho da fila para QueueTrigger. Para obter mais informações, consulte [como funcionam os planos de consumo e Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Esse comportamento de dimensionamento é suficiente para muitos aplicativos. No entanto, os aplicativos com qualquer uma das seguintes características podem não ser dimensionados com eficiência:

- O aplicativo precisa lidar com muitas invocações simultâneas.
- O aplicativo processa um grande número de eventos de e/s.
- O aplicativo está associado à e/s.

Nesses casos, você pode melhorar ainda mais o desempenho empregando padrões assíncronos e usando vários processos de trabalho de linguagem.

### <a name="async"></a>Async

Como o Python é um tempo de execução de thread único, uma instância de host para Python pode processar apenas uma invocação de função por vez. Para aplicativos que processam um grande número de eventos de e/s e/ou são associados a e/s, você pode melhorar o desempenho executando funções de forma assíncrona.

Para executar uma função de forma assíncrona `async def` , use a instrução, que executa a função com [asyncio](https://docs.python.org/3/library/asyncio.html) diretamente:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Uma função sem a `async` palavra-chave é executada automaticamente em um pool de threads asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Usar vários processos de trabalho de idioma

Por padrão, cada instância de host do Functions tem um processo de trabalho de idioma único. Você pode aumentar o número de processos de trabalho por host (até 10) usando a configuração de aplicativo [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . Azure Functions, em seguida, tenta distribuir uniformemente invocações de função simultâneas entre esses trabalhos. 

O FUNCTIONS_WORKER_PROCESS_COUNT se aplica a cada host que o Functions cria ao escalar horizontalmente seu aplicativo para atender à demanda. 

## <a name="context"></a>Contexto

Para obter o contexto de invocação de uma função durante a execução, [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) inclua o argumento em sua assinatura. 

Por exemplo: 

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A classe de [**contexto**](/python/api/azure-functions/azure.functions.context?view=azure-python) tem os seguintes atributos de cadeia de caracteres:

`function_directory`  
O diretório no qual a função está em execução.

`function_name`  
Nome da função.

`invocation_id`  
ID da invocação de função atual.

## <a name="global-variables"></a>Variáveis globais

Não há garantia de que o estado do seu aplicativo será preservado para execuções futuras. No entanto, o tempo de execução de Azure Functions geralmente reutiliza o mesmo processo para várias execuções do mesmo aplicativo. Para armazenar em cache os resultados de uma computação cara, declare-o como uma variável global. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [as configurações de aplicativo](functions-app-settings.md), como cadeias de conexão de serviço, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações declarando `import os` e, em `setting = os.environ["setting-name"]`seguida, usando,.

O exemplo a seguir obtém a [configuração do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings), com a `myAppSetting`chave chamada:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Para o desenvolvimento local, as configurações do aplicativo são [mantidas no arquivo local. Settings. JSON](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Versão do Python 

O Azure Functions dá suporte às seguintes versões do Python:

| Versão do Functions | Versões<sup>*</sup> do Python |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2. x | 3.7<br/>3.6 |

<sup>*</sup>Distribuições oficiais do CPython

Para solicitar uma versão específica do Python ao criar seu aplicativo de funções no Azure, use `--runtime-version` a opção do [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) comando. A versão de tempo de execução do Functions é definida pela `--functions-version` opção. A versão do Python é definida quando o aplicativo de funções é criado e não pode ser alterado.  

Ao executar localmente, o tempo de execução usa a versão disponível do Python. 

## <a name="package-management"></a>Gerenciamento de pacotes

Ao desenvolver localmente usando o Azure Functions Core Tools ou o Visual Studio Code, adicione os nomes e as versões dos pacotes necessários para os arquivos `requirements.txt` e instale-os usando `pip`. 

Por exemplo, o arquivo de requisitos e comando pip a seguir podem ser usados para instalar o pacote `requests` do PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publicando no Azure

Quando você estiver pronto para publicar, verifique se todas as suas dependências disponíveis publicamente estão listadas no arquivo Requirements. txt, localizado na raiz do diretório do projeto. 

Arquivos de projeto e pastas que são excluídos da publicação, incluindo a pasta de ambiente virtual, são listados no arquivo. funcignore.

Há três ações de compilação com suporte para publicar seu projeto Python no Azure:

+ Compilação remota: as dependências são obtidas remotamente com base no conteúdo do arquivo Requirements. txt. A [compilação remota](functions-deployment-technologies.md#remote-build) é o método de compilação recomendado. Remoto também é a opção de compilação padrão das ferramentas do Azure. 
+ Compilação local: as dependências são obtidas localmente com base no conteúdo do arquivo Requirements. txt. 
+ Dependências personalizadas: seu projeto usa pacotes não disponíveis publicamente para nossas ferramentas. (Requer o Docker.)

Para criar suas dependências e publicar usando um sistema de entrega contínua (CD), [use Azure pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Build remoto

Por padrão, o Azure Functions Core Tools solicita um Build remoto quando você usa o seguinte comando [Func Azure functionapp Publish](functions-run-local.md#publish) para publicar seu projeto Python no Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

A [extensão Azure Functions para Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) também solicita uma compilação remota por padrão. 

### <a name="local-build"></a>Build local

Você pode impedir fazer uma compilação remota usando o seguinte comando [Func functionapp Publish do Azure](functions-run-local.md#publish) para publicar com uma compilação local. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure. 

Usando a `--build local` opção, as dependências do projeto são lidas no arquivo Requirements. txt e esses pacotes dependentes são baixados e instalados localmente. Os arquivos e as dependências do projeto são implantados do computador local no Azure. Isso resulta em um pacote de implantação maior sendo carregado no Azure. Se, por algum motivo, as dependências no arquivo Requirements. txt não puderem ser adquiridas pelas ferramentas principais, você deverá usar a opção dependências personalizadas para publicação. 

### <a name="custom-dependencies"></a>Dependências personalizadas

Se seu projeto usa pacotes que não estão publicamente disponíveis para nossas ferramentas, você pode disponibilizá-los para seu aplicativo colocando- \_ \_os\_\_no diretório app/. python_packages. Antes de publicar, execute o seguinte comando para instalar as dependências localmente:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Ao usar dependências personalizadas, você deve usar `--no-build` a opção de publicação, já que já instalou as dependências.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

## <a name="unit-testing"></a>Teste de unidade

As funções escritas em Python podem ser testadas como outros códigos Python usando estruturas de teste padrão. Para a maioria das associações, é possível criar um objeto de entrada fictício criando uma instância de uma classe apropriada do `azure.functions` pacote. Como o [`azure.functions`](https://pypi.org/project/azure-functions/) pacote não está disponível imediatamente, certifique-se de instalá-lo `requirements.txt` por meio do arquivo, conforme descrito na seção [Gerenciamento de pacotes](#package-management) acima. 

Por exemplo, a seguir está um teste fictício de uma função disparada por HTTP:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# __app__/HttpTrigger/__init__.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Aqui está outro exemplo, com uma função disparada por fila:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```
## <a name="temporary-files"></a>Arquivos temporários

O `tempfile.gettempdir()` método retorna uma pasta temporária, que no Linux é `/tmp`. Seu aplicativo pode usar esse diretório para armazenar arquivos temporários gerados e usados por suas funções durante a execução. 

> [!IMPORTANT]
> Os arquivos gravados no diretório temporário não têm garantia de persistir entre invocações. Durante o scale out, os arquivos temporários não são compartilhados entre instâncias. 

O exemplo a seguir cria um arquivo temporário nomeado no diretório temporário (`/tmp`):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

Recomendamos que você mantenha os testes em uma pasta separada da pasta do projeto. Isso impede que você implante o código de teste com seu aplicativo. 

## <a name="cross-origin-resource-sharing"></a>Compartilhamento de recursos entre origens

O Azure Functions dá suporte ao compartilhamento de recursos entre origens (CORS). O CORS é configurado [no portal](functions-how-to-use-azure-function-app-settings.md#cors) e por meio do [CLI do Azure](/cli/azure/functionapp/cors). A lista de origens permitidas pelo CORS aplica-se ao nível do aplicativo de funções. Com o CORS habilitado, as respostas `Access-Control-Allow-Origin` incluem o cabeçalho. Para obter mais informações, consulte [Compartilhamento de recursos entre origens](functions-how-to-use-azure-function-app-settings.md#cors). 

O CORS tem suporte total para aplicativos de funções do Python.

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

Todos os problemas conhecidos e solicitações de recursos são controlados usando a lista [Problemas do GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se você tiver um problema e não for possível localizá-lo no GitHub, abra um novo problema e inclua uma descrição detalhada dele.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Documentação da API do pacote Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Práticas recomendadas do Azure Functions](functions-best-practices.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
* [Associações do armazenamento de blobs](functions-bindings-storage-blob.md)
* [Associações HTTP e webhook](functions-bindings-http-webhook.md)
* [Associações de Armazenamento de Filas](functions-bindings-storage-queue.md)
* [Gatilho de temporizador](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
