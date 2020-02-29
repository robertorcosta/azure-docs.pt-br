---
title: 'Modelo de detecção de anomalias de treinamento: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo treinar modelo de detecção de anomalias para criar um modelo de detecção de anomalias treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 55c5b89eb23581543d57187ee4d7899d97eafe66
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925950"
---
# <a name="train-anomaly-detection-model"></a>Modelo de detecção de anomalias de treinamento

Este artigo descreve como usar o módulo **treinar modelo de detecção de anomalias** no designer de Azure Machine Learning (versão prévia) para criar um modelo de detecção de anomalias treinado.

O módulo usa como entrada um conjunto de parâmetros de modelo para o modelo de detecção de anomalias e um conjunto de dados sem rótulo. Ele retorna um modelo de detecção de anomalias treinado, junto com um conjunto de rótulos para os dados de treinamento.  

Para obter mais informações sobre os algoritmos de detecção de anomalias fornecidos no designer, consulte estes tópicos: 

+ [Detecção de anomalias baseada em PCA](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Como configurar o modelo de detecção de anomalias de treinamento 

1.  Adicione o módulo **treinar modelo de detecção de anomalias** ao seu pipeline no designer. Você pode encontrar esse módulo na categoria de **detecção de anomalias** .

2. Conecte um dos módulos criados para detecção de anomalias, como a [detecção de anomalias baseada em PCA](pca-based-anomaly-detection.md)

    Não há suporte para outros tipos de modelos; ao executar o pipeline, você receberá o erro: todos os modelos devem ter o mesmo tipo de aprendiz.  

3.  Configure o módulo detecção de anomalias escolhendo a coluna rótulo e definindo outros parâmetros específicos para o algoritmo.  

4.  Anexe um conjunto de dados de treinamento à entrada do lado direito do **modelo de detecção de anomalias**de treinamento.  

5.  Execute o pipeline.  

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para exibir os parâmetros do modelo, clique com o botão direito do mouse no módulo e selecione **Visualizar**. 

+ Para criar previsões, use o [modelo de Pontuação](score-model.md) com novos dados de entrada.

+ Para salvar um instantâneo do modelo treinado, selecione o módulo e clique no ícone **registrar conjunto de registros** na guia **saídas + logs** no painel direito.   

 
## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

Consulte [exceções e códigos de erro para o designer (versão prévia)](designer-error-codes.md) para obter uma lista de erros específicos para os módulos do designer.
