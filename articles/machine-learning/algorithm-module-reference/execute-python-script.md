---
title: 'Executar script Python: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo executar script Python no Azure Machine Learning para executar o código Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 52eb3bdb463389d075421661610b5ee94d14d77d
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037078"
---
# <a name="execute-python-script-module"></a>Executar módulo de script Python

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para executar o código Python. Para obter mais informações sobre os princípios de arquitetura e design do Python, consulte [o artigo a seguir](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

Com o Python, você pode executar tarefas que atualmente não são suportadas por módulos existentes, como:

+ Visualizando dados usando `matplotlib`
+ Usando bibliotecas do Python para enumerar conjuntos de valores e modelos em seu espaço de trabalho
+ Leitura, carregamento e manipulação de dados de fontes sem suporte pelo módulo [importar dados](./import-data.md)
+ Execute seu próprio código de aprendizado profundo 


Azure Machine Learning usa a distribuição Anaconda do Python, que inclui muitos utilitários comuns para processamento de dados. Atualizaremos a versão do Anaconda automaticamente. A versão atual é:
 -  Anaconda 4.5 + distribuição para Python 3,6 

Os pacotes pré-instalados são:
-  asn1crypto = = 0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- Certificates = = 2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distribuição = = 1.4.0
- IDNA = = 2,8
- jsonschema==3.0.1
- lightgbm = = 2.2.3
- more-itertools==6.0.0
- numpy = = 1.16.2
- pandas = = 0.24.2
- Pillow = = 6.0.0
- pip==19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex = = 3.7.3
- pyrsistent = = 0.14.11
- python-dateutil==2.8.0
- pytz = = 2018.9
- solicitações = = 2.21.0
- scikit-Learn = = 0.20.3
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3 = = 1.24.1
- Wheel = = 0.33.1 

 Para instalar outros pacotes que não estão na lista pré-instalada, por exemplo, *scikit-misc*, adicione o seguinte código ao seu script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Carregar arquivos
O **script executar Python** dá suporte ao carregamento de arquivos usando [Azure Machine Learning SDK do Python](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

O exemplo a seguir mostra como carregar um arquivo de imagem no módulo **Executar script Python** :

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Depois que o pipeline for enviado com êxito, você poderá visualizar a imagem no painel direito do módulo ![carregada-Image](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Como configurar o script Python de execução

O módulo **Executar script Python** contém o código Python de exemplo que você pode usar como ponto de partida. Para configurar o módulo **Executar script Python** , você fornece um conjunto de entradas e código Python para executar na caixa de texto **script Python** .

1. Adicione o módulo **Executar script Python** ao seu pipeline.

2. Adicione e conecte-se em **dataSet1** quaisquer conjuntos de dados do designer que você deseja usar para entrada. Referencie este conjunto de DataSet em seu script Python como **DataFrame1**.

    O uso de um conjunto de dados é opcional, se você quiser gerar dados usando o Python ou usar o código Python para importar os dados diretamente para o módulo.

    Este módulo dá suporte à adição de um segundo conjunto de uma em **Dataset2**. Referencie o segundo conjunto de DataSet em seu script Python como DataFrame2.

    Os conjuntos de dados armazenados em Azure Machine Learning são convertidos automaticamente em **pandas** Data. frames quando carregados com esse módulo.

    ![Executar o mapa de entrada do Python](media/module/python-module.png)

4. Para incluir novos pacotes ou códigos do Python, adicione o arquivo compactado que contém esses recursos personalizados no **pacote de script**. A entrada para o **grupo de script** deve ser um arquivo compactado carregado em seu espaço de trabalho como um tipo de arquivo de conjunto de dados. 

    Qualquer arquivo contido no arquivo compactado carregado pode ser usado durante a execução do pipeline. Se o arquivo incluir uma estrutura de diretório, a estrutura será preservada, mas você deverá preceder um diretório chamado **src** para o caminho.

5. Na caixa de texto **script do Python** , digite ou cole script Python válido.

    A caixa de texto **script Python** é preenchida previamente com algumas instruções em comentários e código de exemplo para acesso a dados e saída. Você deve editar ou substituir este código. Certifique-se de seguir as convenções do Python sobre recuos e maiúsculas e minúsculas.

    + O script deve conter uma função chamada `azureml_main` como o ponto de entrada para este módulo.
    + A função de ponto de entrada pode conter até dois argumentos de entrada: `Param<dataframe1>` e `Param<dataframe2>`
    + Os arquivos compactados conectados à terceira porta de entrada são descompactados e armazenados no diretório, `.\Script Bundle`, que também é adicionado ao `sys.path`Python. 

    Portanto, se o arquivo zip contiver `mymodule.py`, importe-o usando `import mymodule`.

    + Dois conjuntos de valores podem ser retornados para o designer, que deve ser uma sequência do tipo `pandas.DataFrame`. Você pode criar outras saídas em seu código Python e gravá-las diretamente no armazenamento do Azure.

6. Execute o pipeline ou selecione o módulo e clique em **executar selecionado** para executar apenas o script Python.

    Todos os dados e o código são carregados em uma máquina virtual e executados usando o ambiente do Python especificado.

## <a name="results"></a>Resultados

Os resultados de qualquer computação executada pelo código Python inserido devem ser fornecidos como pandas. Dataframe, que é convertido automaticamente no formato de conjunto de Azure Machine Learning, para que você possa usar os resultados com outros módulos no pipeline.

O módulo retorna dois conjuntos de valores:  
  
+ **Conjunto de resultados 1**, definido pela primeira vez que o banco de dataframe do Panda retornado no script Python

+ O **conjunto de resultados de resultado 2**, definido pelo segundo retornou o dataframe pandas no script Python


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 