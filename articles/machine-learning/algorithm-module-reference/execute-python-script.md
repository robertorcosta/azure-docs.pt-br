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
ms.openlocfilehash: 79dc1b188e91028a98f43dc24972228f2d2101be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684721"
---
# <a name="execute-python-script-module"></a>Executar módulo de script Python

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para executar o código Python. Para obter mais informações sobre os princípios de arquitetura e design do Python, consulte [o artigo a seguir](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

Com o Python, você pode executar tarefas que atualmente não são suportadas por módulos existentes, como:

+ Visualizando dados usando`matplotlib`
+ Usando bibliotecas do Python para enumerar conjuntos de valores e modelos em seu espaço de trabalho
+ Leitura, carregamento e manipulação de dados de fontes sem suporte pelo módulo [importar dados](./import-data.md)
+ Execute seu próprio código de aprendizado profundo 


Azure Machine Learning usa a distribuição Anaconda do Python, que inclui muitos utilitários comuns para processamento de dados. Atualizaremos a versão do Anaconda automaticamente. A versão atual é:
 -  Anaconda 4.5 + distribuição para Python 3,6 

Os pacotes pré-instalados são:
-    Adal = = 1.2.2
-    applicationinsights = = 0.11.9
-    attrs = = 19.3.0
-    Azure-comum = = 1.1.25
-    Azure-Core = = 1.3.0
-    Azure-graphrbac = = 0.61.1
-    Azure-Identity = = 1.3.0
-    Azure-MGMT-Authorization = = 0.60.0
-    Azure-MGMT-containerregistry = = 2.8.0
-    Azure-MGMT-keyvault = = 2.2.0
-    Azure-MGMT-Resource = = 8.0.1
-    Azure-MGMT-Storage = = 8.0.0
-    Azure-Storage-blob = = 1.5.0
-    Azure-Storage-comum = = 1.4.2
-    azureml-Core = = 1.1.5.5
-    azureml-dataprep-nativo = = 14.1.0
-    azureml-dataprep = = 1.3.5
-    azureml-padrões = = 1.1.5.1
-    azureml-designer-clássico-modules = = 0.0.118
-    azureml-designer-Core = = 0.0.31
-    azureml-designer-interno = = 0.0.18
-    azureml-Model-Management-SDK = = 1.0.1 B6. post1
-    azureml-pipeline-Core = = 1.1.5
-    azureml-telemetria = = 1.1.5.3
-    backports. tempfile = = 1,0
-    backports. weakref = = 1,0. post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0.0
-    Certificates = = 2019.11.28
-    cffi = = 1.12.3
-    chardet = = 3.0.4
-    clique em = = 7.1.1
-    cloudpickle = = 1.3.0
-    ConfigParser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    criptografia = = 2.8
-    cycler = = 0.10.0
-    analisar detalhadamente = = 0.3.1.1
-    distribuição = = 1.4.0
-    Docker = = 4.2.0
-    Docutils = = 0.15.2
-    dotnetcore2 = = 2.1.13
-    Flask = = 1.0.3
-    fusepy = = 3.0.1
-    gensim = = 3.8.1
-    Google-API-Core = = 1.16.0
-    Google-auth = = 1.12.0
-    Google-Cloud-Core = = 1.3.0
-    Google-Cloud-Storage = = 1.26.0
-    Google-retomável-mídia = = 0.5.0
-    googleapis-Common-Protos = = 1.51.0
-    gunicorn = = 19.9.0
-    IDNA = = 2.9
-    desbalanceado-Learn = = 0.4.3
-    isodate = = 0.6.0
-    itsdangerous = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-Logging-py = = 0.2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver = = 1.1.0
-    Liac-ARFF = = 2.4.0
-    lightgbm = = 2.2.3
-    markupsafe = = 1.1.1
-    matplotlib = = 3.1.3
-    mais-itertools = = 6.0.0
-    MSAL-Extensions = = 0.1.3
-    MSAL = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    NDG-httpsclient = = 0.5.1
-    nimbusml = = 1.6.1
-    numpy = = 1.18.2
-    oauthlib = = 3.1.0
-    pandas = = 0.25.3
-    pathspec = = 0.7.0
-    Pip = = 20.0.2
-    portalocker = = 1.6.0
-    protobuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1-modules = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2,20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    Python-DateUtil = = 2.8.1
-    pytz = = 2019.3
-    solicitações-oauthlib = = 1.3.0
-    solicitações = = 2.23.0
-    RSA = = 4.0
-    ruamel. YAML = = 0.15.89
-    s3transfer = = 0.3.3
-    scikit-Learn = = 0.22.2
-    SciPy = = 1.4.1
-    secretstorage = = 3.1.2
-    setuptools = = 46.1.1. post20200323
-    seis = = 1.14.0
-    Smart-Open = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket-cliente = = 0.57.0
-    Werkzeug = = 0.16.1
-    Wheel = = 0.34.2

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

# The entry point function must have two input arguments:
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

Depois que a execução do pipeline for concluída, você poderá visualizar a imagem no painel direito do módulo

> [!div class="mx-imgBorder"]
> ![Carregado-imagem](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Como configurar o script Python de execução

O módulo **Executar script Python** contém o código Python de exemplo que você pode usar como ponto de partida. Para configurar o módulo **Executar script Python** , você fornece um conjunto de entradas e código Python para executar na caixa de texto **script Python** .

1. Adicione o módulo **Executar script Python** ao seu pipeline.

2. Adicione e conecte-se em **dataSet1** quaisquer conjuntos de dados do designer que você deseja usar para entrada. Referencie este conjunto de DataSet em seu script Python como **DataFrame1**.

    O uso de um conjunto de dados é opcional, se você quiser gerar dados usando o Python ou usar o código Python para importar os dados diretamente para o módulo.

    Este módulo dá suporte à adição de um segundo conjunto de uma em **Dataset2**. Referencie o segundo conjunto de DataSet em seu script Python como DataFrame2.

    Os conjuntos de dados armazenados em Azure Machine Learning são convertidos automaticamente em **pandas** Data. frames quando carregados com esse módulo.

    ![Executar o mapa de entrada do Python](media/module/python-module.png)

4. Para incluir novos pacotes ou códigos do Python, adicione o arquivo compactado que contém esses recursos personalizados no **pacote de script**. A entrada para o **grupo de script** deve ser um arquivo compactado carregado em seu espaço de trabalho como um tipo de arquivo de conjunto de dados. Você pode carregar o conjunto de recursos na página de ativos de **conjuntos de valores** e pode arrastar e soltar o módulo DataSet da lista **meus conjuntos de valores** na árvore de módulos à esquerda na página de criação do designer. 

    Qualquer arquivo contido no arquivo compactado carregado pode ser usado durante a execução do pipeline. Se o arquivo incluir uma estrutura de diretório, a estrutura será preservada, mas você deverá preceder um diretório chamado **src** para o caminho.

5. Na caixa de texto **script do Python** , digite ou cole script Python válido.

    > [!NOTE]
    > Tenha cuidado ao escrever seu script e verifique se não há nenhum erro de sintaxe, como usar um objeto não declarado ou um módulo não importado. Além disso, preste atenção extra à lista de módulos pré-instaladas. Para importar módulos que não estão listados, instale os pacotes correspondentes em seu script, como
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    A caixa de texto **script Python** é preenchida previamente com algumas instruções em comentários e código de exemplo para acesso a dados e saída. Você deve editar ou substituir este código. Certifique-se de seguir as convenções do Python sobre recuos e maiúsculas e minúsculas.

    + O script deve conter uma função chamada `azureml_main` como ponto de entrada para este módulo.
    + A função de ponto de entrada deve ter dois argumentos `Param<dataframe1>` de `Param<dataframe2>`entrada: e, mesmo quando esses argumentos não são usados em seu script.
    + Os arquivos compactados conectados à terceira porta de entrada são descompactados e armazenados no `.\Script Bundle`diretório, que também é adicionado ao Python `sys.path`. 

    Portanto, se o arquivo zip contiver `mymodule.py`, importe- `import mymodule`o usando.

    + Dois conjuntos de valores podem ser retornados para o designer, que deve ser uma sequência do `pandas.DataFrame`tipo. Você pode criar outras saídas em seu código Python e gravá-las diretamente no armazenamento do Azure.

6. Envie o pipeline ou selecione o módulo e clique em **executar selecionado** para executar apenas o script Python.

    Todos os dados e o código são carregados em uma máquina virtual e executados usando o ambiente do Python especificado.

## <a name="results"></a>Resultados

Os resultados de qualquer computação executada pelo código Python inserido devem ser fornecidos como pandas. Dataframe, que é convertido automaticamente no formato de conjunto de Azure Machine Learning, para que você possa usar os resultados com outros módulos no pipeline.

O módulo retorna dois conjuntos de valores:  
  
+ **Conjunto de resultados 1**, definido pela primeira vez que o banco de dataframe do Panda retornado no script Python

+ O **conjunto de resultados de resultado 2**, definido pelo segundo retornou o dataframe pandas no script Python


## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 