---
title: Transformar dados
titleSuffix: Azure Machine Learning
description: Saiba como transformar dados no designer de Azure Machine Learning para criar seus próprios conjuntos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 05/04/2020
ms.openlocfilehash: 5296ac54cab403ef78b3e8bd32fe5ebe6ea43119
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842871"
---
# <a name="transform-data-in-azure-machine-learning-designer-preview"></a>Transformar dados no designer de Azure Machine Learning (versão prévia)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, você aprenderá a transformar e salvar conjuntos de dados no designer de Azure Machine Learning para que possa preparar seus próprios dados para o aprendizado de máquina.

Você usará o conjunto de dados de [classificação binária de renda de censo adulto](sample-designer-datasets.md) de exemplo para preparar dois conjuntos de dados: um conjunto que inclui informações de censo somente para adultos do Estados Unidos e outro conjunto de dados que inclui informações de censo de pessoas que não são adultos.

Neste artigo, você aprenderá como:

1. Transforme um conjunto de um DataSet para prepará-lo para treinamento.
1. Exporte os conjuntos de resultados resultantes para um repositório de armazenamento.
1. Exibir resultados.

Este "como" é um pré-requisito para o artigo [como treinar modelos de designer](how-to-retrain-designer.md) . Nesse artigo, você aprenderá a usar os conjuntos de valores transformados para treinar vários modelos com parâmetros de pipeline.

## <a name="transform-a-dataset"></a>Transformar um conjunto de uma

Nesta seção, você aprenderá a importar o conjunto de dados de exemplo e a dividir os dados em conjuntos de data e fora dos EUA. Para obter mais informações sobre como importar seus próprios dados para o designer, consulte [como importar dados](how-to-designer-import-data.md).

### <a name="import-data"></a>Importar dados

Use as etapas a seguir para importar o conjunto de exemplos.

1. Entre em <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selecione o workspace com o qual deseja trabalhar.

1. Vá para o designer. Selecione **módulos de precompilação fáceis de usar** para criar um novo pipeline.

1. Selecione um destino de computação padrão para executar o pipeline.

1. À esquerda da tela do pipeline há uma paleta de conjuntos de dados e módulos. Selecione **conjuntos**de os. Em seguida, exiba a seção de **exemplos** .

1. Arraste e solte o conjunto de **censo de classificação binária de renda do adulto** para a tela.

1. Selecione o módulo de conjunto de **censo de renda de adulto** .

1. No painel de detalhes que aparece à direita da tela, selecione **saídas**.

1. Selecione o ícone Visualizar ![ícone de visualização](media/how-to-designer-transform-data/visualize-icon.png).

1. Use a janela de visualização de dados para explorar o conjunto. Tome nota especial dos valores de coluna "nativo-país".

### <a name="split-the-data"></a>Dividir os dados

Nesta seção, você usará o [módulo dividir dados](algorithm-module-reference/split-data.md) para identificar e dividir as linhas que contêm "Estados Unidos" na coluna "nativo-país". 

1. Na paleta de módulos à esquerda da tela, expanda a seção **transformação de dados** e localize o módulo **dividir dados** .

1. Arraste o módulo **dividir dados** para a tela e solte o módulo abaixo do módulo DataSet.

1. Conecte o módulo DataSet ao módulo **dividir dados** .

1. Selecione o módulo **Dividir dados**.

1. No painel detalhes do módulo à direita da tela, defina modo de **divisão** como **expressão regular**.

1. Insira a **expressão regular**: `\"native-country" United-States`.

    O modo de **expressão regular** testa uma única coluna para um valor. Para obter mais informações sobre o módulo dividir dados, consulte a [página de referência do módulo do algoritmo](algorithm-module-reference/split-data.md)relacionado.

Seu pipeline deve ter esta aparência:

![Captura de tela mostrando como configurar o pipeline e o módulo dividir dados](media/how-to-designer-transform-data/split-data.png).


## <a name="save-the-datasets"></a>Salvar os conjuntos de os

Agora que seu pipeline está configurado para dividir os dados, você precisa especificar onde deseja manter os DataSets. Para este exemplo, use o módulo **exportar dados** para salvar o conjunto de dados em um repositório de armazenamento. Para obter mais informações sobre armazenamentos de dados, consulte [conectar-se aos serviços de armazenamento do Azure](how-to-access-data.md)

1. Na paleta de módulos à esquerda da tela, expanda a seção **entrada e saída de dados** e localize o módulo **exportar dados** .

1. Arraste e solte dois módulos **exportar dados** abaixo do módulo **dividir dados** .

