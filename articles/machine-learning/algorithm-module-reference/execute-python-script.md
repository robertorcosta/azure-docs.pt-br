---
title: 'Executar script python: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Execute Python Script no Azure Machine Learning para executar o código Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 0f86d1ad03062797764af6a0d49beacaa3458a8f
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365552"
---
# <a name="execute-python-script-module"></a>Executar módulo de script python

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para executar o código Python. Para obter mais informações sobre os princípios de arquitetura e design do Python, consulte [o artigo a seguir](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts).

Com o Python, você pode executar tarefas que não são suportadas atualmente por módulos existentes, como:

+ Visualização de dados usando`matplotlib`
+ Usando bibliotecas Python para enumerar conjuntos de dados e modelos em seu espaço de trabalho
+ Leitura, carregamento e manipulação de dados de fontes não suportadas pelo módulo [Importar dados](./import-data.md)
+ Execute seu próprio código de aprendizagem profunda 


O Azure Machine Learning usa a distribuição Anaconda do Python, que inclui muitos utilitários comuns para processamento de dados. Atualizaremos a versão anaconda automaticamente. A versão atual é:
 -  Distribuição Anaconda 4.5+ para Python 3.6 

Os pacotes pré-instalados são:
-    adal==1,2,2
-    applicationinsights===0.11.9
-    attrs==19,3.0
-    azure-common==1.1.25
-    azure-core==1.3.0
-    azure-graphrbac==0,61.1
-    azure-identidade==1.3.0
-    azure-mgmt-autorização==0.60.0
-    azure-mgmt-containerregistry==2.8.0
-    azure-mgmt-keyvault==2.2.0
-    azure-mgmt-resource==8.0.1
-    azure-mgmt-storage=8.0.0
-    azure-storage-blob==1.5.0
-    azure-armazenamento-comum==1.4.2
-    azureml-core==1.1.5.5
-    azureml-dataprep-native==14.1.0
-    azureml-dataprep==1.3.5
-    azureml-defaults==1.1.5.1
-    azureml-designer-clássico-módulos==0.0.118
-    azureml-designer-core==0.0.31
-    azureml-designer-interno=0.0.18
-    azureml-modelo-management-sdk==1.0.1b6.post1
-    azureml-pipeline-core==1.1.5
-    azureml-telemetria==1.1.5.3
-    backports.tempfile==1.0
-    backports.weakref==1.0.post1
-    boto3==1,12,29
-    botocore==1,15,29
-    cachetools==4.0.0
-    certifi==2019.11.28
-    cffi==1,12,3
-    chardet==3.0.4
-    click==7.1.1
-    cloudpickle===1.3.0
-    configparser==3,7,4
-    contextlib2===0.6.0.post1
-    criptografia==2,8
-    cycler===0,10.0
-    dill==0,3.1.1
-    distro==1.4.0
-    docker==4.2.0
-    docutils==0,15.2
-    dotnetcore2==2,1,13
-    frasco===1,0,3
-    fusível===3,0,1
-    gensim==3,8,1
-    google-api-core==1.16.0
-    google-auth==1.12.0
-    google-cloud-core==1.3.0
-    google-cloud-storage==1.26.0
-    google-resumable-media==0.5.0
-    googleapis-common-protos==1.51.0
-    gunicorn==19,9.0
-    idna==2,9
-    desequilibrado-learn==0,4,3
-    isótdate==0,6,0
-    seuperigoso===1.1.0
-    jeepney===0,4,3
-    jinja2==2,11.1
-    jmespath==0,9,5
-    joblib===0,14.0
-    json-logging-py==0.2
-    jsonpickle==1.3
-    jsonschema==3.0.1
-    kiwisolver==1.1.0
-    liac-arff==2.4.0
-    lightgbm==2.2.3
-    markupsafe==1.1.1
-    matplotlib==3.1.3
-    mais-itertools==6.0.0
-    msal-extensões==0,1,3
-    msal===1.1.0
-    msrest==0,6,11
-    msrestazure==0,6,3
-    ndg-httpsclient==0,5.1
-    nimbusml===1.6.1
-    numpy==1,18,2
-    oauthlib==3.1.0
-    pandas==0,25.3
-    pathspec===0.7.0
-    pip==20,0,2
-    portalocker===1.6.0
-    protobuf==3,11,3
-    pyarrow ==0.16.0
-    pyasn1-módulos==0,2.8
-    pyasn1==0,4,8
-    pycparser==2,20
-    pycryptodomex==3.7.3
-    pyjwt==1,7.1
-    pyopenssl==19.1.0
-    pyparsing==2,4,6
-    pirsistent==0,16.0
-    python-dateutil==2.8.1
-    pytz==2019.3
-    requests-oauthlib==1.3.0
-    solicitações==2,23.0
-    rsa==4,0
-    ruamel.yaml==0,15,89
-    s3transfer==0,3.3
-    scikit-learn==0.22.2
-    scipy==1,4,1
-    secretstorage==3.1.2
-    ferramentas de configuração==46,1.1.post20200323
-    seis==1,14,0
-    smart-open===1.10.0
-    urllib3==1,25,8
-    websocket-client==0.57.0
-    werkzeug===0,16.1
-    roda==0,34,2

 Para instalar outros pacotes que não estão na lista pré-instalada, por exemplo, *scikit-misc,* adicione o seguinte código ao seu script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Carregar arquivos
