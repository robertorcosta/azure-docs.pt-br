---
title: Usar o Python e o TensorFlow no Azure Functions para fazer inferências de aprendizado de máquina
description: Esse tutorial demonstra como aplicar modelos de machine learning do TensorFlow no Azure Functions
author: anthonychu
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: e7c4e1bbd23d43d5e11ab8b26c3d4e1215b4946b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230498"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Tutorial: Aplicar modelos de machine learning no Azure Functions com o Python e o TensorFlow

Este artigo demonstra como o Azure Functions permite que você use o Python e o TensorFlow com um modelo de machine learning para classificar uma imagem com base no respectivo conteúdo.

Neste tutorial, você aprenderá a: 

> [!div class="checklist"]
> * Inicializar um ambiente local para o desenvolvimento de Azure Functions no Python
> * Importar um modelo de machine learning do TensorFlow personalizado para um aplicativo de funções
> * Criar uma API HTTP sem servidor para prever se uma foto contém um cão ou um gato
> * Consumir a API de um aplicativo Web

![Captura de tela do projeto concluído](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos 

Para criar Azure Functions em Python, você precisa instalar algumas ferramentas.

- [Python 3.6](https://www.python.org/downloads/release/python-360/)
- [Ferramentas básicas do Azure Functions](functions-run-local.md#install-the-azure-functions-core-tools)
- Um editor de códigos como o [Visual Studio Code](https://code.visualstudio.com/)

## <a name="clone-the-tutorial-repository"></a>Clonar o repositório do tutorial

Para começar, abra um terminal e clone o repositório a seguir usando o Git:

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

O repositório contém algumas pastas.

- *start*:  Essa é sua pasta de trabalho para o tutorial
- *end*: Esse é o resultado final e a implementação completa para sua referência
- *resources*: Contém o modelo de machine learning e as bibliotecas auxiliares
- *frontend*: Um site que chama o aplicativo de funções

## <a name="create-and-activate-a-python-virtual-environment"></a>Criar e ativar um ambiente virtual do Python

O Azure Functions requer o Python 3.6.x. Você criará um ambiente virtual para garantir que está usando a versão necessária do Python.

Altere o diretório de trabalho atual para a pasta *start*. Em seguida, crie e ative um ambiente virtual chamado *.venv*. Dependendo da instalação do Python, os comandos para criar um ambiente virtual do Python 3.6 podem ser diferentes das instruções a seguir.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

o prompt de terminal agora é prefixado com `(.venv)`, o que indica que você ativou com êxito o ambiente virtual. Verifique se o `python`, no ambiente virtual, é de fato o Python 3.6.x.

```console
python --version
```

> [!NOTE]
> Para o restante do tutorial, você executa comandos no ambiente virtual. Se você precisar reativar o ambiente virtual em um terminal, execute o comando de ativação apropriado para o sistema operacional.

## <a name="create-an-azure-functions-project"></a>Criar um projeto do Azure Functions

Na pasta *start*, use o Azure Functions Core Tools para inicializar um aplicativo de funções do Python.

```console
func init --worker-runtime python
```

Um aplicativo de funções pode conter uma ou mais Azure Functions. Abra a pasta *start* em um editor e examine o conteúdo.

- [*local.settings.json*](functions-run-local.md#local-settings-file): contém as configurações de aplicativo usadas para desenvolvimento local
- [*host.json*](functions-host-json.md): contém configurações para o host e as extensões do Azure Functions
- [*requirements.txt*](functions-reference-python.md#package-management): contém os pacotes do Python exigidos por este aplicativo

## <a name="create-an-http-function"></a>Criar uma função HTTP

O aplicativo requer um único ponto de extremidade de API HTTP que usa uma URL de imagem como entrada e retorna uma previsão quanto à imagem conter um cachorro ou um gato.

No terminal, use o Azure Functions Core Tools para fazer scaffold de uma nova função HTTP denominada *classify*.

```console
func new --language python --template HttpTrigger --name classify
```

Uma nova pasta chamada *classify* é criada, contendo dois arquivos.

- *\_\_init\_\_.py*: um arquivo para a função principal
- *function.json*:  um arquivo que descreve o gatilho da função e as respectivas associações de entrada e saída

### <a name="run-the-function"></a>Executar a função

No terminal, com o ambiente virtual Python ativado, inicie o aplicativo de funções.

```console
func start
```

Abra uma janela de navegador e navegue até a URL a seguir. A função deve ser executada e retornar *Olá Azure!*

```
http://localhost:7071/api/classify?name=Azure
```

Use `Ctrl-C` para interromper o aplicativo de funções.

## <a name="import-the-tensorflow-model"></a>Importar o modelo TensorFlow

Você usará um modelo TensorFlow predefinido que foi treinado e exportado do Serviço de Visão Personalizada do Azure.

> [!NOTE]
> Se quiser criar seu próprio modelo usando a camada gratuita do Serviço de Visão Personalizada, você poderá seguir as [instruções no repositório do projeto de exemplo](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

O modelo consiste em dois arquivos na pasta *<RAIZ_DO_REPOSITÓRIO>/resources/model*: *model.pb* e *labels.txt*. Copie-os para a pasta da função *classify*.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

verifique se você incluiu o \* no comando acima. Verifique se *classify* agora contém arquivos denominados *model.pb* e *labels.txt*.

## <a name="add-the-helper-functions-and-dependencies"></a>Adicionar as funções auxiliares e dependências

Algumas funções auxiliares para preparar a imagem de entrada e fazer uma previsão usando o TensorFlow estão em um arquivo chamado *predict.py*, na pasta *resources*. Copie esse arquivo para a pasta da função *classify*.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

verifique se *classify* agora contém um arquivo denominado *predict.py*.

### <a name="install-dependencies"></a>Instalar dependências

A biblioteca auxiliar tem algumas dependências que precisam ser instaladas. Abra *start/requirements.txt* no editor e adicione as dependências a seguir ao arquivo.

```txt
tensorflow==1.14
Pillow
requests
```

Salve o arquivo.

No terminal com o ambiente virtual ativado, execute o comando a seguir na pasta *start* para instalar as dependências. Algumas etapas de instalação podem levar alguns minutos para serem concluídas.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>Armazenando em cache o modelo em variáveis globais

No editor, abra *predict.py* e examine a função `_initialize` próxima à parte superior do arquivo. Observe que o modelo TensorFlow é carregado do disco na primeira vez em que a função é executada e salvo em variáveis globais. O carregamento do disco é ignorado nas execuções subsequentes da função `_initialize`. Armazenar em cache o modelo na memória com essa técnica acelera as previsões posteriores.

Para obter mais informações sobre variáveis globais, confira o [Guia do desenvolvedor do Python para o Azure Functions](functions-reference-python.md#global-variables).

## <a name="update-function-to-run-predictions"></a>Atualizar função para executar previsões

Abra *classify/\_\_init\_\_.py* no editor. Importe a biblioteca *predict* que você adicionou à mesma pasta anteriormente. Adicione as instruções `import` a seguir abaixo das outras importações que já estão no arquivo.

```python
import json
from .predict import predict_image_from_url
```

Substitua o código do modelo de função por este a seguir.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

Certifique-se de salvar as alterações.

Essa função recebe uma URL de imagem em um parâmetro de cadeia de caracteres de consulta chamado `img`. Ele chama `predict_image_from_url` da biblioteca auxiliar que baixa a imagem e retorna uma previsão usando o modelo do TensorFlow. Em seguida, a função retorna uma resposta HTTP com os resultados.

Já que o ponto de extremidade HTTP é chamado por uma página da Web hospedada em outro domínio, a resposta HTTP inclui um cabeçalho `Access-Control-Allow-Origin` para atender aos requisitos de CORS (compartilhamento de recursos entre origens) do navegador.

> [!NOTE]
> Em um aplicativo de produção, altere `*` para a origem específica da página da Web para obter segurança adicional.

### <a name="run-the-function-app"></a>Executar o aplicativo de funções

Verifique se o ambiente virtual do Python ainda está ativado e inicie o aplicativo de funções usando o comando a seguir.

```console
func start
```

Em um navegador, abra esta URL que chama sua função com a URL de uma foto de gato. Verifique se um resultado de previsão válido é retornado.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

Mantenha o aplicativo de funções em execução.

### <a name="run-the-web-app"></a>Executar o aplicativo Web

Há um aplicativo Web simples na pasta *frontend* que consome a API HTTP no aplicativo de funções.

Abra um terminal *separado* e altere para a pasta *frontend*. Inicie um servidor HTTP com o Python 3.6.

#### <a name="linux-and-macos"></a>Linux e macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

Em um navegador, navegue até a URL do servidor HTTP que é exibida no terminal. Um aplicativo Web deve aparecer. Insira uma das URLs de foto a seguir na caixa de texto. Você também pode usar uma URL de uma foto de gato ou de cachorro publicamente acessível.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

Quando você clica em enviar, o aplicativo de funções é chamado e um resultado é exibido na página.

## <a name="clean-up-resources"></a>Limpar recursos
Todo este tutorial é executado localmente em seu computador, portanto, não há recursos ou serviços do Azure a serem limpos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar e personalizar uma API HTTP com o Azure Functions para fazer previsões usando um modelo do TensorFlow. Você também aprendeu como chamar a API de um aplicativo Web.

É possível usar as técnicas deste tutorial para criar APIs de qualquer complexidade, fazendo-o simultaneamente à execução no modelo de computação sem servidor fornecido pelo Azure Functions.

Para implantar o aplicativo de funções no Azure, use o [Azure Functions Core Tools](./functions-run-local.md#publish) ou o [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).

> [!div class="nextstepaction"]
> [Guia do desenvolvedor do Python para o Azure Functions](./functions-reference-python.md)
