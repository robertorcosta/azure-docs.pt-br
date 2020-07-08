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
ms.date: 05/26/2020
ms.openlocfilehash: db0e2f90ee45d4e1c0173cbc037084793a66e149
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84449980"
---
# <a name="densenet"></a>DenseNet

Este artigo descreve como usar o módulo **DenseNet** no designer de Azure Machine Learning (versão prévia) para criar um modelo de classificação de imagem usando o algoritmo DenseNet.  

Esse algoritmo de classificação é um método de aprendizado supervisionado e requer um conjunto de informações rotulado. Consulte [converter em](convert-to-image-directory.md) módulo de diretório de imagem para obter mais instruções sobre como obter um diretório de imagem rotulado. Você pode treinar o modelo fornecendo o modelo e o diretório de imagem rotulado como entradas para [treinar o modelo Pytorch](train-pytorch-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada usando o [modelo de imagem de Pontuação](score-image-model.md).

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
| Nome do modelo       | Qualquer   | Mode    | densenet201 | Nome de uma determinada estrutura densenet     |
| Pré-treinados       | Qualquer   | Booliano | verdadeiro        | Se deve ser usado um modelo previamente treinado em ImageNet |
| Com eficiência de memória | Qualquer   | Boolean | Falso       | Se deseja usar o ponto de verificação, o que é muito mais eficiente na memória, mas mais lento |

###  <a name="output"></a>Saída  

| Nome            | Tipo                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Modelo não treinado | UntrainedModelDirectory | Um modelo de densenet não treinado que pode ser conectado ao modelo Train Pytorch. |

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
