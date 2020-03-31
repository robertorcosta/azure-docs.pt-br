---
title: Depurar a linguagem de consulta kusto inline Python usando VS Code - Azure Data Explorer
description: Aprenda a depurar o Kusto query language (KQL) inline Python usando o VS Code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444462"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Depurar a linguagem de consulta kusto inline Python usando o Código VS

O Azure Data Explorer suporta a execução do código Python incorporado na linguagem de consulta Kusto usando o [plugin python().](/azure/kusto/query/pythonplugin) O tempo de execução do plugin está hospedado em uma caixa de areia, um ambiente Python isolado e seguro. O recurso de plugin python() estende as funcionalidades nativas da linguagem de consulta kusto com o enorme arquivamento de pacotes OSS Python. Essa extensão permite executar algoritmos avançados, como aprendizado de máquina, inteligência artificial, séries estatísticas e tempo como parte da consulta.

As ferramentas de linguagem de consulta kusto não são convenientes para desenvolver e depurar algoritmos Python. Portanto, desenvolva o algoritmo em seu ambiente de desenvolvimento integrado ao Python favorito, como Jupyter, PyCharm, VS ou VS Code. Quando o algoritmo estiver completo, copie e cole em KQL. Para melhorar e agilizar esse fluxo de trabalho, o Azure Data Explorer suporta a integração entre clientes Kusto Explorer ou Web UI e o VS Code para autoria e depuração do código Python inline KQL. 

> [!NOTE]
> Este fluxo de trabalho só pode ser usado para depurar tabelas de entrada relativamente pequenas (até poucos MB). Portanto, você pode precisar limitar a entrada para depuração.  Se você precisar processar uma tabela grande, limite-a para depuração usando `| take`, `| sample`ou `where rand() < 0.x`.

## <a name="prerequisites"></a>Pré-requisitos

1. Instale [a distribuição Python Anaconda](https://www.anaconda.com/distribution/#download-section). Em **Opções Avançadas,** **selecione Adicionar Anaconda à variável de ambiente PATH**.
2. Instale [o Visual Studio Code](https://code.visualstudio.com/Download)
3. Instale [a extensão Python para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Execute sua consulta no aplicativo do cliente

1. No aplicativo do cliente, prefixe uma consulta contendo Python inline com`set query_python_debug;`
1. Executa a consulta.
    * Kusto Explorer: VS Code é automaticamente lançado com o script *debug_python.py.*
    * Kusto Web UI: 
        1. Baixe e salve *debug_python.py*, *df.txt*e *kargs.txt*. Na janela, **selecione Permitir**. **Salvar** arquivos no diretório selecionado. 

            ![Web UI baixa os arquivos python inline](media/debug-inline-python/webui-inline-python.png)

        1. Clique com o botão *direito debug_python.py* e abra com código VS. 
        O script *debug_python.py* contém o código Python inline, da consulta KQL, prefixado pelo código de modelo para inicializar o dataframe de entrada de *df.txt* e o dicionário de parâmetros de *kargs.txt*.    
            
1. No código VS, inicie o depurador de código VS: >  **Depuração****de início de depuração (F5)**, selecione a configuração **Python.** O depurador será iniciado e automaticamente ponto de ruptura para depurar o código inline.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Como funciona a depuração inline python no CÓDIGO VS?

1. A consulta é analisado e executado no servidor `| evaluate python()` até que a cláusula requerida seja atingida.
1. A caixa de areia Python é invocada, mas em vez de executar o código, ele serializa a tabela de entrada, o dicionário de parâmetros e o código, e os envia de volta para o cliente.
1. Esses três objetos são salvos em três arquivos: *df.txt*, *kargs.txt*e *debug_python.py* no diretório selecionado (UI web) ou no diretório %TEMP% do cliente (Kusto Explorer).
1. O código VS é lançado, pré-carregado com o arquivo *debug_python.py* que contém um código prefixo para inicializar df e kargs de seus respectivos arquivos, seguido pelo script Python incorporado na consulta KQL.

## <a name="query-example"></a>Exemplo de consulta

1. Execute a seguinte consulta KQL no aplicativo do cliente:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Veja a tabela resultante:

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Execute a mesma consulta KQL no `set query_python_debug;`aplicativo do cliente usando:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. O CÓDIGO VS é lançado:

    ![lançar código VS](media/debug-inline-python/launch-vs-code.png)

1. VS Código desincorre e imprime dataframe 'result' no console de depuração:

    ![Depuração de código VS](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Pode haver diferenças entre a imagem da caixa de areia Python e sua instalação local. [Verifique a imagem da caixa de areia para pacotes específicos consultando o plugin](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
