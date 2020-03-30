---
title: Executar scripts Python
titleSuffix: ML Studio (classic) - Azure
description: Aprenda a usar o módulo Execute Python Script para usar o código Python em experimentos e serviços web do Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: c79f6bd63fa5d8d8c6b22ff271d8ca513a94fd64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218075"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Execute scripts de aprendizado de máquina Python no Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Python é uma ferramenta valiosa no baú de ferramentas de muitos cientistas de dados. É usado em todas as etapas de fluxos típicos de aprendizado de máquina, incluindo exploração de dados, extração de recursos, treinamento e validação de modelos e implantação.

Este artigo descreve como você pode usar o módulo Execute Python Script para usar o código Python em seus experimentos e serviços web do Azure Machine Learning Studio (clássico).

## <a name="using-the-execute-python-script-module"></a>Usando o módulo Execute Python Script

A interface principal do Python in Studio (clássico) é através do módulo [Execute Python Script.][execute-python-script] Ele aceita até três entradas e produz até duas saídas, semelhante ao módulo [Execute R Script.][execute-r-script] O código Python é inserido na caixa de parâmetros `azureml_main`através de uma função de ponto de entrada especialmente chamada .

![Executar módulo de script python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Código python de amostra na caixa de parâmetros do módulo](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parâmetros de entrada

As entradas para o módulo Python são expostas como DataFrames pandas. A `azureml_main` função aceita até dois DataFrames pandas opcionais como parâmetros.

O mapeamento entre as portas de entrada e os parâmetros de função é posicional:

- A primeira porta de entrada conectada é mapeada para o primeiro parâmetro da função.
- A segunda entrada (se conectada) é mapeada para o segundo parâmetro da função.
- A terceira entrada é usada para [importar módulos Python adicionais](#import-modules).

Semântica mais detalhada de como as portas `azureml_main` de entrada são mapeadas para parâmetros da função são mostradas abaixo.

![Tabela de configurações de porta de entrada e assinatura Python resultante](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Valores de retorno de saída

A `azureml_main` função deve retornar um único DataFrame pandas embalado em uma [seqüência](https://docs.python.org/2/c-api/sequence.html) Python, como uma matriz tuple, list ou NumPy. O primeiro elemento desta seqüência é devolvido à primeira porta de saída do módulo. A segunda porta de saída do módulo é usada para [visualizações](#visualizations) e não requer um valor de retorno. Este esquema é mostrado abaixo.

![Mapeando portas de entrada para parâmetros e valor de retorno para a porta de saída](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Tradução de tipos de dados de entrada e saída

Os conjuntos de dados do estúdio não são os mesmos do Panda DataFrames. Como resultado, os conjuntos de dados de entrada no Studio (clássico) são convertidos em Pandas DataFrame, e os DataFrames de saída são convertidos de volta para conjuntos de dados studio (clássico). Durante este processo de conversão, as seguintes traduções também são realizadas:

 **Tipo de dados Python** | **Procedimento de tradução de estúdio** |
| --- | --- |
| Cordas e numéricos| Traduzido como é |
| Pandas 'NA' | Traduzido como 'Valor perdido' |
| Vetores de índice | Sem suporte* |
| Nomes de colunas não-string | Chamada `str` sobre nomes de colunas |
| Nomes de colunas duplicadas | Adicionar sufixo numérico: (1), (2), (3) e assim por diante.

**Todos os quadros de dados de entrada na função Python sempre têm um índice numérico de 64 bits de 0 para o número de linhas menos 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Importando módulos de script Python existentes

O backend usado para executar Python é baseado em [Anaconda](https://www.anaconda.com/distribution/), uma distribuição científica de Python amplamente utilizada. Ele vem com cerca de 200 dos pacotes Python mais comuns usados em cargas de trabalho centradas em dados. O Studio (clássico) não suporta atualmente o uso de sistemas de gerenciamento de pacotes como Pip ou Conda para instalar e gerenciar bibliotecas externas.  Se você encontrar a necessidade de incorporar bibliotecas adicionais, use o seguinte cenário como guia.

Um caso de uso comum é incorporar scripts Python existentes em experimentos do Studio (clássico). O módulo [Execute Python Script][execute-python-script] aceita um arquivo zip contendo módulos Python na terceira porta de entrada. O arquivo é descompactado pela estrutura de execução no runtime e o conteúdo é adicionado ao caminho da biblioteca do interpretador de Python. A função do ponto de entrada `azureml_main` pode, então, importar esses módulos diretamente. 

Como exemplo, considere o arquivo Hello.py contendo uma simples função "Olá, Mundo".

![Função definida pelo usuário no arquivo Hello.py](./media/execute-python-scripts/figure4.png)

Em seguida, criamos um arquivo Hello.zip que contenha o Hello.py:

![Arquivo zip contendo código Python definido pelo usuário](./media/execute-python-scripts/figure5.png)

Carregue o arquivo zip como um conjunto de dados no Studio (clássico). Em seguida, crie e execute um experimento que usa o código Python no arquivo Hello.zip anexando-o à terceira porta de entrada do módulo **Execute Python Script,** conforme mostrado na imagem a seguir.

![Experimento de exemplo com Hello.zip como uma entrada para um módulo De script Python executar](./media/execute-python-scripts/figure6a.png)

![Código Python definido pelo usuário carregado como um arquivo zip](./media/execute-python-scripts/figure6b.png)

A saída do módulo mostra que o arquivo zip foi descompactado e a função `print_hello` foi executada.

![Saída do módulo mostrando função definida pelo usuário](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Acessando blobs de armazenamento azure

Você pode acessar dados armazenados em uma conta do Azure Blob Storage usando essas etapas:

1. Baixe o [pacote Azure Blob Storage para Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) localmente.
1. Carregue o arquivo zip para o espaço de trabalho do studio (clássico) como um conjunto de dados.
1. Crie seu objeto BlobService com`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Desativar **transferência segura necessária** na guia **de configuração de configuração** de armazenamento

![Desativar transferência segura necessária no portal Azure](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Operacionalizando scripts Python

Quaisquer módulos [Executar Script Python][execute-python-script] usados em um teste de pontuação são chamados quando publicados como um serviço Web. Por exemplo, a imagem abaixo mostra um experimento de pontuação que contém o código para avaliar uma única expressão Python.

![Espaço de trabalho do estúdio para um serviço web](./media/execute-python-scripts/figure3a.png)

![Expressão Python Pandas](./media/execute-python-scripts/python-script-with-python-pandas.png)

Um serviço web criado a partir deste experimento tomaria as seguintes ações:

1. Tome uma expressão Python como entrada (como uma string)
1. Envie a expressão Python para o interpretador Python
1. Retorna uma tabela contendo tanto a expressão quanto o resultado avaliado.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Trabalhando com visualizações

Os plots criados usando MatplotLib podem ser devolvidos pelo [Script Execute Python][execute-python-script]. No entanto, os plots não são automaticamente redirecionados para imagens como são ao usar R. Assim, o usuário deve salvar explicitamente quaisquer parcelas em arquivos PNG.

Para gerar imagens de MatplotLib, você deve tomar as seguintes etapas:

1. Mude o backend para "AGG" do renderizador padrão baseado em Qt.
1. Crie um novo objeto de figura.
1. Pegue o eixo e gere todas as parcelas nele.
1. Salve a figura em um arquivo PNG.

Este processo é ilustrado nas imagens a seguir que criam uma matriz de plotagem de dispersão usando a função scatter_matrix em Pandas.

![Código para salvar figuras matplotLib em imagens](./media/execute-python-scripts/figure-v1-8.png)

![Clique em visualizar em um módulo Execute Python Script para visualizar as figuras](./media/execute-python-scripts/figure-v2-9a.png)

![Visualização de parcelas para um experimento de amostra usando código Python](./media/execute-python-scripts/figure-v2-9b.png)

É possível retornar várias figuras salvando-as em diferentes imagens. Studio (clássico) runtime pega todas as imagens e as concatena para visualização.

## <a name="advanced-examples"></a>Exemplos avançados

O ambiente Anaconda instalado no Studio (clássico) contém pacotes comuns como NumPy, SciPy e Scikits-Learn. Esses pacotes podem ser efetivamente usados para o processamento de dados em um pipeline de aprendizado de máquina.

Por exemplo, o experimento e o script a seguir ilustram o uso de alunos de conjuntos em Scikits-Learn para calcular pontuações de importância de recursos para um conjunto de dados. As pontuações podem ser usadas para realizar a seleção supervisionada de recursos antes de serem alimentadas em outro modelo.

Aqui está a função Python usada para calcular as pontuações de importância e ordenar os recursos com base nas pontuações:

![Função para classificar recursos por pontuações](./media/execute-python-scripts/figure8.png)

O experimento a seguir então calcula e retorna as pontuações de importância dos recursos no conjunto de dados "Pima Indian Diabetes" no Azure Machine Learning Studio (clássico):

![Experimento para classificar recursos no conjunto de dados Pima Indian Diabetes usando Python](./media/execute-python-scripts/figure9a.png)

![Visualização da saída do módulo Execute Python Script](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Limitações

O módulo [Execute Python Script][execute-python-script] atualmente tem as seguintes limitações:

### <a name="sandboxed-execution"></a>Execução sandboxed

O tempo de execução do Python está atualmente sandboxed e não permite o acesso à rede ou ao sistema de arquivos local de forma persistente. Todos os arquivos salvos localmente são isolados e excluídos após a conclusão do módulo. O código Python não pode acessar a maioria das pastas do computador em que é executado, com exceção do diretório atual e seus subdiretórios.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Falta de suporte sofisticado de desenvolvimento e depuração

 O módulo Python atualmente não dá suporte a recursos de IDE, como IntelliSense e depuração. Além disso, se o módulo falhar em runtime, o rastreamento de pilha do Python completo estará disponível. Porém, ele deve ser exibido no log de saída para o módulo. No momento, recomendamos que você desenvolva e depure scripts Python em um ambiente como IPython e depois importe o código para o módulo.

### <a name="single-data-frame-output"></a>Saída de quadro de dados único

 O ponto de entrada do Python para retornar somente uma estrutura de dados como saída. Atualmente, não é possível retornar objetos Python arbitrários, como modelos treinados diretamente de volta ao tempo de execução do Studio (clássico). Assim como o módulo [Executar Script R][execute-r-script], que tem a mesma limitação, é possível em muitos casos serializar objetos em uma matriz de bytes e retorná-la dentro de um quadro de dados.

### <a name="inability-to-customize-python-installation"></a>Incapacidade de personalizar a instalação do Python

Atualmente, a única maneira de adicionar módulos personalizados do Python é por meio do mecanismo de compactação de arquivo zip descrito anteriormente. Embora isso seja viável para pequenos módulos, é complicado para módulos grandes (especialmente módulos com DLLs nativos) ou um grande número de módulos.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira o [Centro de Desenvolvedores do Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
