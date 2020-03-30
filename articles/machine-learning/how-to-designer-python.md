---
title: Python
titleSuffix: Azure Machine Learning
description: Aprenda a usar o Python no designer de Machine Learning do Azure para transformar dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455784"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Execute o código Python no designer de Machine Learning do Azure

Neste artigo, você aprende a usar o módulo [Execute Python Script](algorithm-module-reference/execute-python-script.md) para adicionar lógica personalizada ao designer de Machine Learning do Azure. No how-to a seguir, você usa a biblioteca Pandas para fazer engenharia de recursos simples.

Você pode usar o editor de código embutido para adicionar rapidamente uma lógica Python simples. Se você quiser adicionar código mais complexo ou carregar bibliotecas Python adicionais, você deve usar o método zip file.

O ambiente de execução padrão usa a distribuição Anacondas do Python. Para obter uma lista completa de pacotes pré-instalados, consulte a página de referência do [módulo Execute Python Script.](algorithm-module-reference/execute-python-script.md)

![Executar mapa de entrada Python](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Executar Python escrito no designer

### <a name="add-the-execute-python-script-module"></a>Adicionar o módulo Execute Python Script

1. Encontre o módulo **Execute Python Script** na paleta de designer. Ele pode ser encontrado na seção **Linguagem Python.**

1. Arraste e solte o módulo para a tela do gasoduto.

### <a name="connect-input-datasets"></a>Conectar conjuntos de dados de entrada

Este artigo usa o conjunto de dados de amostra, **dados de preço de automóveis (Raw)**. 

1. Arraste e solte seu conjunto de dados para a tela do pipeline.

1. Conecte a porta de saída do conjunto de dados à porta de entrada superior esquerda do módulo **Execute Python Script.** O designer expõe a entrada como um parâmetro para o script do ponto de entrada.
    
    A porta de entrada certa é reservada para bibliotecas python com zíper.

    ![Conectar conjuntos de dados](media/how-to-designer-python/connect-dataset.png)
        

1. Observe qual porta de entrada você usa. O designer atribui a porta de `dataset1` entrada esquerda à `dataset2`variável e à porta de entrada do meio a . 

Os módulos de entrada são opcionais, pois você pode gerar ou importar dados diretamente no módulo **Execute Python Script.**

### <a name="write-your-python-code"></a>Escreva seu código Python

O designer fornece um script de ponto de entrada inicial para você editar e inserir seu próprio código Python. 

Neste exemplo, você usa Pandas para combinar duas colunas encontradas no conjunto de dados do automóvel, **Price** and **Horsepower,** para criar uma nova coluna, **Dólares por potência.** Esta coluna representa quanto você paga por cada potência, o que pode ser um recurso útil para decidir se um carro é um bom negócio para o dinheiro. 

1. Selecione o módulo **Execute Python Script.**

1. No painel que aparece à direita da tela, selecione a caixa de texto **do script Python.**

1. Copie e cole o seguinte código na caixa de texto.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Seu pipeline deve olhar a seguinte imagem:
    
    ![Executar pipeline Python](media/how-to-designer-python/execute-python-pipeline.png)

    O script do ponto `azureml_main`de entrada deve conter a função . Existem dois parâmetros de função que mapeiam as duas portas de entrada para o módulo **Execute Python Script.**

    O valor de retorno deve ser um Dataframe pandas. Você pode retornar até dois dataframes como saídas de módulo.
    
1. Envie o oleoduto.

Agora, você tem um conjunto de dados com o novo recurso **Dollars/HP,** que pode ser útil no treinamento de um recomendador de carros. Este é um exemplo de extração de recursos e redução da dimensionalidade. 

## <a name="next-steps"></a>Próximas etapas

Saiba como [importar seus próprios dados](how-to-designer-import-data.md) no designer de Machine Learning do Azure.