O **Script Execute Python** suporta upload de arquivos usando o [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

O exemplo a seguir mostra como carregar um arquivo de imagem no módulo **Execute Python Script:**

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

Depois que a execução do pipeline estiver concluída, você pode visualizar a imagem no painel direito do módulo

> [!div class="mx-imgBorder"]
> ![Imagem enviada](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>Como configurar o Script Execute Python

O módulo **Execute Python Script** contém código Python de amostra que você pode usar como ponto de partida. Para configurar o módulo **Execute Python Script,** você fornece um conjunto de entradas e código Python para executar na caixa de texto **do script Python.**

1. Adicione o módulo **Execute Python Script** ao seu pipeline.

2. Adicione e conecte no **Dataset1** quaisquer conjuntos de dados do designer que você deseja usar para entrada. Faça referência a esse conjunto de dados no script Python como **DataFrame1**.

    O uso de um conjunto de dados é opcional, se você quiser gerar dados usando Python, ou usar código Python para importar os dados diretamente para o módulo.

    Este módulo suporta a adição de um segundo conjunto de dados no **Dataset2**. Faça referência ao segundo conjunto de dados em seu script Python como DataFrame2.

    Os conjuntos de dados armazenados no Azure Machine Learning são automaticamente convertidos em **pandas** data.frames quando carregados com este módulo.

    ![Executar mapa de entrada Python](media/module/python-module.png)

4. Para incluir novos pacotes ou código Python, adicione o arquivo com zíper contendo esses recursos personalizados no **pacote Script**. A entrada para **o pacote Script** deve ser um arquivo fechado carregado no seu espaço de trabalho como um conjunto de dados tipo arquivo. Você pode carregar o conjunto de dados na página de ativos **Datasets** e pode arrastar e soltar o módulo de conjunto de dados da lista **Meus conjuntos** de dados na árvore do módulo esquerdo na página de autoria do designer. 

    Qualquer arquivo contido no arquivo fechado enviado pode ser usado durante a execução do pipeline. Se o arquivo incluir uma estrutura de diretório, a estrutura será preservada, mas você deve preparar um diretório chamado **src** para o caminho.

5. Na caixa de texto **do script Python,** digite ou cole o script Python válido.

    A caixa de texto **do script Python** é pré-preenchida com algumas instruções nos comentários e código de exemplo para acesso e saída de dados. Você deve editar ou substituir este código. Certifique-se de seguir as convenções Python sobre recuo e invólucro.

    + O script deve conter `azureml_main` uma função nomeada como o ponto de entrada deste módulo.
    + A função de ponto de entrada pode `Param<dataframe1>` conter até dois argumentos de entrada: e`Param<dataframe2>`
    + Os arquivos com zíper conectados à terceira porta de `.\Script Bundle`entrada são descompactados e armazenados no diretório, que também é adicionado ao Python `sys.path`. 

    Portanto, se o seu `mymodule.py`arquivo zip `import mymodule`contiver, importe-o usando .

    + Dois conjuntos de dados podem ser devolvidos ao `pandas.DataFrame`designer, que deve ser uma seqüência do tipo . Você pode criar outras saídas em seu código Python e escrevê-las diretamente no armazenamento do Azure.

6. Envie o pipeline ou selecione o módulo e clique **em Executar selecionado** para executar apenas o script Python.

    Todos os dados e códigos são carregados em uma máquina virtual e executados usando o ambiente Python especificado.

## <a name="results"></a>Resultados

Os resultados de quaisquer cálculos realizados pelo código Python incorporado devem ser fornecidos como pandas. DataFrame, que é convertido automaticamente para o formato de conjunto de dados azure Machine Learning, para que você possa usar os resultados com outros módulos no pipeline.

O módulo retorna dois conjuntos de dados:  
  
+ **Resultados Dataset 1**, definido pelo primeiro dataframe pandas retornado no script Python

+ **Resultado Dataset 2**, definido pelo segundo dataframe de pandas retornado no script Python


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 