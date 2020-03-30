---
title: Referência de desenvolvedor do Python para o Azure Functions
description: Saiba como desenvolver funções usando Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 30f40db33b6aa8b40202c023f301265565257180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276680"
---
# <a name="azure-functions-python-developer-guide"></a>Guia do desenvolvedor de Python para o Azure Functions

Este artigo é uma introdução ao desenvolvimento do Azure Functions usando Python. O conteúdo abaixo pressupõe que você já tenha lido o [Guia de desenvolvedores do Azure Functions](functions-reference.md). 

Para projetos de amostra de função independentes em Python, consulte as [amostras de funções python](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Modelo de programação

O Azure Functions espera que uma função seja um método apátrida no seu script Python que processa a entrada e produz saída. Por padrão, o tempo de execução espera que `main()` o `__init__.py` método seja implementado como um método global chamado no arquivo. Você também pode [especificar um ponto de entrada alternativo](#alternate-entry-point).

Os dados de gatilhos e vinculações estão vinculados `name` à função através de atributos de método usando a propriedade definida no arquivo *function.json.* Por exemplo, a _função.json_ abaixo descreve uma função simples `req`desencadeada por uma solicitação HTTP chamada :

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Com base nessa `__init__.py` definição, o arquivo que contém o código de função pode parecer o seguinte exemplo:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Você também pode declarar explicitamente os tipos de atributos e o tipo de retorno na função usando anotações do tipo Python. Isso ajuda você a usar os recursos de intellisense e autocompletar fornecidos por muitos editores de código Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Use as anotações do Python incluídas no pacote [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) para associar a entrada e as saídas a seus métodos.

## <a name="alternate-entry-point"></a>Ponto de entrada alternativo

Você pode alterar o comportamento padrão de uma `scriptFile` `entryPoint` função especificando opcionalmente as propriedades no arquivo *function.json.* Por exemplo, a _função.json_ abaixo informa `customentry()` o tempo de execução para usar o método no arquivo _main.py,_ como o ponto de entrada para sua função Azure.

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

A estrutura de pasta recomendada para um projeto de Funções Python parece ser o seguinte exemplo:

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
 tests
```
A pasta principal\_\_\_\_do projeto (app) pode conter os seguintes arquivos:

* *local.settings.json*: Usado para armazenar configurações de aplicativos e strings de conexão ao ser executado localmente. Esse arquivo não é publicado no Azure. Para saber mais, consulte [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: Contém a lista de pacotes que o sistema instala ao publicar no Azure.
* *host.json*: Contém opções de configuração globais que afetam todas as funções em um aplicativo de função. Esse arquivo é publicado do Azure. Nem todas as opções são suportadas ao serem executados localmente. Para saber mais, consulte [host.json](functions-host-json.md).
* *.funcignore*: (Opcional) declara arquivos que não devem ser publicados no Azure.
* *.gitignore*: (Opcional) declara arquivos que são excluídos de um git repo, como local.settings.json.

Cada função possui seu próprio arquivo de código e arquivo de configuração de associação (function.json). 

Ao implantar seu projeto em um aplicativo de função no Azure, todo o conteúdo da pasta principal do projeto*\_\_(app)\_* deve ser incluído no pacote, mas não na pasta em si. Recomendamos que você mantenha seus testes em uma pasta `tests`separada da pasta do projeto, neste exemplo . Isso impede que você implante o código de teste com o seu aplicativo. Para obter mais informações, consulte [Teste de Unidade](#unit-testing).

## <a name="import-behavior"></a>Comportamento de importação

Você pode importar módulos em seu código de função usando referências relativas e absolutas explícitas. Com base na estrutura da pasta mostrada acima, as seguintes importações funcionam dentro do * \_ \_aplicativo\_\_\_\_\\\_\_\_* de arquivo de função \minha primeira função _ init .py :

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

As seguintes importações *não funcionam* dentro do mesmo arquivo:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

O código compartilhado deve ser mantido em uma pasta separada no * \_ \_aplicativo\_*. Para referenciar módulos na pasta *de código compartilhado,\_* você pode usar a seguinte sintaxe:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Gatilhos e Entradas

As entradas são divididas em duas categorias no Azure Functions: entrada do gatilho e entrada adicional. Embora sejam diferentes `function.json` no arquivo, o uso é idêntico no código Python.  As seqüências de conexão ou segredos `local.settings.json` para o mapa de fontes de gatilho e entrada mapeiam os valores no arquivo ao ser executado localmente e as configurações do aplicativo ao ser executado no Azure. 

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

Quando a função é invocada, a solicitação HTTP é transmitida para a função como `req`. Uma entrada será recuperada do Azure Blob Storage com base no _ID_ na URL da rota e disponibilizada como `obj` no corpo de função.  Aqui, a conta de armazenamento especificada é a seqüência de conexão encontrada na configuração do aplicativo AzureWebJobsStorage, que é a mesma conta de armazenamento usada pelo aplicativo de função.


## <a name="outputs"></a>outputs

A saída pode ser expressa em parâmetros de saída e em valores retornados. Se houver apenas uma saída, recomendamos usar o valor retornado. Para múltiplas saídas, você precisará usar parâmetros de saída.

Para usar o valor retornado de uma função como o valor de uma associação de saída, a propriedade `name` da associação deve ser definida como `$return` em `function.json`.

Para produzir várias saídas, use `set()` [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) o método fornecido pela interface para atribuir um valor à vinculação. Por exemplo, a função a seguir pode enviar uma mensagem para uma fila e também retornar uma resposta HTTP.

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

O acesso ao logger de tempo de execução [`logging`](https://docs.python.org/3/library/logging.html#module-logging) funções do Azure está disponível através de um manipulador raiz em seu aplicativo de função. Esse agente é vinculado ao Application Insights e permite sinalizar avisos e erros encontrados durante a execução da função.

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

Para saber mais sobre o registro, consulte [Funções do Monitor Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Gatilho HTTP e vinculações

O gatilho HTTP é definido no arquivo function.jon. A `name` ligação deve corresponder ao parâmetro nomeado na função. Nos exemplos anteriores, um `req` nome de vinculação é usado. Este parâmetro é um objeto [HttpRequest] e um objeto [HttpResponse] é devolvido.

A partir do objeto [HttpRequest,] você pode obter cabeçalhos de solicitação, parâmetros de consulta, parâmetros de rota e o corpo de mensagem. 

O exemplo a seguir é do modelo de [gatilho HTTP para Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

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

Nesta função, o valor `name` do parâmetro de consulta `params` é obtido a partir do parâmetro do objeto [HttpRequest.] O corpo de mensagem codificado pelo `get_json` JSON é lido usando o método. 

Da mesma forma, `status_code` você `headers` pode definir o e para a mensagem de resposta no objeto [HttpResponse] retornado.

## <a name="scaling-and-concurrency"></a>Dimensionamento e concorrência

Por padrão, o Azure Functions monitora automaticamente a carga em seu aplicativo e cria instâncias adicionais de host para Python conforme necessário. As funções usam limiares incorporados (não configuráveis pelo usuário) para diferentes tipos de gatilho para decidir quando adicionar instâncias, como a idade das mensagens e o tamanho da fila para O QueueTrigger. Para obter mais informações, consulte [como funcionam os planos de consumo e premium.](functions-scale.md#how-the-consumption-and-premium-plans-work)

Esse comportamento de dimensionamento é suficiente para muitas aplicações. Aplicações com qualquer uma das seguintes características, no entanto, podem não ser dimensionadas tão efetivamente:

- O aplicativo precisa lidar com muitas invocações simultâneas.
- O aplicativo processa um grande número de eventos de I/O.
- A aplicação está vinculada à I/O.

Nesses casos, você pode melhorar ainda mais o desempenho empregando padrões de sincronia e usando vários processos de trabalhadores de idiomas.

### <a name="async"></a>Assíncrono

Como o Python é um tempo de execução de um thread único, uma instância de host para Python pode processar apenas uma invocação de função por vez. Para aplicativos que processam um grande número de eventos de I/O e/ou está vinculado à I/O, você pode melhorar o desempenho executando funções de forma assíncrona.

Para executar uma função assíncronamente, use a `async def` declaração, que executa a função com [assíncronia](https://docs.python.org/3/library/asyncio.html) diretamente:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Uma função `async` sem a palavra-chave é executada automaticamente em um pool de threads asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Use vários processos de trabalhadores de idiomas

Por padrão, cada instância de host de Funções tem um único processo de trabalhador de idiomas. Você pode aumentar o número de processos de trabalhadorpor host (até 10) usando a configuração de aplicativo [FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) As funções do Azure, então, tentam distribuir uniformemente invocações de funções simultâneas entre esses trabalhadores. 

O FUNCTIONS_WORKER_PROCESS_COUNT se aplica a cada host que as Funções criam ao dimensionar seu aplicativo para atender à demanda. 

## <a name="context"></a>Contexto

Para obter o contexto de invocação de [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) uma função durante a execução, inclua o argumento em sua assinatura. 

Por exemplo: 

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

A classe [**Contexto**](/python/api/azure-functions/azure.functions.context?view=azure-python) tem os seguintes atributos de seqüência:

`function_directory`  
O diretório no qual a função está em execução.

`function_name`  
Nome da função.

`invocation_id`  
ID da invocação de função atual.

## <a name="global-variables"></a>Variáveis globais

Não é garantido que o estado do seu aplicativo será preservado para futuras execuções. No entanto, o tempo de execução do Azure Functions muitas vezes reutiliza o mesmo processo para múltiplas execuções do mesmo aplicativo. A fim de armazenar os resultados de um cálculo caro, declare-o como uma variável global. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as configurações do aplicativo,](functions-app-settings.md)como strings de conexão de serviço, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações `import os` declarando `setting = os.environ["setting-name"]`e, em seguida, usando, .

O exemplo a seguir obtém a `myAppSetting`configuração do [aplicativo,](functions-how-to-use-azure-function-app-settings.md#settings)com a chave nomeada :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Para o desenvolvimento local, as configurações do aplicativo são [mantidas no arquivo local.settings.json](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Versão do Python 

As funções do Azure suportam as seguintes versões python:

| Versão do Functions | Versões python<sup>*</sup> |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2. x | 3.7<br/>3.6 |

<sup>*</sup>Distribuições oficiais do CPython

Para solicitar uma versão específica do Python ao criar seu `--runtime-version` aplicativo [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) de função no Azure, use a opção do comando. A versão em tempo de `--functions-version` execução Functions é definida pela opção. A versão Python é definida quando o aplicativo de função é criado e não pode ser alterado.  

Ao ser executado localmente, o tempo de execução usa a versão python disponível. 

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

Quando estiver pronto para publicar, certifique-se de que todas as suas dependências disponíveis publicamente estejam listadas no arquivo requirements.txt, que está localizado na raiz do diretório do projeto. 

Os arquivos do projeto e as pastas excluídas da publicação, incluindo a pasta do ambiente virtual, estão listados no arquivo .funcignore.

Existem três ações de construção suportadas para publicar seu projeto Python no Azure:

+ Compilação remota: As dependências são obtidas remotamente com base no conteúdo do arquivo requirements.txt. [A compilação remota](functions-deployment-technologies.md#remote-build) é o método de compilação recomendado. Remoto também é a opção de compilação padrão da ferramenta Azure. 
+ Compilação local: As dependências são obtidas localmente com base no conteúdo do arquivo requirements.txt. 
+ Dependências personalizadas: Seu projeto usa pacotes não disponíveis publicamente para nossas ferramentas. (Requer Docker.)

Para construir suas dependências e publicar usando um sistema de entrega contínua (CD), [use a Azure Pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Construção remota

Por padrão, o Azure Functions Core Tools solicita uma compilação remota quando você usa o seguinte comando [func azure functionapp publica](functions-run-local.md#publish) para publicar seu projeto Python no Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

A [extensão de funções do Azure para o Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) também solicita uma compilação remota por padrão. 

### <a name="local-build"></a>Construção local

Você pode impedir a realização de uma compilação remota usando o seguinte comando [func azure functionapp publicar](functions-run-local.md#publish) para publicar com uma compilação local. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure. 

Usando `--build local` a opção, as dependências do projeto são lidas a partir do arquivo requirements.txt e esses pacotes dependentes são baixados e instalados localmente. Arquivos de projeto e dependências são implantados do seu computador local para o Azure. Isso resulta em um pacote de implantação maior sendo carregado no Azure. Se, por algum motivo, as dependências em seus requirements.txt file não puderem ser adquiridas pelo Core Tools, você deve usar a opção de dependências personalizadas para publicação. 

### <a name="custom-dependencies"></a>Dependências personalizadas

Se o seu projeto usa pacotes não disponíveis publicamente para nossas ferramentas, \_ \_\_\_você pode disponibilizá-los para o seu aplicativo colocando-os no diretório do aplicativo /.python_packages. Antes de publicar, execute o seguinte comando para instalar as dependências localmente:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Ao usar dependências personalizadas, `--no-build` você deve usar a opção de publicação, já que você já instalou as dependências.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

## <a name="unit-testing"></a>Teste de unidade

As funções escritas em Python podem ser testadas como outros códigos Python usando estruturas de teste padrão. Para a maioria das vinculações, é possível criar um objeto de entrada `azure.functions` simulado criando uma instância de uma classe apropriada a partir do pacote. Uma [`azure.functions`](https://pypi.org/project/azure-functions/) vez que o pacote não está disponível `requirements.txt` imediatamente, certifique-se de instalá-lo através do seu arquivo, conforme descrito na seção [de gerenciamento de pacotes](#package-management) acima. 

Por exemplo, a seguir está um teste simulado de uma função acionada HTTP:

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

Aqui está outro exemplo, com uma função acionada na fila:

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

O `tempfile.gettempdir()` método retorna uma pasta temporária, que no Linux é `/tmp`. Seu aplicativo pode usar este diretório para armazenar arquivos temporários gerados e usados por suas funções durante a execução. 

> [!IMPORTANT]
> Os arquivos escritos no diretório temporário não são garantidos para persistir em invocações. Durante a escala, os arquivos temporários não são compartilhados entre instâncias. 

O exemplo a seguir cria um arquivo`/tmp`temporário nomeado no diretório temporário ( ):

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

Recomendamos que você mantenha seus testes em uma pasta separada da pasta do projeto. Isso impede que você implante o código de teste com o seu aplicativo. 

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

Todos os problemas conhecidos e solicitações de recursos são controlados usando a lista [Problemas do GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se você tiver um problema e não for possível localizá-lo no GitHub, abra um novo problema e inclua uma descrição detalhada dele.

### <a name="cross-origin-resource-sharing"></a>Compartilhamento de recursos entre origens

O Azure Functions suporta compartilhamento de recursos de origem cruzada (CORS). O CORS é configurado [no portal](functions-how-to-use-azure-function-app-settings.md#cors) e através da CLI [Do Azure.](/cli/azure/functionapp/cors) A lista de origens permitidas pelo CORS se aplica ao nível do aplicativo de função. Com o CORS ativado, `Access-Control-Allow-Origin` as respostas incluem o cabeçalho. Para obter mais informações, consulte [Compartilhamento de recursos entre origens](functions-how-to-use-azure-function-app-settings.md#cors).

A lista de origens permitidas [não é suportada atualmente](https://github.com/Azure/azure-functions-python-worker/issues/444) para aplicativos de função Python. Devido a essa limitação, você `Access-Control-Allow-Origin` deve definir expressamente o cabeçalho em suas funções HTTP, como mostrado no exemplo a seguir:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Certifique-se de que você também atualize sua função.json para suportar o método OPTIONS HTTP:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Este método HTTP é usado por navegadores da Web para negociar a lista de origens permitidas. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Documentação da API do pacote de funções do Azure](/python/api/azure-functions/azure.functions?view=azure-python)
* [Práticas recomendadas do Azure Functions](functions-best-practices.md)
* [Azure Funções gatilhos e vinculações](functions-triggers-bindings.md)
* [Associações do armazenamento de blobs](functions-bindings-storage-blob.md)
* [Associações HTTP e webhook](functions-bindings-http-webhook.md)
* [Associações de Armazenamento de Filas](functions-bindings-storage-queue.md)
* [Gatilho de temporizador](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