1. Conecte cada porta de saída do módulo **dividir dados** a um módulo de **dados de exportação** diferente.

    Seu pipeline deve ser semelhante a este:

    ![Captura de tela mostrando como conectar os módulos exportar dados](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Selecione o módulo **exportar dados** que está conectado à porta mais *à esquerda*do módulo **dividir dados** .

    A ordem das portas de saída importantes para o módulo **dividir dados** . A primeira porta de saída contém as linhas em que a expressão regular é verdadeira. Nesse caso, a primeira porta contém linhas para renda com base nos EUA e a segunda porta contém linhas para renda não baseada em EUA.

1. No painel detalhes do módulo à direita da tela, defina as seguintes opções:
    
    **Tipo de repositório de armazenamento**: armazenamento de BLOBs do Azure

    **Repositório de armazenamento**: selecione um repositório de armazenamento existente ou selecione "novo repositório de armazenamento" para criar um agora.

    **Caminho**:`/data/us-income`

    **Formato de arquivo**: CSV

    > [!NOTE]
    > Este artigo pressupõe que você tenha acesso a um repositório de armazenamento registrado para o espaço de trabalho atual do Azure Machine Learning. Para obter instruções sobre como configurar um repositório de armazenamento, consulte [conectar-se aos serviços de armazenamento do Azure](how-to-access-data.md#azure-machine-learning-studio).

    Se você não tiver um repositório de armazenamento, poderá criar um agora. Para fins de exemplo, este artigo salvará os conjuntos de valores na conta de armazenamento de BLOBs padrão associada ao espaço de trabalho. Ele salvará os conjuntos de itens no `azureml` contêiner em uma nova pasta chamada `data`.

1.  Selecione o módulo **exportar dados** conectado à porta mais *à direita*do módulo **dividir dados** .

1. No painel detalhes do módulo à direita da tela, defina as seguintes opções:
    
    **Tipo de repositório de armazenamento**: armazenamento de BLOBs do Azure

    **Repositório de armazenamento**: selecione o mesmo repositório de armazenamento que está acima

    **Caminho**:`/data/non-us-income`

    **Formato de arquivo**: CSV

1. Confirme se o módulo **exportar dados** conectado à porta à esquerda dos **dados divididos** tem o **caminho** `/data/us-income`.

1. Confirme se o módulo **exportar dados** conectado à porta à direita tem o **caminho** `/data/non-us-income`.

    O pipeline e as configurações devem ter a seguinte aparência:
    
    ![Captura de tela mostrando como configurar os módulos exportar dados](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Enviar a execução

Agora que o pipeline está configurado para dividir e exportar os dados, envie uma execução de pipeline.

1. Na parte superior da tela, selecione **Enviar**.

1. Na caixa de diálogo **configurar execução de pipeline** , selecione **criar novo** para criar um experimento.

    Os experimentos agrupam logicamente as execuções de pipeline relacionadas. Se você executar esse pipeline no futuro, deverá usar o mesmo experimento para fins de log e acompanhamento.

1. Forneça um nome de teste descritivo como "split-censo-data".

1. Selecione **Enviar**.

## <a name="view-results"></a>Exibir os resultados

Depois que o pipeline for concluído, você poderá exibir os resultados navegando até o armazenamento de BLOBs na portal do Azure. Você também pode exibir os resultados intermediários do módulo **dividir dados** para confirmar que seus dados foram divididos corretamente.

1. Selecione o módulo **Dividir dados**.

1. No painel de detalhes do módulo à direita da tela, selecione **Saídas + logs**. 

1. Selecione o ícone de ![visualização Visualizar](media/how-to-designer-transform-data/visualize-icon.png) ícone ao lado de **resultados dataSet1**. 

1. Verifique se a coluna "Native-Country" contém apenas o valor "United-States".

1. Selecione o ícone de ![visualização Visualizar](media/how-to-designer-transform-data/visualize-icon.png) ícone ao lado de **resultados dataset2**. 

1. Verifique se a coluna "Native-Country" não contém o valor "United-States".

## <a name="clean-up-resources"></a>Limpar os recursos

Ignore esta seção se quiser continuar com a parte 2 deste "como" para readaptar [modelos com o designer de Azure Machine Learning](how-to-retrain-designer.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como transformar um conjunto de um DataSet e salvá-lo em um repositório de armazenamento registrado.

Continue na próxima parte desta série de instruções com a [readaptação de modelos com Azure Machine Learning designer](how-to-retrain-designer.md) para usar seus conjuntos de valores transformados e parâmetros de pipeline para treinar modelos de aprendizado de máquina.
