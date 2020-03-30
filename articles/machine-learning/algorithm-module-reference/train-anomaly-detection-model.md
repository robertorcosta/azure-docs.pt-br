---
title: 'Modelo de detecção de anomalia do trem: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Train Anomaly Detection Model para criar um modelo de detecção de anomalias treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502965"
---
# <a name="train-anomaly-detection-model"></a>Modelo de detecção de anomalias de treinamento

Este artigo descreve como usar o módulo **Train Anomaly Detection Model** no Azure Machine Learning designer (preview) para criar um modelo de detecção de anomalias treinado.

O módulo toma como entrada um conjunto de parâmetros de modelo para o modelo de detecção de anomalias e um conjunto de dados não rotulado. Ele retorna um modelo de detecção de anomalias treinado, juntamente com um conjunto de rótulos para os dados de treinamento.  

Para obter mais informações sobre os algoritmos de detecção de anomalias fornecidos no designer, consulte estes tópicos: 

+ [Detecção de anomalia baseada em PCA](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Como configurar o modelo de detecção de anomalias de trem 

1.  Adicione o módulo **Modelo de Detecção de Anomalia de Trem** ao seu pipeline no designer. Você pode encontrar este módulo na categoria **Detecção de Anomalias.**

2. Conecte um dos módulos projetados para detecção de anomalias, como a [detecção de anomalias baseadas em PCA](pca-based-anomaly-detection.md)

    Outros tipos de modelos não são suportados; ao executar o pipeline você terá o erro: Todos os modelos devem ter o mesmo tipo de aprendiz.  

3.  Configure o módulo de detecção de anomalias escolhendo a coluna de rótulos e definindo outros parâmetros específicos para o algoritmo.  

4.  Anexar um conjunto de dados de treinamento à entrada à direita do Modelo de **Detecção de Anomalias**de Trem .  

5.  Envie o oleoduto.  

## <a name="results"></a>Resultados

Após o treinamento ser concluído:

+ Para visualizar os parâmetros do modelo, clique com o botão direito do mouse no módulo e selecione **Visualizar**. 

+ Para criar previsões, use [Score Model](score-model.md) com novos dados de entrada.

+ Para salvar um instantâneo do modelo treinado, selecione o módulo e clique no ícone **do conjunto de dados Registrar** na guia **Saídas+logs** no painel direito.   

 
## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

Consulte [Exceções e códigos de erro para o designer (visualização)](designer-error-codes.md) para obter uma lista de erros específicos dos módulos de designer.
'