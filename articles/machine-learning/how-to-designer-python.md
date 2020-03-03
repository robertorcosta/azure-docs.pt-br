---
title: Python
titleSuffix: Azure Machine Learning
description: Saiba como usar o Python no designer de Azure Machine Learning para transformar dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: b0b0bb5eefde9e744b1f30109d60ded91d3b44e8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228678"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Executar código Python no designer de Azure Machine Learning

Neste artigo, você aprenderá a usar o módulo [Executar script Python](algorithm-module-reference/execute-python-script.md) para adicionar lógica personalizada ao designer de Azure Machine Learning. Nas instruções a seguir, você usa a biblioteca pandas para fazer engenharia de recursos simples.

Você pode usar o editor de código interno para adicionar rapidamente uma lógica simples do Python. Se você quiser adicionar um código mais complexo ou carregar bibliotecas adicionais do Python, use o método de arquivo zip.

O ambiente de execução padrão usa a distribuição Anacondas do Python. Para obter uma lista completa de pacotes pré-instalados, consulte a página de [referência executar módulo de script Python](algorithm-module-reference/execute-python-script.md) .

![Executar o mapa de entrada do Python](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Executar o Python escrito no designer

### <a name="add-the-execute-python-script-module"></a>Adicionar o módulo executar script Python

1. Localize o módulo **Executar script Python** na paleta do designer. Ele pode ser encontrado na seção **idioma do Python** .

1. Arraste e solte o módulo na tela do pipeline.

### <a name="connect-input-datasets"></a>Conectar conjuntos de dados de entrada

Este artigo usa o conjunto de dados de exemplo, o **preço do automóvel (bruto)** . 

1. Arraste e solte o conjunto de seus conjuntos de os para a tela do pipeline.

1. Conecte a porta de saída do conjunto de dados à porta de entrada superior esquerda do módulo **Executar script Python** . O designer expõe a entrada como um parâmetro para o script de ponto de entrada.
    
    A porta de entrada à direita é reservada para bibliotecas do Python compactadas.

    ![Conectar conjuntos de os](media/how-to-designer-python/connect-dataset.png)
        

1. Anote a porta de entrada que você usa. O Designer atribui a porta de entrada à esquerda para a variável `dataset1` e a porta de entrada intermediária para `dataset2`. 

Os módulos de entrada são opcionais, já que você pode gerar ou importar dados diretamente no módulo **Executar script Python** .

### <a name="write-your-python-code"></a>Escreva seu código Python

O designer fornece um script de ponto de entrada inicial para você editar e inserir seu próprio código Python. 

Neste exemplo, você usa pandas para combinar duas colunas encontradas no conjunto de linhas do automóvel, **preço** e **potência**, para criar uma nova coluna, **dólares por cavalo**. Esta coluna representa o quanto você paga para cada potência, o que pode ser um recurso útil para decidir se um carro é um bom negócio para o dinheiro. 

1. Selecione o módulo **Executar script Python** .

1. No painel que aparece à direita da tela, selecione a caixa de texto **script Python** .

1. Copie e cole o código a seguir na caixa de texto.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Seu pipeline deve ter a seguinte imagem:
    
    ![Executar pipeline Python](media/how-to-designer-python/execute-python-pipeline.png)

    O script de ponto de entrada deve conter a função `azureml_main`. Há dois parâmetros de função que são mapeados para as duas portas de entrada para o módulo **Executar script Python** .

    O valor de retorno deve ser um dataframe do pandas. Você pode retornar até dois quadros de molduras como saídas de módulo.
    
1. Execute o pipeline.

Agora, você tem um conjunto de recursos com o novo recurso **dólares/HP**, que pode ser útil para treinar um recomendador de carros. Este é um exemplo de extração de recursos e redução de dimensionalidade. 

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba como [importar seus próprios dados](how-to-designer-import-data.md) no designer de Azure Machine Learning.