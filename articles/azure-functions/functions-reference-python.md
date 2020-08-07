---
title: Referência de desenvolvedor do Python para o Azure Functions
description: Saiba como desenvolver funções usando Python
ms.topic: article
ms.date: 12/13/2019
ms.custom: devx-track-python
ms.openlocfilehash: 776355ce981ba5cc2a24bfe473da2f55427eadf6
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850739"
---
# <a name="azure-functions-python-developer-guide"></a>Guia do desenvolvedor de Python para o Azure Functions

Este artigo é uma introdução ao desenvolvimento do Azure Functions usando Python. O conteúdo abaixo pressupõe que você já tenha lido o [Guia de desenvolvedores do Azure Functions](functions-reference.md).

Para projetos de amostra de função autônoma no Python, confira as [amostras de Functions para Python](/samples/browse/?products=azure-functions&languages=python).

## <a name="programming-model"></a>Modelo de programação

O Azure Functions espera que a função seja um método sem estado no script de Python que processa a entrada e produz a saída. Por padrão, o runtime espera que ela seja implementada como um método global chamado `main()` no arquivo `__init__.py`. Você também pode [especificar um ponto de entrada alternativo](#alternate-entry-point).

Os dados de gatilhos e associações estão vinculados à função por meio de atributos de método que usam a propriedade `name` definida no arquivo *function.json*. Por exemplo, o _function.json_ abaixo descreve uma função simples disparada por uma solicitação HTTP denominada `req`:

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Com base nessa definição, o arquivo `__init__.py` que contém o código de função pode ser semelhante a este exemplo:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Você também pode declarar os tipos de atributo e de retorno na função por meio de anotações de tipo do Python. Isso ajuda você a usar o IntelliSense e os recursos de preenchimento automático fornecidos por vários editores de código Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Use as anotações do Python incluídas no pacote [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) para associar a entrada e as saídas a seus métodos.

## <a name="alternate-entry-point"></a>Ponto de entrada alternativo

Você poderá alterar o comportamento padrão de uma função se especificar opcionalmente as propriedades `scriptFile` e `entryPoint` no arquivo *function.json*. Por exemplo, o _function.json_ a seguir diz ao runtime para usar o método `customentry()` no arquivo _main.py_ como o ponto de entrada da sua função do Azure.

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

A estrutura de pastas recomendada a um projeto Python do Functions é semelhante a este exemplo:

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
A pasta principal do projeto (\_aplicativo \_\_\_) pode conter os seguintes arquivos:

* *local.settings.json*: usado para armazenar as configurações do aplicativo e as cadeias de conexão durante a execução local. Esse arquivo não é publicado no Azure. Para saber mais, confira [local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: contém a lista de pacotes que o sistema instala quando publica no Azure.
* *host.json*: contém opções de configuração global que afetam todas as funções em um aplicativo de funções. Esse arquivo é publicado do Azure. Nem todas as opções têm suporte quando executadas localmente. Para saber mais, confira [host.json](functions-host-json.md).
* *.funcignore*: (opcional) declara os arquivos que não devem ser publicados no Azure.
* *.gitignore*: (opcional) declara os arquivos que são excluídos de um repositório Git, como local.settings.json.
* *Dockerfile*: (opcional) usado quando seu projeto é publicado em um [contêiner personalizado](functions-create-function-linux-custom-image.md).

Cada função possui seu próprio arquivo de código e arquivo de configuração de associação (function.json).

Ao implantar seu projeto em seu aplicativo de funções no Azure, todo o conteúdo do projeto principal ( *\_\_app\_\_* ) deverá ser incluído no pacote, mas não a pasta em si. Recomendamos que você mantenha os testes em uma pasta separada da pasta do projeto, que, neste exemplo, é `tests`. Isso impede que você implante o código de teste com seu aplicativo. Para obter mais informações, confira [Teste de Unidade](#unit-testing).

## <a name="import-behavior"></a>Comportamento de importação

Você pode importar módulos para seu código de função usando tanto referências absolutas quanto relativas explícitas. Com base na estrutura de pastas mostrada acima, as importações abaixo funcionam de dentro do arquivo de função *\_\_app\_\_\my\_first\_function\\_\_init\_\_.py*:

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

As importações abaixo *não funcionam* de dentro do mesmo arquivo:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

O código compartilhado deve ser mantido em uma pasta separada em *\_\_app\_\_* . Para fazer referência a módulos na pasta *shared\_code*, você pode usar a seguinte sintaxe:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Gatilhos e entradas

As entradas são divididas em duas categorias no Azure Functions: entrada do gatilho e entrada adicional. Embora elas sejam diferentes no arquivo `function.json`, seu uso é idêntico no código Python.  As cadeias de conexão ou os segredos para fontes de entrada e gatilho são mapeados para valores no arquivo `local.settings.json` quando são executados localmente, e as configurações do aplicativo quando são executados no Azure.

Por exemplo, o código abaixo demonstra a diferença entre os dois:

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

Quando a função é invocada, a solicitação HTTP é transmitida para a função como `req`. Uma entrada será recuperada do Armazenamento de Blobs do Azure com base na _ID_ na URL da rota e disponibilizada como `obj` no corpo da função.  Aqui, a conta de armazenamento especificada é a cadeia de conexão encontrada na configuração do aplicativo AzureWebJobsStorage, que é a mesma conta de armazenamento usada pelo aplicativo de funções.


## <a name="outputs"></a>outputs

A saída pode ser expressa em parâmetros de saída e em valores retornados. Se houver apenas uma saída, recomendamos usar o valor retornado. Para múltiplas saídas, você precisará usar parâmetros de saída.

Para usar o valor retornado de uma função como o valor de uma associação de saída, a propriedade `name` da associação deve ser definida como `$return` em `function.json`.

Para produzir várias saídas, use o método `set()` fornecido pela interface [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) a fim de atribuir um valor à associação. Por exemplo, a função a seguir pode enviar uma mensagem para uma fila e também retornar uma resposta HTTP.

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

## <a name="logging"></a>Registro em log

O acesso ao agente do Azure Functions runtime está disponível por meio de um manipulador [`logging`](https://docs.python.org/3/library/logging.html#module-logging) raiz no seu aplicativo de funções. Esse agente é vinculado ao Application Insights e permite sinalizar avisos e erros encontrados durante a execução da função.

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

Para saber mais sobre como registrar em log, confira [Monitorar o Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Gatilhos e associações HTTP

O gatilho HTTP é definido no function.jsno arquivo. O `name` da associação deve corresponder ao parâmetro nomeado na função.
Nos exemplos anteriores, um nome de associação `req` é usado. Esse parâmetro é um objeto [HttpRequest], e um objeto [HttpResponse] é retornado.

No objeto [HttpRequest], você pode obter cabeçalhos de solicitação, parâmetros de consulta, parâmetros de rota e o corpo da mensagem.

O exemplo a seguir é do [modelo de gatilho HTTP para Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

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

Nessa função, o valor do parâmetro de consulta `name` é obtido do parâmetro `params` do objeto [HttpRequest]. O corpo da mensagem codificada em JSON é lido pelo método `get_json`.

Da mesma forma, você pode definir `status_code` e `headers` para a mensagem de resposta no objeto retornado [HttpResponse].

## <a name="scaling-and-concurrency"></a>Dimensionamento e simultaneidade

Por padrão, o Azure Functions monitora automaticamente a carga em seu aplicativo e cria instâncias de host adicionais para Python conforme a necessidade. O Functions usa limites internos (não configuráveis pelo usuário) em tipos de gatilhos diferentes para decidir quando adicionar instâncias, por exemplo, a idade das mensagens e o tamanho da fila para QueueTrigger. Para obter mais informações, confira [Como funcionam os planos de consumo e Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Esse comportamento de dimensionamento é suficiente para vários aplicativos. No entanto, os aplicativos com alguma das seguintes características podem não ser dimensionados com eficiência:

- O aplicativo precisa lidar com muitas invocações simultâneas.
- O aplicativo processa um grande número de eventos de E/S.
- O aplicativo está associado a E/S.

Nesses casos, você pode melhorar ainda mais o desempenho com o emprego de padrões assíncronos e o uso de vários processos de trabalho de linguagem.

### <a name="async"></a>Assíncrono

Como o Python é um runtime de thread único, uma instância de host para Python pode processar apenas uma invocação de função por vez. Para aplicativos que processam um grande número de eventos de E/S e/ou são associados a E/S, você pode melhorar o desempenho executando as funções de forma assíncrona.

Para executar uma função de forma assíncrona, use a instrução `async def`, que executa a função com [asyncio](https://docs.python.org/3/library/asyncio.html) diretamente:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Uma função sem a palavra-chave `async` é executada automaticamente em um pool de threads asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Usar vários processos de trabalho de linguagem

Por padrão, cada instância de host do Functions tem um processo de trabalho de linguagem único. Você pode aumentar o número de processos de trabalho por host (até 10) com a configuração de aplicativo [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count). O Azure Functions tenta distribuir uniformemente invocações de função simultâneas entre esses trabalhos.

O FUNCTIONS_WORKER_PROCESS_COUNT se aplica a cada host que o Functions cria quando escala horizontalmente seu aplicativo para atender à demanda.

## <a name="context"></a>Contexto

Para obter o contexto de invocação de uma função durante a execução, inclua o argumento [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) em sua assinatura.

Por exemplo:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

a classe[**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) tem os seguintes atributos de cadeia de caracteres:

`function_directory` O diretório no qual a função está em execução.

`function_name` O nome da função.

`invocation_id` A ID da invocação de função atual.

## <a name="global-variables"></a>Variáveis globais

Não há garantia de que o estado do seu aplicativo será preservado para execuções futuras. No entanto, o runtime do Azure Functions geralmente reutiliza o mesmo processo para várias execuções do mesmo aplicativo. Para armazenar em cache os resultados de uma computação cara, declare-os como uma variável global.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Variáveis de ambiente

No Functions, as [configurações de aplicativo](functions-app-settings.md), como cadeias de conexão de serviço, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações com a declaração de `import os` e o uso de `setting = os.environ["setting-name"]`.

O exemplo abaixo obtém a [configuração de aplicativo](functions-how-to-use-azure-function-app-settings.md#settings), com a chave denominada `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Para o desenvolvimento local, as configurações de aplicativo são [mantidas no arquivo local.settings.json](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Versão do Python

O Azure Functions dá suporte às seguintes versões do Python:

| Versão do Functions | Versões do<sup>*</sup> Python |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2. x | 3.7<br/>3.6 |

<sup>*</sup>Distribuições oficiais do CPython

Para solicitar uma versão específica do Python quando você cria seu aplicativo de funções no Azure, use a opção `--runtime-version` do comando [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create). A versão de runtime do Functions é definida pela opção `--functions-version`. A versão do Python é definida quando o aplicativo de funções é criado e não pode ser alterada.

Durante a execução local, o runtime usa a versão disponível do Python.

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

Quando você estiver pronto para publicar, verifique se todas as dependências disponíveis publicamente estão listadas no arquivo Requirements.txt, localizado na raiz do diretório do projeto.

Os arquivos e as pastas de projeto que são excluídos da publicação, incluindo a pasta de ambiente virtual, são listados no arquivo .funcignore.

Há três ações de compilação com suporte para publicar seu projeto Python no Azure: compilação remota, compilação local e compilações usando dependências personalizadas.

Você também pode usar Azure Pipelines para criar suas dependências e publicar usando a entrega contínua (CD). Para saber mais, consulte [entrega contínua usando o Azure DevOps](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Build remoto

Ao usar a compilação remota, as dependências restauradas no servidor e as dependências nativas correspondem ao ambiente de produção. Isso resulta em um pacote de implantação menor a ser carregado. Use a compilação remota ao desenvolver aplicativos Python no Windows. Se o seu projeto tiver dependências personalizadas, você poderá [usar a compilação remota com a URL de índice extra](#remote-build-with-extra-index-url).

As dependências são obtidas remotamente com base no conteúdo do arquivo de requirements.txt. O [Build remoto](functions-deployment-technologies.md#remote-build) é o método de build recomendado. Por padrão, o Azure Functions Core Tools solicita um build remoto quando você usa o comando [func azure functionapp publish](functions-run-local.md#publish) a seguir para publicar seu projeto Python no Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

A [extensão do Azure Functions para Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) também solicita um build remoto por padrão.

### <a name="local-build"></a>Build local

As dependências são obtidas localmente com base no conteúdo do arquivo de requirements.txt. Você pode evitar fazer um build remoto com o uso do comando [func azure functionapp publish](functions-run-local.md#publish) a seguir para publicar com um build local.

```command
func azure functionapp publish <APP_NAME> --build local
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

Com a opção `--build local`, as dependências do projeto são lidas no arquivo requirements.txt e esses pacotes dependentes são baixados e instalados localmente. Os arquivos e as dependências do projeto são implantados de seu computador local no Azure. Isso faz com que um pacote de implantação maior seja carregado no Azure. Se, por algum motivo, as dependências no arquivo requirements.txt não puderem ser adquiridas pelo Core Tools, você precisará usar a opção de dependências personalizadas para publicar.

Não recomendamos o uso de compilações locais ao desenvolver localmente no Windows.

### <a name="custom-dependencies"></a>Dependências personalizadas

Quando o projeto tem dependências não encontradas no [índice do pacote do Python](https://pypi.org/), há duas maneiras de compilar o projeto. O método de compilação depende de como você cria o projeto.

#### <a name="remote-build-with-extra-index-url"></a>Build remoto com URL de índice extra

Quando os pacotes estiverem disponíveis em um índice de pacote personalizado acessível, use uma compilação remota. Antes de publicar, certifique-se de [criar uma configuração de aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) chamada `PIP_EXTRA_INDEX_URL` . O valor dessa configuração é a URL do seu índice de pacote personalizado. Usar essa configuração instrui a compilação remota a ser executada `pip install` usando a `--extra-index-url` opção. Para saber mais, confira a [documentação de instalação do Python Pip](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format).

Você também pode usar credenciais básicas de autenticação com suas URLs de índice de pacote extra. Para saber mais, consulte [credenciais de autenticação básica](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) na documentação do Python.

#### <a name="install-local-packages"></a>Instalar pacotes locais

Se seu projeto usa pacotes que não estão disponíveis publicamente para nossas ferramentas, você poderá disponibilizá-los para seu aplicativo se os colocar no diretório \_app \_\_\_/.python_packages. Antes de publicar, execute o seguinte comando para instalar as dependências localmente:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Ao usar dependências personalizadas, você deve usar a `--no-build` opção de publicação, já que já instalou as dependências na pasta do projeto.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Lembre-se de substituir `<APP_NAME>` pelo nome do aplicativo de funções no Azure.

## <a name="unit-testing"></a>Teste de Unidade

As funções escritas em Python podem ser testadas como qualquer outro código Python por meio de estruturas de teste padrão. Para a maioria das associações, é possível criar um objeto de entrada fictício com a criação de uma instância de uma classe apropriada do pacote `azure.functions`. Já que o pacote [`azure.functions`](https://pypi.org/project/azure-functions/) não fica disponível imediatamente, instale-o por meio do arquivo `requirements.txt` conforme descrito na seção [Gerenciamento de pacotes](#package-management) acima.

Por exemplo, abaixo temos um teste fictício de uma função disparada por HTTP:

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

O método `tempfile.gettempdir()` retorna uma pasta temporária, que no Linux é a `/tmp`. Seu aplicativo pode usar esse diretório para armazenar arquivos temporários gerados e usados por suas funções durante a execução.

> [!IMPORTANT]
> Os arquivos gravados no diretório temporário podem não persistir entre invocações. Durante o processo de escalar horizontalmente, os arquivos temporários não são compartilhados entre instâncias.

O exemplo abaixo cria um arquivo temporário nomeado no diretório temporário (`/tmp`):

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

## <a name="preinstalled-libraries"></a>Bibliotecas pré-instaladas

Há algumas bibliotecas fornecidas com o tempo de execução de funções do Python.

### <a name="python-standard-library"></a>Biblioteca padrão do Python

A biblioteca padrão do Python contém uma lista de módulos do Python internos que são fornecidos com cada distribuição do Python. A maioria dessas bibliotecas ajuda você a acessar a funcionalidade do sistema, como e/s de arquivo. Em sistemas Windows, essas bibliotecas são instaladas com o Python. Nos sistemas baseados em UNIX, eles são fornecidos por coleções de pacotes.

Para exibir os detalhes completos da lista dessas bibliotecas, visite os links abaixo:

* [Biblioteca padrão do Python 3,6](https://docs.python.org/3.6/library/)
* [Biblioteca padrão do Python 3,7](https://docs.python.org/3.7/library/)
* [Biblioteca padrão do Python 3,8](https://docs.python.org/3.8/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions dependências de trabalho do Python

O trabalho do Python do Functions requer um conjunto específico de bibliotecas. Você também pode usar essas bibliotecas em suas funções, mas elas não fazem parte do padrão Python. Se suas funções dependem de qualquer uma dessas bibliotecas, elas podem não estar disponíveis para seu código durante a execução fora do Azure Functions. Você pode encontrar uma lista detalhada de dependências na seção **instalar \_ requer** no arquivo [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) .

> [!NOTE]
> Se o requirements.txt do seu aplicativo de funções contiver uma `azure-functions-worker` entrada, remova-a. O operador do Functions é gerenciado automaticamente pela plataforma Azure Functions e nós os atualizamos regularmente com novos recursos e correções de bugs. A instalação manual de uma versão antiga do Worker no requirements.txt pode causar problemas inesperados.

### <a name="azure-functions-python-library"></a>Azure Functions biblioteca do Python

Cada atualização de trabalho do Python inclui uma nova versão da [biblioteca Azure Functions Python (Azure. Functions)](https://github.com/Azure/azure-functions-python-library). Essa abordagem torna mais fácil atualizar continuamente seus aplicativos de funções do Python, pois cada atualização é compatível com versões anteriores. Uma lista de versões dessa biblioteca pode ser encontrada no [Azure-Functions PyPi](https://pypi.org/project/azure-functions/#history).

A versão da biblioteca de tempo de execução é corrigida pelo Azure e não pode ser substituída por requirements.txt. A `azure-functions` entrada no requirements.txt é apenas para refiapor e conscientização do cliente.

Use o código a seguir para acompanhar a versão real da biblioteca de funções do Python em seu tempo de execução:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Bibliotecas do sistema de tempo de execução

Para obter uma lista de bibliotecas do sistema pré-instalado em imagens do Docker de trabalho do Python, siga os links abaixo:

|  runtime do Functions  | Versão do Debian | Versões do Python |
|------------|------------|------------|
| Versão 2.x | Stretch  | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Versão 3.x | Buster | [Python 3.6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3.8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile) |

## <a name="cross-origin-resource-sharing"></a>Compartilhamento de recursos entre origens

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

O CORS tem suporte total a aplicativos de funções do Python.

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

Veja a seguir uma lista de guias de solução de problemas comuns:

* [ModuleNotFoundError e ImportError](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Não é possível importar ' cygrpc '](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Todos os problemas conhecidos e solicitações de recursos são controlados usando a lista [Problemas do GitHub](https://github.com/Azure/azure-functions-python-worker/issues). Se você tiver um problema e não for possível localizá-lo no GitHub, abra um novo problema e inclua uma descrição detalhada dele.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Documentação da API do pacote do Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
* [Associações do armazenamento de blobs](functions-bindings-storage-blob.md)
* [Associações HTTP e webhook](functions-bindings-http-webhook.md)
* [Associações de Armazenamento de Filas](functions-bindings-storage-queue.md)
* [Gatilho de temporizador](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
