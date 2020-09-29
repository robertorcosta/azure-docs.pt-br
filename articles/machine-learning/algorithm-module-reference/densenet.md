---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Saiba como criar um modelo de classificação de imagem usando o algoritmo densenet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: d64933f7b2c8ebc8597b93cbd16b34158f936f96
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450099"
---
# <a name="densenet"></a>DenseNet

Este artigo descreve como usar o módulo **DenseNet** no designer de Azure Machine Learning para criar um modelo de classificação de imagem usando o algoritmo DenseNet.  

Esse algoritmo de classificação é um método de aprendizado supervisionado e requer um diretório de imagem rotulado. 

> [!NOTE]
> Este módulo não dá suporte a conjuntos de dados rotulados gerados a partir de *Rótulos de dado* no estúdio, mas só dá suporte ao diretório de imagem rotulado gerado do módulo [converter para diretório de imagem](convert-to-image-directory.md) . 

Você pode treinar o modelo fornecendo o modelo e o diretório de imagem rotulado como entradas para [treinar o modelo Pytorch](train-pytorch-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada usando o [modelo de imagem de Pontuação](score-image-model.md).

### <a name="more-about-densenet"></a>Mais sobre DenseNet

Consulte [redes de convolução com conexão densa](https://arxiv.org/abs/1608.06993) para obter mais detalhes.

## <a name="how-to-configure-densenet"></a>Como configurar o DenseNet

1.  Adicione o módulo **DenseNet** ao seu pipeline no designer.  

2.  Para **nome do modelo**, especifique o nome de uma determinada estrutura densenet e você pode selecionar entre densenet com suporte: ' densenet121 ', ' densenet161 ', ' densenet169 ', ' densenet201 '.

3.  Para pré- **treinado**, especifique se deseja usar um modelo previamente treinado em ImageNet. Se selecionado, você pode ajustar o modelo com base no modelo pretreinado selecionado; Se estiver desmarcada, você poderá treinar do zero.

4.  Para a **eficiência da memória**, especifique se deseja usar o ponto de verificação, o que é muito mais eficiente na memória, mas mais lento. Consulte https://arxiv.org/pdf/1707.06990.pdf para obter mais informações.

5.  Conecte a saída do módulo **DenseNet** módulo do conjunto de módulos de imagem, treinamento e validação ao [modelo Train Pytorch](train-pytorch-model.md). 

6. Enviar o pipeline.


## <a name="results"></a>Resultados

Após a execução do pipeline ser concluída, para usar o modelo de pontuação, conecte o [modelo Train Pytorch](train-pytorch-model.md) ao [modelo de imagem de Pontuação](score-image-model.md)para prever valores para novos exemplos de entrada.

## <a name="technical-notes"></a>Observações técnicas  

###  <a name="module-parameters"></a>Parâmetros do módulo  

| Nome             | Intervalo | Type    | Padrão     | Descrição                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Nome do modelo       | Qualquer   | Modo    | densenet201 | Nome de uma determinada estrutura densenet     |
| Pré-treinados       | Qualquer   | Booliano | verdadeiro        | Se deve ser usado um modelo previamente treinado em ImageNet |
| Com eficiência de memória | Qualquer   | Booliano | Falso       | Se deseja usar o ponto de verificação, o que é muito mais eficiente na memória, mas mais lento |

###  <a name="output"></a>Saída  

| Nome            | Tipo                    | Descrição                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Modelo não treinado | UntrainedModelDirectory | Um modelo de densenet não treinado que pode ser conectado ao modelo Train Pytorch. |

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
