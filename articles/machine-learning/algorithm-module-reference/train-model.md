---
title: 'Modelo de trem: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo **Modelo de Trem** no Azure Machine Learning para treinar um modelo de classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0a9728e05aee27e74054a77e2c9be7dc08968207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455852"
---
# <a name="train-model-module"></a>Módulo Treinar Modelo

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para treinar um modelo de classificação ou regressão. O treinamento ocorre depois de definir um modelo e definir seus parâmetros e requer dados marcados. Você também pode usar **o Train Model** para retreinar um modelo existente com novos dados. 

## <a name="how-the-training-process-works"></a>Como funciona o processo de treinamento

No Azure Machine Learning, criar e usar um modelo de aprendizado de máquina é tipicamente um processo de três etapas. 

1. Você configura um modelo, escolhendo um determinado tipo de algoritmo, e definindo seus parâmetros ou hiperparâmetros. Escolha qualquer um dos seguintes tipos de modelo: 

    + **Modelos de classificação,** baseados em redes neurais, árvores de decisão e florestas de decisão, e outros algoritmos.
    + **Modelos de regressão,** que podem incluir regressão linear padrão, ou que usam outros algoritmos, incluindo redes neurais e regressão bayesiana.  

2. Forneça um conjunto de dados que é rotulado e tem dados compatíveis com o algoritmo. Conecte os dados e o modelo ao **Modelo de Trem**.

    O que o treinamento produz é um formato binário específico, o iLearner, que encapsula os padrões estatísticos aprendidos com os dados. Não é possível modificar ou ler diretamente este formato; no entanto, outros módulos podem usar este modelo treinado. 
    
    Você também pode visualizar propriedades do modelo. Para obter mais informações, consulte a seção Resultados.

3. Após a conclusão do treinamento, use o modelo treinado com um dos módulos de [pontuação,](./score-model.md)para fazer previsões sobre novos dados.

## <a name="how-to-use-train-model"></a>Como usar o Modelo de Trem 
  
1.  No Azure Machine Learning, configure um modelo de classificação ou modelo de regressão.
    
2. Adicione o módulo **Modelo de Trem** ao pipeline.  Você pode encontrar este módulo na categoria **Machine Learning.** ExpandA **o Trem**e arraste o módulo **Train Model** para o seu pipeline.
  
3.  Na entrada esquerda, conecte o modo destreinado. Anexar o conjunto de dados de treinamento à entrada à mão direita do Modelo de **Trem**.

    O conjunto de dados de treinamento deve conter uma coluna de etiqueta. Todas as filas sem rótulos são ignoradas.
  
4.  Para **a coluna Rótulo,** clique em **Editar coluna** no painel direito do módulo e escolha uma única coluna que contenha resultados que o modelo possa usar para treinamento.
  
    - Para problemas de classificação, a coluna de etiqueta deve conter valores **categóricos** ou valores **discretos.** Alguns exemplos podem ser uma classificação sim/não, um código de classificação de doença ou nome, ou um grupo de renda.  Se você escolher uma coluna não categórica, o módulo retornará um erro durante o treinamento.
  
    -   Para problemas de regressão, a coluna de rótulo deve conter dados **numéricos** que representam a variável resposta. Idealmente, os dados numéricos representam uma escala contínua. 
    
    Exemplos podem ser uma pontuação de risco de crédito, o tempo projetado para falhar em um disco rígido ou o número previsto de chamadas para um call center em um determinado dia ou hora.  Se você não escolher uma coluna numérica, você pode obter um erro.
  
    -   Se você não especificar qual coluna de rótulo usar, o Aprendizado de Máquina do Azure tenta inferir qual é a coluna de rótulo apropriada, usando os metadados do conjunto de dados. Se ele escolher a coluna errada, use o seletor de coluna para corrigi-la.
  
    > [!TIP] 
    > Se você tiver problemas para usar o Seletor de colunas, consulte o artigo [Selecionar colunas no Conjunto de dados](./select-columns-in-dataset.md) para obter dicas. Descreve alguns cenários e dicas comuns para usar as opções **COM REGRAS** e **PELO NOME.**
  
5.  Envie o oleoduto. Se você tem um monte de dados, isso pode demorar um pouco.

## <a name="results"></a><a name="bkmk_results"></a> Resultados

Depois que o modelo é treinado:


+ Para usar o modelo em outros pipelines, selecione o módulo e selecione o ícone **do conjunto de dados Registrar** na guia **Saídas** no painel direito. Você pode acessar modelos salvos na paleta de módulos em **Conjuntos de dados**.

+ Para usar o modelo na previsão de novos valores, conecte-o ao módulo [Score Model,](./score-model.md) juntamente com novos dados de entrada.


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 